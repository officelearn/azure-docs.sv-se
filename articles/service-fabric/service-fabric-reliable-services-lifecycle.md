---
title: Översikt över livscykeln för tillförlitliga tjänster
description: Lär dig mer om livscykelhändelserna i ett Azure Service Fabric Reliable Services-program för tillståndskänsliga och tillståndslösa tjänster.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: fe338ca3f25cd606da7f95f6c9437a3cd3dc4e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258283"
---
# <a name="reliable-services-lifecycle-overview"></a>Översikt över reliable services livscykel
> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

När du tänker på livscykeln för Azure Service Fabric Reliable Services är grunderna i livscykeln de viktigaste. I allmänhet innehåller livscykeln följande:

- Under start:
  - Tjänsterna är konstruerade.
  - Tjänsterna har möjlighet att konstruera och returnera noll eller fler lyssnare.
  - Alla returnerade lyssnare öppnas, vilket möjliggör kommunikation med tjänsten.
  - Tjänstens **RunAsync-metod** anropas, vilket gör att tjänsten kan utföra långvariga uppgifter eller bakgrundsarbete.
- Under avstängning:
  - Annulleringstoken som skickades till **RunAsync** avbryts och lyssnarna stängs.
  - När lyssnarna stängs är själva serviceobjektet avstruerat.

Det finns detaljer kring den exakta ordningen på dessa händelser. Händelseordningen kan ändras något beroende på om den tillförlitliga tjänsten är tillståndslös eller tillståndskänslig. Dessutom, för tillståndskänsliga tjänster, måste vi ta itu med den primära swap scenario. Under den här sekvensen överförs rollen primär till en annan replik (eller kommer tillbaka) utan att tjänsten stängs av. Slutligen måste vi tänka på fel- eller felförhållanden.

## <a name="stateless-service-startup"></a>Start av tillståndslös tjänst
Livscykeln för en tillståndslös tjänst är enkel. Här är ordningen på händelserna:

1. Tjänsten är konstruerad.
2. Sedan, parallellt, händer två saker:
    - `StatelessService.CreateServiceInstanceListeners()`anropas och alla returnerade lyssnare öppnas. `ICommunicationListener.OpenAsync()`kallas på varje lyssnare.
    - Tjänstens `StatelessService.RunAsync()` metod anropas.
3. Om det finns något `StatelessService.OnOpenAsync()` att göra anropa anropas tjänstens metod. Det här anropet är en ovanlig åsidosättning, men det är tillgängligt. Utökade tjänstinitiseringsuppgifter kan startas just nu.

Tänk på att det inte finns någon ordning mellan anropen för att skapa och öppna lyssnarna och **RunAsync**. Lyssnarna kan öppna innan **RunAsync** startas. På samma sätt kan du anropa **RunAsync** innan kommunikationsavlyssnare är öppna eller till och med konstruerade. Om någon synkronisering krävs, lämnas den som en övning till implementeraren. Här är några vanliga lösningar:

  - Ibland kan lyssnare inte fungera förrän någon annan information har skapats eller arbete har utförts. För tillståndslösa tjänster kan det arbetet vanligtvis utföras på andra platser, till exempel följande: 
    - I tjänstens konstruktör.
    - Under `CreateServiceInstanceListeners()` samtalet.
    - Som en del av byggandet av lyssnaren själv.
  - Ibland startar inte koden i **RunAsync** förrän lyssnarna är öppna. I detta fall är ytterligare samordning nödvändig. En vanlig lösning är att det finns en flagga i lyssnarna som anger när de är klara. Den här flaggan kontrolleras sedan i **RunAsync** innan du fortsätter till verkligt arbete.

## <a name="stateless-service-shutdown"></a>Avstängning av tillståndslös tjänst
För att stänga av en tillståndslös tjänst följs samma mönster, precis i omvänd ordning:

1. Parallellt gäller det:
    - Alla öppna lyssnare är stängda. `ICommunicationListener.CloseAsync()`kallas på varje lyssnare.
    - Annulleringstoken som skickades till `RunAsync()` avbryts. En kontroll av annulleringstokens `IsCancellationRequested` egenskap returnerar `ThrowIfCancellationRequested` sant, och `OperationCanceledException`om den anropas, genererar tokens metod en .
2. När `CloseAsync()` du är klar `RunAsync()` på varje lyssnare och `StatelessService.OnCloseAsync()` även avslutas, tjänstens metod kallas, om det finns.  OnCloseAsync anropas när den tillståndslösa tjänstinstansen kommer att stängas av på ett smidigt sätt. Detta kan inträffa när tjänstens kod uppgraderas, tjänstinstansen flyttas på grund av belastningsutjämning eller ett tillfälligt fel upptäcks. Det är ovanligt `StatelessService.OnCloseAsync()`att åsidosätta , men det kan användas för att säkert stänga resurser, stoppa bakgrundsbearbetning, slutföra spara externa tillstånd eller stänga befintliga anslutningar.
3. När `StatelessService.OnCloseAsync()` serviceobjektet är klart är det avstruerat.

## <a name="stateful-service-startup"></a>Tillståndskänslig tjänststart
Tillståndskänsliga tjänster har ett liknande mönster som statslösa tjänster, med några ändringar. För att starta en tillståndskänslig tjänst är händelseordningen följande:

1. Tjänsten är konstruerad.
2. `StatefulServiceBase.OnOpenAsync()`kallas. Det här anropet åsidosätts inte ofta i tjänsten.
3. Följande saker händer parallellt:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`åberopas. 
      - Om tjänsten är en primär tjänst öppnas alla returnerade lyssnare. `ICommunicationListener.OpenAsync()`kallas på varje lyssnare.
      - Om tjänsten är en sekundär tjänst öppnas `ListenOnSecondary = true` endast de lyssnare som är markerade som. Att ha lyssnare som är öppna på sekundärer är mindre vanligt.
    - Om tjänsten för närvarande är primär `StatefulServiceBase.RunAsync()` anropas tjänstens metod.
4. När alla repliklyssare samtal avslutas `OpenAsync()` och `RunAsync()` anropas, `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet åsidosätts inte ofta i tjänsten.

