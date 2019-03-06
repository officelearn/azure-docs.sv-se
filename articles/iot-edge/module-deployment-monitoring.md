---
title: Automatisk distribution för enhetsgrupper – Azure IoT Edge | Microsoft Docs
description: Använda automatisk distributioner i Azure IoT Edge för att hantera grupper av enheter baserat på delade taggar
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 72238db8322132f42e14656c106b82d8604af21f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435107"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Förstå IoT Edge automatiska distributioner för enskilda enheter eller i stor skala

Azure IoT Edge-enheter följer en [enhetslivscykeln](../iot-hub/iot-hub-device-management-overview.md) som påminner om andra typer av IoT-enheter:

1. Etablera nya IoT Edge-enheter genom att imaging en enhet med ett operativsystem och installera den [IoT Edge-körningen](iot-edge-runtime.md).
2. Konfigurera enheter för att köra [IoT Edge-moduler](iot-edge-modules.md), och sedan övervaka deras hälsotillstånd. 
3. Slutligen, dra tillbaka enheter när de ersätts eller föråldrade.  

Azure IoT Edge gör det två sätt att konfigurera vilka moduler som ska köras på IoT Edge-enheter: en för utveckling och snabba iterationer på en enskild enhet (används av den här metoden i Azure IoT Edge [självstudier](tutorial-deploy-function.md)), och en för att hantera stora fleets av IoT Edge-enheter. Båda dessa metoder är tillgängliga i Azure-portalen och genom programmering. För att skicka meddelanden till grupper eller ett stort antal enheter, kan du ange vilka enheter som du vill distribuera dina moduler med [taggar](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) i enhetstvillingen. Följande steg tala om en distribution till en enhetsgrupp i delstaten Washington identifieras via egenskapen taggar. 

Den här artikeln fokuserar på konfigurationen och övervakning faser för fjärranläggning av enheter, gemensamt kallas för automatisk IoT Edge-distributioner. Övergripande distributionsstegen är följande: 

1. En operatör definierar en distribution som beskriver en uppsättning moduler samt målenheter. Varje distribution har ett distribution-manifest som visar den här informationen. 
2. IoT Hub-tjänsten kommunicerar med alla målriktade enheter att konfigurera dem med de önskade modulerna. 
3. IoT Hub-tjänsten hämtar status från IoT Edge-enheter och gör dem tillgängliga för operatorn.  En operatör kan exempelvis se när en Edge-enhet inte har konfigurerats felfritt eller om en modul misslyckas under körning. 
4. Nya IoT Edge-enheter som uppfyller villkor som Sök mål har konfigurerats för distributionen när som helst. Till exempel konfigurerar en distribution som riktar sig mot alla IoT Edge-enheter i delstaten Washington automatiskt en ny IoT Edge-enhet när den har etablerats och läggas till enhetsgrupp i delstaten Washington. 
 
Den här artikeln beskriver varje komponent som ingår i Konfigurera och övervaka en distribution. En genomgång för att skapa och uppdatera en distribution finns i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Distribution

En automatisk IoT Edge-distribution tilldelar IoT Edge modulen bilder för att köra som instanser på en riktad uppsättning IoT Edge-enheter. Det fungerar genom att konfigurera ett manifest för IoT Edge-distribution med en lista med moduler med motsvarande initieringsparametrar. En distribution kan tilldelas till en enskild enhet (baserat på enhets-ID) eller till en grupp av enheter (baserat på taggar). När en IoT Edge-enhet tar emot ett manifest för distribution, det hämtar och installerar behållaravbildningarna från respektive container-databaser och konfigurerar dem enlighet med detta. När en distribution har skapats kan kan en operatör övervaka distributionsstatusen om målenheterna är korrekt konfigurerad.

IoT Edge-enheter kan konfigureras med en distribution. Följande krav måste vara på enheten innan den kan ta emot distributionen:

* Det grundläggande operativsystemet
* Ett system för behållaren, t.ex. Moby eller Docker
* Etablering av IoT Edge-körningen 

### <a name="deployment-manifest"></a>Distributionsmanifest

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler som ska konfigureras på de aktuella IoT Edge-enheterna. Den innehåller konfigurationsmetadata för alla moduler, inklusive de nödvändiga systemmoduler (särskilt IoT Edge-agenten och IoT Edge hub).  

Av konfigurationsmetadata för varje modul innehåller: 

* Version 
* Typ 
* Status (till exempel igång eller stoppad) 
* Starta om princip 
* Bild- och container registry
* Vägar för data som indata och utdata 

Om modulen avbildningen lagras i ett privat behållarregister, har IoT Edge-agenten autentiseringsuppgifter för registret. 

### <a name="target-condition"></a>Målvillkor

Målvillkoret utvärderas kontinuerligt under hela livslängden för distributionen. Alla nya enheter som uppfyller kraven ingår och alla befintliga enheter som inte längre gör tas bort. Distributionen återaktiveras om tjänsten identifierar ändringar target villkor. 

Exempelvis kan du har en distribution A med ett mål villkor tags.environment = 'prod'. När du sätta igång distributionen finns 10 produktionsenheter. Modulerna som har installerats i dessa 10 enheter. IoT Edge-Agentstatus visas som 10 Totalt antal enheter, 10 lyckades, 0 misslyckades och 0 väntar. Nu lägger du till fem fler enheter med tags.environment = 'prod'. Tjänsten identifierar ändringen och IoT Edge-Agentstatus blir 15 totalt antal enheter, 10 lyckades, 0 misslyckades och 5 väntar vid försök att distribuera till fem nya enheter.

