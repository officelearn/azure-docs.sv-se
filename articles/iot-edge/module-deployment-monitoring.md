---
title: Distribuera moduler för Azure IoT kant | Microsoft Docs
description: Lär dig mer om hur moduler distribueras till enheter
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ffd3a8e6bde7310f6bdbed0e0f87419c73fcd6fc
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>Förstå IoT kant distributioner för enstaka enheter eller i skala - förhandsgranskning

Azure IoT-gränsenheterna följer en [enhetslivscykeln] [ lnk-lifecycle] som liknar andra typer av IoT-enheter:

1. IoT-gränsenheterna etableras, vilket omfattar imaging en enhet med ett operativsystem och installera den [IoT kant runtime][lnk-runtime].
1. Enheterna som är konfigurerade för att köras [IoT kant moduler][lnk-modules], och sedan övervakas för hälsotillstånd. 
1. Slutligen dras enheter när de ersätts eller föråldrade.  

Azure IoT-Edge tillhandahåller två sätt att konfigurera modulerna som körs på enheter som IoT: en för utveckling och snabb iterationer på en enskild enhet (som du använde i Azure IoT kant självstudier) och en för att hantera stora fordonsflottor kant för IoT-enheter. Båda dessa metoder är tillgängliga i Azure-portalen och genom programmering.

Den här artikeln fokuserar på konfiguration och övervakning faser för flottor av enheterna, vilket gemensamt kallas för IoT kant-distributioner. Övergripande stegen för distributionen är följande:   

1. En operator definierar en distribution som beskriver en uppsättning moduler samt målenheter. Varje distribution har en distributionsmanifestet som visar den här informationen. 
1. Tjänsten IoT-hubb kommunicerar med alla målriktade enheter att konfigurera dem med de önskade modulerna. 
1. IoT-hubb-tjänsten hämtar status från IoT-gränsenheterna och hämtar de för operatorn för att övervaka.  En operatör kan exempelvis se när en insticksenhet har inte konfigurerats korrekt eller om en modul misslyckas under körning. 
1. När som helst konfigureras nya kant för IoT-enheter som uppfyller villkor som målobjekt för distributionen. Till exempel konfigurerar en distribution som riktar sig till alla IoT-gränsenheterna i staten Washington automatiskt en ny IoT Edge-enhet när den är etablerad och lagts till i staten Washington enhetsgrupp. 
 
Den här artikeln beskriver hur varje komponent som ingår i konfigurering och övervakning av en distribution. En genomgång av hur du skapar och uppdaterar en distribution finns [distribuera och övervaka IoT kant moduler i skala][lnk-howto].

## <a name="deployment"></a>Distribution

En distribution tilldelar IoT kant modulen bilder att köras som instanser på en riktad uppsättning kant för IoT-enheter. Det fungerar genom att konfigurera en IoT-Edge distributionsmanifestet om du vill inkludera en lista med moduler med motsvarande initieringsparametrar. En distribution kan tilldelas till en enhet (vanligtvis baserat på enhets-Id) eller till en grupp av enheter (baserat på taggar). När en IoT-enhet tar emot en distributionsmanifestet, hämtar och installerar modulen behållaren bilder från respektive behållaren databaser, och konfigurerar den dem i enlighet med detta. När du har skapat en distribution kan operatör övervaka Distributionsstatus om målenheter är korrekt konfigurerad.   

Enheter måste etableras som IoT-gränsenheterna konfigureras med en distribution. Följande är förutsättningar och ingår inte i distributionen:
* Det grundläggande operativsystemet
* Docker 
* Etablering av körningsmiljön IoT kant 

### <a name="deployment-manifest"></a>Distributionsmanifestet

En distributionsmanifestet är ett JSON-dokument som beskriver modulerna som ska konfigureras på målenheter i IoT kant. Den innehåller configuration metadata för alla moduler, inklusive obligatoriska moduler (särskilt IoT kant agenten och kant för IoT-hubb).  

Av konfigurationsmetadata för varje modul omfattar: 
* Version 
* Typ 
* Status (t ex igång eller stoppad) 
* Starta om principen 
* Bild- och databasen 
* Vägar för data som indata och utdata 

### <a name="target-condition"></a>Målvillkoren

Målvillkoren utvärderas kontinuerligt för att inkludera nya enheter som uppfyller kraven eller ta bort enheter som inte längre gör via livslängden för distributionen. Distributionen ska återaktiveras om tjänsten identifierar ändringar mål villkor. Exempelvis kan du har en distribution A som har ett mål villkoret tags.environment = 'prod'. När du startar distributionen, finns det 10 produktprenumeration enheter. Modulerna som är installerat i dessa 10 enheter. IoT kant agentens Status visas som 10 Totalt antal enheter, 10 har svar, 0 fel svar och 0 väntar på svar. Nu du lägga till 5 flera enheter med tags.environment = 'prod'. Tjänsten identifierar ändringen och Agentstatus för IoT-Edge blir 15 totalt antal enheter, 10 har svar, 0 fel svar och 5 väntande svar när den försöker distribuera till fem nya enheter.

Använd booleskt villkor på enheten twins taggar eller deviceId för att välja målenheter. Om du vill använda villkoret med taggar du behöver lägga till ”taggar”:{} avsnitt i enheten dubbla under samma nivå som egenskaper. [Mer information om taggar i enheten dubbla](../iot-hub/iot-hub-devguide-device-twins.md)

