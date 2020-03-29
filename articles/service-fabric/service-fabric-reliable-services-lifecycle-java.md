---
title: Livscykelscykeln för Azure Service Fabric Reliable Services
description: Lär dig mer om livscykelhändelserna i ett Azure Service Fabric Reliable Services-program som använder Java för tillståndskänsliga och tillståndslösa tjänster.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639606"
---
# <a name="reliable-services-lifecycle"></a>Reliable Services-livscykel
> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services är en av de programmeringsmodeller som finns i Azure Service Fabric. När du lär dig mer om livscykeln för tillförlitliga tjänster är det viktigast att förstå de grundläggande livscykelhändelserna. Exakt ordning på händelser beror på konfigurationsinformation. 

I allmänhet innehåller livscykeln för tillförlitliga tjänster följande händelser:

* Under start:
  * Tjänsterna är konstruerade.
  * Tjänsterna har möjlighet att konstruera och returnera noll eller fler lyssnare.
  * Alla returnerade lyssnare öppnas för kommunikation med tjänsten.
  * Tjänstens `runAsync` metod anropas, så tjänsten kan utföra tidskrävande eller bakgrundsarbete.
* Under avstängning:
  * Annulleringstoken `runAsync` som skickades till avbryts och lyssnarna stängs.
  * Själva serviceobjektet är avstruerat.

Ordningen på händelserna i Reliable Services kan ändras något beroende på om den tillförlitliga tjänsten är tillståndslös eller tillståndskänslig. 

För tillståndskänsliga tjänster måste du också ta itu med det primära växlingsscenariot. Under den här sekvensen överförs rollen som primär till en annan replik (eller kommer tillbaka) utan att tjänsten stängs av. 

Slutligen måste du tänka på fel eller fel villkor.

## <a name="stateless-service-startup"></a>Start av tillståndslös tjänst
Livscykeln för en tillståndslös tjänst är ganska enkel. Här är ordningen på händelserna:

1. Tjänsten är konstruerad.
2. Dessa händelser inträffar parallellt:
    - `StatelessService.createServiceInstanceListeners()`anropas och alla returnerade lyssnare öppnas. `CommunicationListener.openAsync()`kallas på varje lyssnare.
    - Tjänstens `runAsync` metod (`StatelessService.runAsync()`) anropas.
3. Om det finns något `onOpenAsync` att göra kallas tjänstens egen metod. Specifikt kallas. `StatelessService.onOpenAsync()` Detta är en ovanlig åsidosättning, men den är tillgänglig.

Det är viktigt att notera att det inte finns någon beställning mellan samtalet `runAsync`för att skapa och öppna lyssnarna och samtalet till . Lyssnarna kan öppna `runAsync` innan den startas. På samma `runAsync` sätt kan åberopas innan kommunikationslyssnare är öppna, eller innan de ens har konstruerats. Om någon synkronisering krävs måste den göras av implementeraren. Här är några vanliga lösningar:

* Ibland kan lyssnare inte fungera förrän annan information har skapats eller annat arbete har utförts. För tillståndslösa tjänster kan det arbetet vanligtvis utföras i tjänstens konstruktör. Det kan göras `createServiceInstanceListeners()` under samtalet, eller som en del av byggandet av lyssnaren själv.
* Ibland startar `runAsync` inte koden i förrän lyssnarna är öppna. I detta fall är ytterligare samordning nödvändig. En vanlig lösning är att lägga till en flagga i lyssnarna. Flaggan anger när lyssnarna är klara. Metoden `runAsync` kontrollerar detta innan det verkliga arbetet fortsätter.

## <a name="stateless-service-shutdown"></a>Avstängning av tillståndslös tjänst
När du stänger av en tillståndslös tjänst följs samma mönster, men i omvänd ordning:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare är stängda. `CommunicationListener.closeAsync()`kallas på varje lyssnare.
    - Annulleringstoken `runAsync()` som skickades till avbryts. Om du kontrollerar `isCancelled` egenskapen annulleringstokens egenskap returneras `true`, och om den anropas, genererar tokens `throwIfCancellationRequested` metod en `CancellationException`.
2. När `closeAsync()` den är klar `runAsync()` på varje lyssnare och `StatelessService.onCloseAsync()` även är klar anropas tjänstens metod, om den finns. Återigen är detta inte en vanlig åsidosättning, men det kan användas för att säkert stänga resurser, stoppa bakgrundsbearbetning, avsluta spara externa tillstånd eller stänga befintliga anslutningar.
3. När `StatelessService.onCloseAsync()` serviceobjektet är klart är det avstruerat.

## <a name="stateful-service-startup"></a>Tillståndskänslig tjänststart
Tillståndskänsliga tjänster har ett mönster som liknar statslösa tjänster, med några få ändringar.  Här är ordningen på händelser för att starta en tillståndskänslig tjänst:

