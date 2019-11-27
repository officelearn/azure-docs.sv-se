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
ms.openlocfilehash: eb45f2b929c08ce77c83af450726a00dd6af458e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456727"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Förstå IoT Edge automatiska distributioner för enskilda enheter eller i stor skala

Azure IoT Edge enheter följer en [enhets livs cykel](../iot-hub/iot-hub-device-management-overview.md) som liknar andra typer av IoT-enheter:

1. Etablera nya IoT Edge enheter genom att avbildningar en enhet med ett operativ system och installera [IoT Edge runtime](iot-edge-runtime.md).
2. Konfigurera enheterna så att de kör [IoT Edge moduler](iot-edge-modules.md)och övervaka deras hälsa. 
3. Slutligen, dra tillbaka enheter när de ersätts eller föråldrade.  

Azure IoT Edge tillhandahåller två sätt att konfigurera modulerna så att de körs på IoT Edge enheter: en för utveckling och snabba iterationer på en enda enhet (du använde den här metoden i Azure IoT Edge [självstudier](tutorial-deploy-function.md)) och en för att hantera stora flotta av IoT Edge enheter. Båda dessa metoder är tillgängliga i Azure-portalen och genom programmering. För mål grupper eller ett stort antal enheter kan du ange vilka enheter som du vill distribuera dina moduler till med hjälp av [taggar](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) i enheten. Följande steg tala om en distribution till en enhetsgrupp i delstaten Washington identifieras via egenskapen taggar. 

Den här artikeln fokuserar på konfigurationen och övervakning faser för fjärranläggning av enheter, gemensamt kallas för automatisk IoT Edge-distributioner. De övergripande distributions stegen är följande: 

1. En operatör definierar en distribution som beskriver en uppsättning moduler samt målenheter. Varje distribution har ett distributions manifest som motsvarar den här informationen. 
2. IoT Hub-tjänsten kommunicerar med alla målriktade enheter att konfigurera dem med de önskade modulerna. 
3. IoT Hub-tjänsten hämtar status från IoT Edge-enheter och gör dem tillgängliga för operatorn.  En operatör kan till exempel se när en Edge-enhet inte har kon figurer ATS korrekt eller om en modul Miss lyckas under körningen. 
4. Nya IoT Edge-enheter som uppfyller villkor som Sök mål har konfigurerats för distributionen när som helst. En distribution som riktar sig till alla IoT Edge enheter i Washington-tillstånd konfigurerar till exempel automatiskt en ny IoT Edge enhet när den har tillhandahållits och lagts till i enhets gruppen för Washington-tillstånd. 
 