Exempel på mål villkor:
* deviceId = 'linuxprod1'
* Tags.Environment = 'prod'
* Tags.Environment = prod och tags.location = 'westus'
* Tags.Environment = prod eller tags.location = 'westus'
* Tags.operator = ”John' och tags.environment = prod inte deviceId = 'linuxprod1'

Här följer några avgränsar när du skapar ett villkor för mål:

* Du kan bara bygga ett mål villkor med hjälp av taggar eller deviceId i enheten dubbla.
* Dubbla citattecken tillåts inte i någon del av målvillkoren. Använd enkla citattecken.
* Enkla citattecken representerar värden för målvillkoren. Du måste därför escape enkla citattecken med ett annat enkelt citattecken om det är en del av namnet på en enhet. Till exempel målvillkoren för: operator'sDevice skulle behöva skrivas som deviceId =' operatorn '' sDevice'.
* Siffror, bokstäver och följande tecken är tillåtna i målet villkoret values:-:.+%_#*? (),=@;$

### <a name="priority"></a>Prioritet

En prioritet definierar om en distribution ska tillämpas på en målenhet i förhållande till andra distributioner. En prioritet för distribution är ett positivt heltal med större siffra anger högre prioritet. Om en IoT-insticksenhet utsätts för mer än en distribution, gäller distributionen med högst prioritet.  Distributioner med lägre prioritet tillämpas inte heller kombineras de.  Om en enhet är angiven som mål med två eller fler distributioner med samma prioritet, gäller den senast skapade distributionen (bestäms av tidsstämpeln vid skapande).

### <a name="labels"></a>Etiketter 

Etiketter är sträng nyckel/värde-par som du kan använda för att filtrera och gruppera distributioner. En distribution kan ha flera etiketter. Etiketter är valfritt och utan att påverka faktiska konfigurationen av IoT-gränsenheterna. 

### <a name="deployment-status"></a>Status för distribution

En distribution kan övervakas för att avgöra om den har tillämpats för alla aktuella kant för IoT-enheter.  En riktad Edge-enhet visas i en eller flera av följande statuskategorier: 
* **Målet** visar IoT-Edge enheter som matchar distributionen målobjekt för villkoret.
* **Faktiska** visar riktade IoT kanten enheter som inte omfattas av en annan distribution av högre prioritet.
* **Felfri** visar IoT-Edge enheter som har rapporterat tillbaka till tjänsten att modulerna som har distribuerats korrekt. 
* **Feltillstånd** visar IoT-Edge enheter har rapporterat tillbaka till tjänsten som en eller moduler har inte har distribuerats. Om du vill undersöka felet, fjärransluta till som enheterna och visa loggfilerna.
* **Okänd** visar IoT-Edge enheter som inte rapporterade status som rör distributionen. Om du vill undersöka, visa service info och log-filer.

## <a name="phased-rollout"></a>Stegvis distribution 

En stegvis distribution är en övergripande process genom vilken operatör distribuerar ändringar till en breddat uppsättning kant för IoT-enheter. Målet är att göra ändringar gradvis för att minska risken för att göra wide skala bryta ändringar.  

En stegvis distribution utförs i följande faser och steg: 
1. Upprätta en testmiljö för IoT-gränsenheterna genom att etablera dem och ange en enhet dubbla tagg som `tag.environment='test'`. Testmiljön ska spegla produktionsmiljön som distributionen kommer så småningom mål. 
1. Skapa en distribution med önskad moduler och konfigurationer. Målobjekt villkoret ska rikta testet Enhetsmiljö IoT kant.   
1. Validera den nya modulkonfigurationen i testmiljön.
1. Uppdatera distributionen för att inkludera en delmängd av produktion IoT-gränsenheterna genom att lägga till en ny tagg villkoret målobjekt. Kontrollera också att prioriteten för distributionen är högre än andra distributioner riktat till enheterna 
1. Kontrollera att distributionen har utförts på den aktuella IoT-enheter genom att granska distributionsstatusen.
1. Uppdatera distributionen för att rikta alla återstående produktion kant för IoT-enheter.

## <a name="rollback"></a>Återställning

Distributioner kan återställas vid fel eller felaktig konfiguration.  Eftersom en distribution anger absoluta modulkonfigurationen för en IoT-enhet är måste en ytterligare distribution också vara mål på samma enhet med lägre prioritet även om målet är att ta bort alla moduler.  

Utföra återställningar i följande ordning: 
1. Bekräfta att en andra distribution också är inriktad på samma eller enhetsuppsättning. Om målet för återställningen är att ta bort alla moduler måste bör den andra distributionen inte innehålla några moduler. 
1. Ändra eller ta bort villkoret måluttryck av distribution du vill återställa så att enheterna som inte längre uppfyller villkoret målobjekt.
1. Kontrollera att återställningen lyckades genom att granska distributionsstatusen.
   * Föras tillbaka distributionen bör inte längre visa status för enheter som återställdes.
   * Den andra distributionen innehåller bör nu Distributionsstatus för de enheter som återställdes.


## <a name="next-steps"></a>Nästa steg

* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT kant moduler i skala][lnk-howto].
* Mer information om andra IoT kant begrepp som den [IoT kant runtime] [ lnk-runtime] och [IoT kant moduler][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md