1. Tjänsten är konstruerad.
2. `StatefulServiceBase.onOpenAsync()`kallas. Det här anropet åsidosätts inte ofta i tjänsten.
3. Dessa händelser inträffar parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()`åberopas. 
      - Om tjänsten är en primär tjänst öppnas alla returnerade lyssnare. `CommunicationListener.openAsync()`kallas på varje lyssnare.
      - Om tjänsten är en sekundär tjänst öppnas `listenOnSecondary = true` endast lyssnare som är markerade som. Att ha lyssnare som är öppna på sekundärer är mindre vanligt.
    - Om tjänsten för närvarande är en `StatefulServiceBase.runAsync()` primär anropas tjänstens metod.
4. När alla repliklyssare samtal avslutas `openAsync()` och `runAsync()` anropas, `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet åsidosätts inte ofta i tjänsten.

I likhet med statslösa tjänster, i tillståndskänslig tjänst, det finns ingen samordning mellan `runAsync` den ordning i vilken lyssnarna skapas och öppnas och när kallas. Om du behöver samordning, lösningarna är ungefär desamma. Men det finns ytterligare ett fall för tillståndskänslig service. Säg att de samtal som anländer till kommunikationen lyssnare kräver information som förvaras i vissa [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). Eftersom kommunikationslyssnare kan öppna innan tillförlitliga samlingar kan `runAsync` läsas eller skrivbara, och innan start, är viss ytterligare samordning nödvändig. Den enklaste och vanligaste lösningen är att kommunikationslyssnare returnerar en felkod. Klienten använder felkoden för att veta för att försöka igen begäran.

## <a name="stateful-service-shutdown"></a>Tillståndskänslig tjänst avstängning
Precis som tillståndslösa tjänster är livscykelhändelserna under avstängning samma som under starten, men omvända. När en tillståndskänslig tjänst stängs av inträffar följande händelser:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare är stängda. `CommunicationListener.closeAsync()`kallas på varje lyssnare.
    - Annulleringstoken `runAsync()` som skickades till avbryts. Ett anrop till `isCancelled()` annulleringstokens metod returnerar `true`, och om den anropas genererar tokens `throwIfCancellationRequested()` metod en `OperationCanceledException`.
2. När `closeAsync()` du är klar `runAsync()` på varje lyssnare och `StatefulServiceBase.onChangeRoleAsync()` även slutar, tjänstens kallas. Det här anropet åsidosätts inte ofta i tjänsten.

   > [!NOTE]  
   > Väntar `runAsync` på att slutföra är bara nödvändigt om den här repliken är en primär replik.

3. När `StatefulServiceBase.onChangeRoleAsync()` metoden är klar `StatefulServiceBase.onCloseAsync()` anropas metoden. Det här anropet är en ovanlig åsidosättning, men det är tillgängligt.
3. När `StatefulServiceBase.onCloseAsync()` serviceobjektet är klart är det avstruerat.

## <a name="stateful-service-primary-swaps"></a>Primära swappar för tillståndskänslig tjänst
Medan en tillståndskänslig tjänst körs öppnas kommunikationsavlyssnare och `runAsync` metoden anropas endast för de primära replikerna av dessa tillståndskänsliga tjänster. Sekundära repliker är konstruerade, men ser inga ytterligare anrop. Medan en tillståndskänslig tjänst körs kan den replik som för närvarande är den primära ändras. Vilka livscykelhändelser som en tillståndskänslig replik kan se beror på om det är repliken som nedgraderas eller befordras under swappen.

### <a name="for-the-demoted-primary"></a>För den degraderade primära
Service Fabric behöver den primära repliken som nedgraderas för att stoppa bearbetningen av meddelanden och stoppa allt bakgrundsarbete. Det här steget liknar när tjänsten stängs av. En skillnad är att tjänsten inte är degraderade eller stängd, eftersom den förblir sekundär. Följande sker:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare är stängda. `CommunicationListener.closeAsync()`kallas på varje lyssnare.
    - Annulleringstoken `runAsync()` som skickades till avbryts. En kontroll av metoden `isCancelled()` för `true`annulleringstoken returnerar . Om den anropas, `throwIfCancellationRequested()` genererar tokens metod en `OperationCanceledException`.
2. När `closeAsync()` du är klar `runAsync()` på varje lyssnare och `StatefulServiceBase.onChangeRoleAsync()` även slutar, tjänstens kallas. Det här anropet åsidosätts inte ofta i tjänsten.

### <a name="for-the-promoted-secondary"></a>För den befordrade sekundära
På samma sätt behöver Service Fabric den sekundära replik som befordras för att börja lyssna efter meddelanden på kabeln och för att starta alla bakgrundsuppgifter som den behöver slutföra. Den här processen liknar när tjänsten skapas. Skillnaden är att själva repliken redan finns. Följande sker:

1. Dessa händelser inträffar parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()`anropas och alla returnerade lyssnare öppnas. `CommunicationListener.openAsync()`kallas på varje lyssnare.
    - Tjänstens `StatefulServiceBase.runAsync()` metod anropas.
2. När alla repliklyssare samtal avslutas `openAsync()` och `runAsync()` anropas, `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet åsidosätts inte ofta i tjänsten.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem vid tillståndskänslig avstängning av tjänsten och primär degradering
Service Fabric ändrar den primära av en tillståndskänslig tjänst av flera skäl. De vanligaste orsakerna är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [uppgradering av program](service-fabric-application-upgrade.md). Under dessa operationer är det viktigt att `cancellationToken`tjänsten respekterar . Detta gäller även vid normal tjänstavstängning, till exempel om tjänsten har tagits bort.