Använd valfritt booleskt villkor på device twins taggar eller deviceId för att välja målenheter. Om du vill använda villkoret med taggar kan du behöva lägga till ”taggar”:{} avsnittet i enhetstvillingen under samma nivå som egenskaper. [Mer information om taggar i enhetstvilling](../iot-hub/iot-hub-devguide-device-twins.md)

Exempel på mål-villkor:

* deviceId = 'linuxprod1'
* Tags.Environment = 'prod'
* Tags.Environment = prod och tags.location = 'westus'
* Tags.Environment = prod eller tags.location = 'westus'
* Tags.operator = ”John” och tags.environment = prod inte deviceId = 'linuxprod1'

Här följer några avgränsar när du skapar en Målvillkor:

* Du kan bara skapa en Målvillkor med hjälp av taggar eller deviceId i enhetstvillingen.
* Dubbla citattecken tillåts inte i någon del av målvillkoret. Använd enkla citattecken.
* Enkla citattecken representerar värden för målvillkoret. Du måste därför escape enkelt citattecken med ett annat enkelt citattecken om det är en del av namnet på enheten. Till exempel att fokusera på en enhet med namnet `operator'sDevice`, skriva `deviceId='operator''sDevice'`.
* Siffror, bokstäver och följande tecken är tillåtna i villkoret målvärden: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioritet

En prioritet definierar om en distribution ska tillämpas på en målenhet i förhållande till andra distributioner. Prioritetsvärdet distribution är ett positivt heltal med större siffra anger högre prioritet. Om en IoT Edge-enhet är målet för fler än en distribution, gäller distributionen med högst prioritet.  Distributioner med lägre prioritet tillämpas inte, och inte heller slås samman de.  Om en enhet har riktats med två eller fler distributioner med samma prioritet gäller senast skapade distributionen (bestäms av tidsstämpeln vid skapande).

### <a name="labels"></a>Etiketter 

Etiketter är sträng nyckel/värde-par som du kan använda för att filtrera och gruppera distributioner. En distribution kan ha flera etiketter. Etiketter är valfria och göra utan att påverka den faktiska konfigurationen av IoT Edge-enheter. 

### <a name="deployment-status"></a>Status för distribution

En distribution kan övervakas för att avgöra om det har tillämpats för alla aktuella IoT Edge-enheten.  En riktad Edge-enhet visas i en eller flera av följande statuskategorier: 

* **Target** visar IoT Edge-enheter som matchar den distribution som riktar in sig på villkoret.
* **Faktiska** visar riktade IoT Edge-enheter som inte omfattas av en annan distribution av högre prioritet.
* **Felfria** visar IoT Edge-enheter som har rapporterat tillbaka till tjänsten att modulerna som har distribuerats korrekt. 
* **Defekta** visar IoT Edge enheter har rapporterat tillbaka till tjänsten att en eller moduler har inte distribuerats korrekt. För att undersöka felet, fjärransluta till dessa enheter och visa loggfilerna.
* **Okänd** visar IoT Edge-enheter som inte rapporterades som vilken status som om den här distributionen. För att undersöka, visa service information och log-filer.

## <a name="phased-rollout"></a>Stegvis distribution 

En stegvis distribution är ett övergripande processen där en operatör distribuerar ändringar till en breddat uppsättning IoT Edge-enheter. Målet är att göra ändringar gradvis för att minska risken för att göra många skala större ändringar.  

En stegvis distribution körs i följande faser och steg: 

1. Upprätta en testmiljö för IoT Edge-enheter genom att etablera dem och ställa in en enhetstagg twin som `tag.environment='test'`. Testmiljön ska spegla produktionsmiljön som så småningom rikta in distributionen. 
2. Skapa en distribution, inklusive de önskade moduler och konfigurationer. Sök mål villkoret ska rikta testet miljö för IoT Edge-enhet.   
3. Verifiera modulkonfigurationen för nya i testmiljön.
4. Uppdatera distribution för att inkludera en delmängd av produktion IoT Edge-enheter genom att lägga till en ny tagg målobjekt villkoret. Kontrollera också att prioriteten för distributionen är högre än andra distributioner riktat till enheterna 
5. Kontrollera att distributionen har utförts på den aktuella IoT-enheter genom att granska distributionsstatusen.
6. Uppdatera distributionen för att rikta in alla återstående produktion IoT Edge-enheter.

## <a name="rollback"></a>Återställning

Distributioner kan återkallas om du får fel eller felaktiga konfigurationer.  Eftersom en distribution anger absolut modulkonfigurationen för en IoT Edge-enhet är måste en ytterligare distribution också vara mål på samma enhet med lägre prioritet även om målet är att ta bort alla moduler.  

Utföra återställningar i följande ordning: 

1. Bekräfta att en andra distribution också är inriktad på samma enhetsuppsättning. Om målet för återställningen är att ta bort alla moduler måste får den andra distributionen inte innehålla alla moduler. 
2. Ändra eller ta bort villkorsuttrycket mål för distributionen som du vill återställa så att enheterna som inte längre uppfyller villkoret målobjekt.
3. Kontrollera att återställningen har utförts genom att granska distributionsstatusen.
   * Återställas tillbaka distributionen bör inte längre visa status för enheter som har återställts.
   * Den andra distributionen ska nu innehålla Distributionsstatus för de enheter som har återställts.


## <a name="next-steps"></a>Nästa steg

* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
* Mer information om andra IoT Edge-begrepp som den [IoT Edge-körningen](iot-edge-runtime.md) och [IoT Edge-moduler](iot-edge-modules.md).