I likhet med tillståndslösa tjänster finns det ingen samordning mellan den ordning i vilken lyssnarna skapas och öppnas och när **RunAsync** anropas. Om du behöver samordning, lösningarna är ungefär desamma. Det finns ytterligare ett fall för tillståndskänslig service. Säg att de samtal som anländer till kommunikationen lyssnare kräver information som förvaras i vissa [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Eftersom kommunikationslyssnare kan öppna innan de tillförlitliga samlingarna är läsbara eller skrivbara, och innan **RunAsync** kunde starta, krävs ytterligare samordning. Den enklaste och vanligaste lösningen är att kommunikationslyssnare returnerar en felkod som klienten använder för att veta för att försöka igen för att försöka med begäran igen.

## <a name="stateful-service-shutdown"></a>Tillståndskänslig tjänst avstängning
Precis som tillståndslösa tjänster är livscykelhändelserna under avstängning samma som under starten, men omvända. När en tillståndskänslig tjänst stängs av inträffar följande händelser:

1. Parallellt gäller det:
    - Alla öppna lyssnare är stängda. `ICommunicationListener.CloseAsync()`kallas på varje lyssnare.
    - Annulleringstoken som skickades till `RunAsync()` avbryts. En kontroll av annulleringstokens `IsCancellationRequested` egenskap returnerar `ThrowIfCancellationRequested` sant, och `OperationCanceledException`om den anropas, genererar tokens metod en .
2. När `CloseAsync()` du är klar `RunAsync()` på varje lyssnare och `StatefulServiceBase.OnChangeRoleAsync()` även slutar, tjänstens kallas. Det här anropet åsidosätts inte ofta i tjänsten.

   > [!NOTE]  
   > Behovet av att vänta på att **RunAsync** ska slutföras är bara nödvändigt om den här repliken är en primär replik.

3. När `StatefulServiceBase.OnChangeRoleAsync()` metoden är klar `StatefulServiceBase.OnCloseAsync()` anropas metoden. Det här anropet är en ovanlig åsidosättning, men det är tillgängligt.
3. När `StatefulServiceBase.OnCloseAsync()` serviceobjektet är klart är det avstruerat.

## <a name="stateful-service-primary-swaps"></a>Tillståndskänslig tjänst Primära swappar
Medan en tillståndskänslig tjänst körs, bara de primära replikerna för dessa tillståndskänsliga tjänster har sina kommunikationsavlyssnare öppnas och deras **RunAsync-metod** anropas. Sekundära repliker är konstruerade, men ser inga ytterligare anrop. Medan en tillståndskänslig tjänst körs kan repliken som för närvarande är primär ändras till följd av fel- eller klusterbalanseringsoptimering. Vad innebär detta när det gäller livscykelhändelser som en replik kan se? Vilket beteende som den tillståndskänsliga repliken ser beror på om det är repliken som degraderas eller befordras under swappen.

### <a name="for-the-primary-thats-demoted"></a>För primärt som degraderas
För den primära repliken som nedgraderas behöver Service Fabric den här repliken för att stoppa bearbetningen av meddelanden och avsluta allt bakgrundsarbete som den utför. Som ett resultat ser det här steget ut som det gjorde när tjänsten stängs av. En skillnad är att tjänsten inte är degraderade eller stängd eftersom den förblir sekundär. Följande API:er anropas:

1. Parallellt gäller det:
    - Alla öppna lyssnare är stängda. `ICommunicationListener.CloseAsync()`kallas på varje lyssnare.
    - Annulleringstoken som skickades till `RunAsync()` avbryts. En kontroll av annulleringstokens `IsCancellationRequested` egenskap returnerar `ThrowIfCancellationRequested` sant, och `OperationCanceledException`om den anropas, genererar tokens metod en .
2. När `CloseAsync()` du är klar `RunAsync()` på varje lyssnare och `StatefulServiceBase.OnChangeRoleAsync()` även slutar, tjänstens kallas. Det här anropet åsidosätts inte ofta i tjänsten.

### <a name="for-the-secondary-thats-promoted"></a>För sekundärt som marknadsförs
På samma sätt behöver Service Fabric den sekundära replik som befordras för att börja lyssna efter meddelanden på tråden och starta alla bakgrundsuppgifter som den behöver slutföra. Därför ser den här processen ut som den gjorde när tjänsten skapas, förutom att själva repliken redan finns. Följande API:er anropas:

1. Parallellt gäller det:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`anropas och alla returnerade lyssnare öppnas. `ICommunicationListener.OpenAsync()`kallas på varje lyssnare.
    - Tjänstens `StatefulServiceBase.RunAsync()` metod anropas.
2. När alla repliklyssare samtal avslutas `OpenAsync()` och `RunAsync()` anropas, `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet åsidosätts inte ofta i tjänsten.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem under tillståndskänslig tjänst avstängning och primär degradering
Service Fabric ändrar primärt för en tillståndskänslig tjänst av olika skäl. De vanligaste är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [uppgradering av program](service-fabric-application-upgrade.md). Under dessa åtgärder (samt under normal avstängning av tjänsten, som du skulle se om tjänsten `CancellationToken`har tagits bort), är det viktigt att tjänsten respekterar . 

Tjänster som inte hanterar avbokning rent kan uppleva flera problem. Dessa åtgärder är långsamma eftersom Service Fabric väntar på att tjänsterna ska stoppas graciöst. Detta kan i slutändan leda till misslyckade uppgraderingar som time out och återställa. Underlåtenhet att uppfylla annulleringstoken kan också orsaka obalanserade kluster. Kluster blir obalanserade eftersom noder blir heta, men tjänsterna kan inte balanseras eftersom det tar för lång tid att flytta dem någon annanstans. 

Eftersom tjänsterna är tillståndskänsliga är det också troligt att de använder [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). I Service Fabric, när en primär nedgraderas, är en av de första sakerna som händer att skrivåtkomst till det underliggande tillståndet återkallas. Detta leder till en andra uppsättning problem som kan påverka tjänstens livscykel. Samlingarna returnerar undantag baserat på tidpunkten och om repliken flyttas eller stängs av. Dessa undantag bör hanteras korrekt. Undantag som genereras av Service Fabric kan delas in i permanenta [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) och övergående [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategorier. Permanenta undantag ska loggas och genereras medan de tillfälliga undantagen kan göras om baserat på viss logik för återförsök.

Att hantera undantag som kommer `ReliableCollections` från användning av händelser i samband med tjänstens livscykel är en viktig del av testning och validering av en tillförlitlig tjänst. Vi rekommenderar att du alltid kör din tjänst under belastning när du utför uppgraderingar och [kaostester](service-fabric-controlled-chaos.md) innan du distribuerar till produktion. De här grundläggande stegen hjälper dig att se till att tjänsten implementeras korrekt och hanterar livscykelhändelser korrekt.


## <a name="notes-on-the-service-lifecycle"></a>Anmärkningar om tjänstens livscykel
  - Både `RunAsync()` metoden och `CreateServiceReplicaListeners/CreateServiceInstanceListeners` anropen är valfria. En tjänst kan ha en av dem, båda eller ingen av dem. Om tjänsten till exempel gör allt sitt arbete som svar på användaranrop, behöver den inte implementera `RunAsync()`. Endast kommunikationslyssnare och tillhörande kod är nödvändiga. På samma sätt är det valfritt att skapa och returnera kommunikationsavlyssnare, `RunAsync()`eftersom tjänsten bara kan ha bakgrundsarbete att utföra och därför bara behöver implementeras.
  - Det är giltigt för `RunAsync()` en tjänst att slutföra framgångsrikt och returnera från den. Att slutföra är inte ett feltillstånd. Slutförd `RunAsync()` anger att tjänstens bakgrundsarbete har avslutats. För tillståndskänsliga `RunAsync()` tillförlitliga tjänster anropas igen om repliken nedgraderas från Primär till Sekundär och sedan befordras tillbaka till Primär.
  - Om en tjänst `RunAsync()` lämnar genom att utlösa något oväntat undantag utgör detta ett fel. Serviceobjektet stängs av och ett hälsofel rapporteras.
  - Även om det inte finns någon tidsgräns för att återvända från dessa metoder, förlorar du omedelbart möjligheten att skriva till tillförlitliga samlingar, och därför kan inte slutföra något verkligt arbete. Vi rekommenderar att du återvänder så snabbt som möjligt när du får begäran om avbokning. Om din tjänst inte svarar på dessa API-anrop inom rimlig tid kan Service Fabric med våld avsluta din tjänst. Vanligtvis händer detta bara under programuppgraderingar eller när en tjänst tas bort. Den här timeouten är 15 minuter som standard.
  - Fel i `OnCloseAsync()` sökvägen `OnAbort()` resulterar i att anropas, vilket är en sista chans bästa möjliga möjlighet för tjänsten att rensa upp och frigöra alla resurser som de har hävdat. Detta anropas vanligtvis när ett permanent fel upptäcks på noden eller när Service Fabric inte kan hantera tjänstinstansens livscykel på ett tillförlitligt sätt på grund av interna fel.
  - `OnChangeRoleAsync()`anropas när den tillståndskänsliga tjänstrepliken ändrar roll, till exempel till primär eller sekundär. Primära repliker ges skrivstatus (tillåts skapa och skriva till tillförlitliga samlingar). Sekundära repliker ges lässtatus (kan bara läsa från befintliga tillförlitliga samlingar). De flesta arbetar i en tillståndskänslig tjänst utförs vid den primära repliken. Sekundära repliker kan utföra skrivskyddad validering, rapportgenerering, datautvinning eller andra skrivskyddade jobb.

## <a name="next-steps"></a>Nästa steg
- [Introduktion till tillförlitliga tjänster](service-fabric-reliable-services-introduction.md)
- [Snabbstart för Tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md)
- [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