Den här artikeln beskriver varje komponent som ingår i Konfigurera och övervaka en distribution. En genom gång av hur du skapar och uppdaterar en-distribution finns i [distribuera och övervaka IoT Edge moduler i skala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Distribution

En automatisk IoT Edge-distribution tilldelar IoT Edge modulen bilder för att köra som instanser på en riktad uppsättning IoT Edge-enheter. Det fungerar genom att konfigurera ett manifest för IoT Edge-distribution med en lista med moduler med motsvarande initieringsparametrar. En distribution kan tilldelas en enda enhet (baserat på enhets-ID) eller till en grupp av enheter (baserat på taggar). När en IoT Edge enhet tar emot ett distributions manifest laddas och installeras behållar avbildningarna från respektive behållares databaser och konfigurerar dem därefter. När en distribution har skapats kan en operatör övervaka distributions statusen för att se om de riktade enheterna är korrekt konfigurerade.

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

Mål villkoret utvärderas kontinuerligt under hela livs längden för distributionen. Alla nya enheter som uppfyller kraven ingår och alla befintliga enheter som inte längre gör tas bort. Distributionen återaktiveras om tjänsten identifierar ändringar target villkor. 

Exempelvis kan du har en distribution A med ett mål villkor tags.environment = 'prod'. När du sätta igång distributionen finns 10 produktionsenheter. Modulerna som har installerats i dessa 10 enheter. IoT Edge-Agentstatus visas som 10 Totalt antal enheter, 10 lyckades, 0 misslyckades och 0 väntar. Nu lägger du till fem fler enheter med tags.environment = 'prod'. Tjänsten identifierar ändringen och IoT Edge-Agentstatus blir 15 totalt antal enheter, 10 lyckades, 0 misslyckades och 5 väntar vid försök att distribuera till fem nya enheter.

Använd valfritt booleskt villkor på device twins taggar eller deviceId för att välja målenheter. Om du vill använda villkor med taggar måste du lägga till Taggar:{} avsnittet i enheten i dubbla under samma nivå som egenskaper. [Lär dig mer om taggar i enhets dubbla](../iot-hub/iot-hub-devguide-device-twins.md)

Exempel på mål-villkor:

* deviceId = 'linuxprod1'
* Tags.Environment = 'prod'
* Tags.Environment = prod och tags.location = 'westus'
* Tags.Environment = prod eller tags.location = 'westus'
* Tags.operator = ”John” och tags.environment = prod inte deviceId = 'linuxprod1'

Här följer några avgränsar när du skapar en Målvillkor:

* Du kan bara skapa en Målvillkor med hjälp av taggar eller deviceId i enhetstvillingen.
* Dubbla citattecken tillåts inte i någon del av målvillkoret. Använd enkla citattecken.
* Enkla citattecken representerar värden för målvillkoret. Du måste därför escape enkelt citattecken med ett annat enkelt citattecken om det är en del av namnet på enheten. Om du till exempel vill rikta en enhet med namnet `operator'sDevice`skriver du `deviceId='operator''sDevice'`.
* Siffror, bokstäver och följande tecken tillåts i mål villkors värden: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioritet

En prioritet definierar om en distribution ska tillämpas på en målenhet i förhållande till andra distributioner. Prioritetsvärdet distribution är ett positivt heltal med större siffra anger högre prioritet. Om en IoT Edge-enhet är målet för fler än en distribution, gäller distributionen med högst prioritet.  Distributioner med lägre prioritet tillämpas inte och slås inte samman.  Om en enhet är riktad mot två eller flera distributioner med samma prioritet, gäller den senast skapade distributionen (bestäms av tids stämplingen för skapandet).

### <a name="labels"></a>Etiketter 

Etiketter är sträng nyckel/värde-par som du kan använda för att filtrera och gruppera distributioner. En distribution kan ha flera etiketter. Etiketter är valfria och göra utan att påverka den faktiska konfigurationen av IoT Edge-enheter. 

### <a name="deployment-status"></a>Status för distribution

En distribution kan övervakas för att avgöra om det har tillämpats för alla aktuella IoT Edge-enheten.  En riktad gräns enhet visas i en eller flera av följande status kategorier: 

* **Mål** visar de IoT Edge enheter som matchar villkoret för distributions målet.
* **Faktiskt** visar mål IoT Edge enheter som inte är riktade till en annan distribution med högre prioritet.
* **Felfria** visar IoT Edge enheter som har rapporterat tillbaka till tjänsten att modulerna har distribuerats korrekt. 
* **Ohälsosamt** visar IoT Edge enheterna har rapporterat tillbaka till tjänsten att en eller flera moduler inte har distribuerats korrekt. För att undersöka felet, fjärransluta till dessa enheter och visa loggfilerna.
* **Okänd** visar IoT Edge enheter som inte rapporterade någon status för den här distributionen. För att undersöka, visa service information och log-filer.

## <a name="phased-rollout"></a>Stegvis distribution 

En stegvis distribution är ett övergripande processen där en operatör distribuerar ändringar till en breddat uppsättning IoT Edge-enheter. Målet är att göra ändringar gradvis för att minska risken för att göra många skala större ändringar.  

En stegvis distribution körs i följande faser och steg: 

1. Upprätta en test miljö för IoT Edge enheter genom att etablera dem och ställa in en enhets dubbla taggar som `tag.environment='test'`. Test miljön bör spegla produktions miljön som distributionen kommer att ta slut. 
2. Skapa en distribution, inklusive de önskade moduler och konfigurationer. Sök mål villkoret ska rikta testet miljö för IoT Edge-enhet.   
3. Verifiera modulkonfigurationen för nya i testmiljön.
4. Uppdatera distribution för att inkludera en delmängd av produktion IoT Edge-enheter genom att lägga till en ny tagg målobjekt villkoret. Kontrollera också att prioriteten för distributionen är högre än andra distributioner riktat till enheterna 
5. Kontrollera att distributionen har utförts på den aktuella IoT-enheter genom att granska distributionsstatusen.
6. Uppdatera distributionen för att rikta in alla återstående produktion IoT Edge-enheter.

## <a name="rollback"></a>Återtagande

Distributioner kan återkallas om du får fel eller felaktiga konfigurationer.  Eftersom en distribution definierar den absoluta modul konfigurationen för en IoT Edge enhet måste även en ytterligare distribution vara riktad till samma enhet med lägre prioritet även om målet är att ta bort alla moduler.  

Utföra återställningar i följande ordning: 

1. Bekräfta att en andra distribution också är inriktad på samma enhetsuppsättning. Om målet för återställningen är att ta bort alla moduler måste får den andra distributionen inte innehålla alla moduler. 
2. Ändra eller ta bort villkorsuttrycket mål för distributionen som du vill återställa så att enheterna som inte längre uppfyller villkoret målobjekt.
3. Kontrollera att återställningen har utförts genom att granska distributionsstatusen.
   * Återställas tillbaka distributionen bör inte längre visa status för enheter som har återställts.
   * Den andra distributionen ska nu innehålla Distributionsstatus för de enheter som har återställts.


## <a name="next-steps"></a>Nästa steg

* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge moduler i stor skala](how-to-deploy-monitor.md).
* Lär dig mer om andra IoT Edge begrepp som [IoT Edge runtime](iot-edge-runtime.md) -och [IoT Edge-moduler](iot-edge-modules.md).