Tjänster som inte hanterar avbokning på ett rent sätt kan uppstå i flera problem. Dessa åtgärder är långsamma eftersom Service Fabric väntar på att tjänsterna ska stoppas graciöst. Detta kan i slutändan leda till misslyckade uppgraderingar som time out och återställning. Underlåtenhet att uppfylla annulleringstoken kan också orsaka obalanserade kluster. Kluster blir obalanserade eftersom noder blir heta. Tjänsterna kan dock inte balanseras om eftersom det tar för lång tid att flytta dem någon annanstans. 

Eftersom tjänsterna är tillståndskänsliga är det också troligt att tjänsterna använder [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). I Service Fabric, när en primär nedgraderas, är en av de första sakerna som händer att skrivåtkomst till det underliggande tillståndet återkallas. Detta leder till en andra uppsättning problem som kan påverka tjänstens livscykel. Samlingarna returnerar undantag baserat på tidpunkten och om repliken flyttas eller stängs av. Det är viktigt att hantera dessa undantag på rätt sätt. 

Undantag som genereras av Service Fabric är antingen permanenta [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) eller övergående [(`FabricTransientException`).](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception) Permanenta undantag ska loggas och genereras. Tillfälliga undantag kan göras om baserat på logik för återförsök.

En viktig del av testning och validering av tillförlitliga tjänster `ReliableCollections` är att hantera de undantag som kommer från att använda livscykelhändelserna tillsammans med tjänsten. Vi rekommenderar att du alltid kör din tjänst under belastning. Du bör också utföra uppgraderingar och [kaostester](service-fabric-controlled-chaos.md) innan du distribuerar till produktion. De här grundläggande stegen hjälper till att säkerställa att tjänsten implementeras korrekt och att den hanterar livscykelhändelser korrekt.

## <a name="notes-on-service-lifecycle"></a>Anmärkningar om tjänstens livscykel
* Både `runAsync()` metoden och `createServiceInstanceListeners/createServiceReplicaListeners` anropen är valfria. En tjänst kan ha en, både eller varken. Om tjänsten till exempel gör allt sitt arbete som svar på användarsamtal `runAsync()`behöver den inte implementeras. Endast kommunikationslyssnare och tillhörande kod är nödvändiga.  På samma sätt är det valfritt att skapa och returnera kommunikationslyssnare. Tjänsten kanske bara har bakgrundsarbete att utföra, `runAsync()`så den behöver bara implementera .
* Det är giltigt för en `runAsync()` tjänst att slutföra framgångsrikt och returnera från den. Detta anses inte vara ett feltillstånd. Det representerar bakgrundsarbetet för tjänsten efterbehandling. För tillståndskänsliga `runAsync()` reliable services, skulle anropas igen om tjänsten degraderas från primär, och sedan befordras tillbaka till primär.
* Om en tjänst `runAsync()` lämnar genom att utlösa något oväntat undantag är detta ett fel. Tjänstobjektet stängs av och ett hälsofel rapporteras.
* Även om det inte finns någon tidsgräns för att återvända från dessa metoder, förlorar du omedelbart möjligheten att skriva. Därför kan du inte slutföra något verkligt arbete. Vi rekommenderar att du returnerar så snabbt som möjligt när du får begäran om avbokning. Om din tjänst inte svarar på dessa API-anrop inom rimlig tid kan Service Fabric med tvång avsluta din tjänst. Detta sker vanligtvis endast under programuppgraderingar eller när en tjänst tas bort. Den här timeouten är 15 minuter som standard.
* Fel i `onCloseAsync()` sökvägen `onAbort()` resulterar i att anropas. Det här samtalet är en sista chans, bästa möjliga möjlighet för tjänsten att rensa upp och frigöra alla resurser som de har hävdat. Detta anropas vanligtvis när ett permanent fel upptäcks på noden eller när Service Fabric inte kan hantera tjänstinstansens livscykel på ett tillförlitligt sätt på grund av interna fel.
* `OnChangeRoleAsync()`anropas när den tillståndskänsliga tjänstrepliken ändrar roll, till exempel till primär eller sekundär. Primära repliker ges skrivstatus (tillåts skapa och skriva till tillförlitliga samlingar). Sekundära repliker ges lässtatus (kan bara läsa från befintliga tillförlitliga samlingar). De flesta arbetar i en tillståndskänslig tjänst utförs vid den primära repliken. Sekundära repliker kan utföra skrivskyddad validering, rapportgenerering, datautvinning eller andra skrivskyddade jobb.

## <a name="next-steps"></a>Nästa steg
* [Introduktion till tillförlitliga tjänster](service-fabric-reliable-services-introduction.md)
* [Snabbstart för tillförlitliga tjänster](service-fabric-reliable-services-quick-start-java.md)

