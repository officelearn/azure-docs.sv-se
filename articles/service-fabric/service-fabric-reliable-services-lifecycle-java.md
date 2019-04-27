---
title: Azure Service Fabric Reliable Services-livscykel | Microsoft Docs
description: Läs mer om Livscykelhändelser i Service Fabric Reliable Services.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 36c1ff2ace944d84120bf456060c7504170a814c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772949"
---
# <a name="reliable-services-lifecycle"></a>Reliable Services-livscykel
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services är en av programmeringsmodellerna som är tillgängliga i Azure Service Fabric. När lära dig om livscykeln för Reliable Services, är det mest viktigt att förstå grundläggande Livscykelhändelser. Den exakta sorteringen av händelser beror på konfigurationsinformation. 

I allmänhet innehåller Reliable Services-livscykel följande händelser:

* Under start:
  * Tjänster skapas.
  * Tjänster har möjlighet att konstruera och returnerar noll eller flera lyssnare.
  * Alla returnerade lyssnare är öppna för kommunikation med tjänsten.
  * Tjänstens `runAsync` metoden anropas, så att tjänsten kan göra långvariga eller background arbete.
* Under avstängning:
  * Annulleringen token som skickades till `runAsync` har avbrutits och lyssnarna är stängda.
  * Objektet själva destructed.

Ordningen för händelser i Reliable Services kan ändras lite beroende på om tillförlitlig tjänst är tillståndslösa eller tillståndskänsliga. 

Du måste också lösa det primära swap-scenariot för tillståndskänsliga tjänster. Under den här sekvensen rollen som primär överförs till en annan replik (eller kommer tillbaka) utan att tjänsten avslutas. 

Slutligen kan behöver du bekymra dig om villkor för fel eller inte.

## <a name="stateless-service-startup"></a>Tillståndslösa tjänsten startades
Livscykeln för en tillståndslös tjänst är ganska enkelt. Här är händelseordningen:

1. Tjänsten skapas.
2. Dessa händelser inträffar parallellt:
    - `StatelessService.createServiceInstanceListeners()` har anropats och eventuella returneras lyssnare är öppna. `CommunicationListener.openAsync()` anropas på varje lyssnare.
    - Tjänstens `runAsync` metod (`StatelessService.runAsync()`) anropas.
3. Om den finns på tjänstens egna `onOpenAsync` metoden anropas. Mer specifikt `StatelessService.onOpenAsync()` anropas. Det här är en ovanlig åsidosättning, men den är tillgänglig.

Det är viktigt att Observera att det finns ingen ordning mellan anropet för att skapa och öppna lyssnarna och anropet till `runAsync`. Lyssnarna kan öppna innan `runAsync` har startats. På samma sätt `runAsync` kan anropas innan kommunikationslyssnarna är öppna, eller innan de även har skapats. Om ingen synkronisering krävs, måste du göra det genom Implementeraren. Här följer ett antal vanliga lösningar:

* Lyssnare fungerar ibland inte förrän annan information som har skapats eller andra jobbet är klart. För tillståndslösa tjänster som fungerar vanligtvis kan genomföras i tjänstens konstruktorn. Det kan göras under den `createServiceInstanceListeners()` anropar, eller som en del av produktionen av lyssnaren själva.
* Ibland koden i `runAsync` startar inte förrän lyssnarna är öppna. I så fall krävs ytterligare samordning. En vanlig lösning är att lägga till en flagga i lyssnarna. Flagga som anger när lyssnarna är klar. Den `runAsync` metoden kontrollerar detta innan du fortsätter det faktiska arbetet.

## <a name="stateless-service-shutdown"></a>Tillståndslös tjänst avstängning
När du har påbörjat en tillståndslös tjänst är samma mönster följs, men i omvänd:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare stängs. `CommunicationListener.closeAsync()` anropas på varje lyssnare.
    - Annulleringen token som skickades till `runAsync()` har avbrutits. Kontrollera token annullering `isCancelled` egenskapen returnerar `true`, och om den anropas token `throwIfCancellationRequested` metoden returnerar en `CancellationException`.
2. När `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också är klar tjänstens `StatelessService.onCloseAsync()` metoden anropas, om den finns. Igen, detta är inte en gemensam åsidosättning, men den kan användas för att på ett säkert sätt stänga resurser, stoppa behandling i bakgrunden, Slutför sparar externa tillstånd eller stänga ned befintliga anslutningar.
3. Efter `StatelessService.onCloseAsync()` har slutförts serviceobjektet destructed.

## <a name="stateful-service-startup"></a>Tillståndskänsliga tjänsten startades
Tillståndskänsliga tjänster har ett mönster som liknar tillståndslösa tjänster, med några ändringar.  Här är ordningen för händelser för att starta en tillståndskänslig tjänst:

1. Tjänsten skapas.
2. `StatefulServiceBase.onOpenAsync()` kallas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.
3. Dessa händelser inträffar parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()` anropas. 
      - Om tjänsten är en primär tjänst, öppnas alla returnerade lyssnare. `CommunicationListener.openAsync()` anropas på varje lyssnare.
      - Om tjänsten är en sekundär service kan bara lyssnare märkta `listenOnSecondary = true` öppnas. Det är mindre vanligt att ha lyssnare som är öppna på sekundära databaser.
    - Om tjänsten för närvarande är en primär, tjänstens `StatefulServiceBase.runAsync()` metoden anropas.
4. När du har alla repliken lyssningsfunktionens `openAsync()` anropar Slutför och `runAsync()` anropas `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.

Liknar tillståndslösa tjänster i tillståndskänslig tjänst, det finns inga samordning mellan den ordning i vilken lyssnarna skapas och öppnas och när `runAsync` anropas. Om du behöver samordning är ungefär samma lösningarna. Men det finns en ytterligare fallet för tillståndskänsliga tjänster. Säger att de anrop som når kommunikationslyssnarna kräver information sparas i vissa [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Eftersom kommunikationslyssnarna kan öppna innan tillförlitliga samlingar är läsbart eller skrivbart och innan `runAsync` startar vissa ytterligare samordning krävs. Den enklaste och vanligaste lösningen är för kommunikationslyssnarna att returnera en felkod. Klienten använder felkoden veta för att försöka.

## <a name="stateful-service-shutdown"></a>Avstängning av tillståndskänslig tjänst
Precis som tillståndslösa tjänster Livscykelhändelser vid avstängningen är samma som under starten, men ångras. När en tillståndskänslig tjänst har stängts ned, händer följande:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare stängs. `CommunicationListener.closeAsync()` anropas på varje lyssnare.
    - Annulleringen token som skickades till `runAsync()` har avbrutits. Ett anrop till annullering token `isCancelled()` metoden returnerar `true`, och om den anropas token `throwIfCancellationRequested()` metoden returnerar en `OperationCanceledException`.
2. Efter `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också är klar tjänstens `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.

   > [!NOTE]  
   > Väntar på `runAsync` ska slutföras krävs endast om repliken är en primär replik.

3. Efter den `StatefulServiceBase.onChangeRoleAsync()` metoden har slutförts i `StatefulServiceBase.onCloseAsync()` metoden anropas. Det här anropet är en ovanlig åsidosättning, men den är tillgänglig.
3. Efter `StatefulServiceBase.onCloseAsync()` har slutförts serviceobjektet destructed.

## <a name="stateful-service-primary-swaps"></a>Tillståndskänslig tjänst primära växlingar
När en tillståndskänslig tjänst körs kommunikationslyssnarna öppnas och `runAsync` metoden anropas endast för de primära replikerna av de tillståndskänsliga tjänsterna. Sekundära repliker skapas, men det finns inga ytterligare anrop. När en tillståndskänslig tjänst körs den replik som är för närvarande primärt kan ändra. Livscykelhändelser för en tillståndskänslig replik kan se beror på om det är repliken att degraderas eller uppgraderas under växlingen.

### <a name="for-the-demoted-primary"></a>För de sänkta primära
Service Fabric måste den primära repliken degraderas för att stoppa bearbetningen meddelanden och stoppa alla bakgrundsjobbet. Det här steget liknar när tjänsten är avstängd. En skillnad är att tjänsten inte är destructed eller stängts, eftersom den fortfarande som en sekundär. Följande sker:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare stängs. `CommunicationListener.closeAsync()` anropas på varje lyssnare.
    - Annulleringen token som skickades till `runAsync()` har avbrutits. En kontroll av token annullering `isCancelled()` metoden returnerar `true`. Om den anropas token `throwIfCancellationRequested()` metoden returnerar en `OperationCanceledException`.
2. Efter `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också är klar tjänstens `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.

### <a name="for-the-promoted-secondary"></a>För upphöjda sekundär
Service Fabric måste på samma sätt kan den sekundära repliken befordras att starta lyssnar efter meddelanden i nätverket, samt att starta alla bakgrundsaktiviteter som krävs för att slutföra. Den här processen liknar när tjänsten skapas. Skillnaden är att repliken själva redan finns. Följande sker:

1. Dessa händelser inträffar parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()` har anropats och eventuella returneras lyssnare är öppna. `CommunicationListener.openAsync()` anropas på varje lyssnare.
    - Tjänstens `StatefulServiceBase.runAsync()` metoden anropas.
2. När du har alla repliken lyssningsfunktionens `openAsync()` anropar Slutför och `runAsync()` anropas `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem vid avstängning av tillståndskänslig tjänst eller primära degradering
Service Fabric ändras av en tillståndskänslig tjänst av flera skäl. De vanligaste orsakerna är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [Programuppgradering](service-fabric-application-upgrade.md). Under dessa åtgärder är det viktigt att tjänsten respekterar de `cancellationToken`. Detta gäller även under normal drift stänga av, till exempel om tjänsten har tagits bort.

Tjänster som inte hanterar avbryta korrekt kan uppleva flera problem. De här åtgärderna är långsam eftersom Service Fabric väntar för tjänster att stoppa ett smidigt sätt. Detta kan slutligen leda till misslyckade uppgraderingar som timeout och återställning. Det gick inte att respektera annullering token kan även orsaka imbalanced kluster. Kluster blir obalanserade eftersom noder hämta frekvent. Tjänsterna kan inte vara genomförs eftersom det tar för lång tid att flytta dem någon annanstans. 

Eftersom tjänsterna är tillståndskänsliga, är det också sannolikt att tjänsterna använder [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). När en primär degraderas i Service Fabric är en av de första sakerna som sker att skrivbehörighet till det underliggande tillståndet har återkallats. Detta leder till en annan uppsättning av problem som kan påverka service-livscykeln. Samlingar returnerade undantag baserat på tiden och om repliken flyttas eller avstängning. Det är viktigt att hantera dessa korrekt. 

Undantag från Service Fabric är antingen permanenta [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) eller tillfälligt [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Permanent undantag ska loggas och utlöses. Tillfälliga undantag kan göras baserat på logik för omprövning.

En viktig del av testar och validerar Reliable Services hanterar undantagen som kommer från att använda den `ReliableCollections` tillsammans med Livscykelhändelser för tjänsten. Vi rekommenderar att du alltid köra tjänsten under belastning. Du bör också utföra uppgraderingar och [chaos testning](service-fabric-controlled-chaos.md) innan du distribuerar till produktion. De grundläggande stegen för att säkerställa att din tjänst har implementerats korrekt och att den hanterar Livscykelhändelser för korrekt.

## <a name="notes-on-service-lifecycle"></a>Anmärkningar om tjänsten livscykel
* Både den `runAsync()` metod och `createServiceInstanceListeners/createServiceReplicaListeners` anrop är valfria. En tjänst kan ha en, båda eller ingen. Till exempel om tjänsten gör allt arbete som svar på användaren anrop, finns inget behov av den för att implementera `runAsync()`. Endast kommunikationslyssnarna och deras associerade kod krävs.  På samma sätt är skapa och returnera kommunikationslyssnarna valfritt. Tjänsten kan ha bakgrundsjobbet krävs, så den behöver bara att implementera `runAsync()`.
* Den är giltig för en tjänst för att slutföra `runAsync()` har och gå tillbaka från den. Detta är inte anses vara ett fel inträffar. Den motsvarar bakgrundsjobbet tjänsten avslutas. För tillståndskänsliga Reliable Services `runAsync()` skulle anropas igen om tjänsten nedgraderas från primära och sedan befordras till primär.
* Om en tjänst avslutas från `runAsync()` genom att utlösa ett oväntat undantag, detta är ett fel. Objektet stängs av och ett hälsotillstånd fel rapporteras.
* Det finns ingen tidsbegränsning om hur du återställer från dessa metoder, men du förlorar omedelbart kunna skriva. Därför kan utföra du inte några riktiga arbetet. Vi rekommenderar att du kommer tillbaka så snabbt som möjligt när tas emot på avbrottsbegäran. Om tjänsten inte svarar till dessa API-anrop under en rimlig tid, kan Service Fabric tvång att avsluta tjänsten. Detta sker vanligtvis bara under programuppgraderingar eller när en tjänst tas bort. Den här timeouten är 15 minuter som standard.
* Fel i den `onCloseAsync()` leda till sökvägen `onAbort()` som anropas. Det här anropet är en sista chansen, mån möjlighet för tjänsten för att rensa och släpper alla resurser som de har ägs. Detta kallas vanligtvis när ett permanent fel upptäcks på noden, eller när Service Fabric på ett tillförlitligt sätt inte kan hantera den tjänstinstansen livscykel på grund av ett internt fel.
* `OnChangeRoleAsync()` anropas när repliken tillståndskänslig tjänst är ändrar roll, till exempel till primär eller sekundär. Primära repliker får skrivstatus (ska kunna skapa och skriva till tillförlitliga samlingar). Sekundära repliker ges Lässtatus (kan bara läsa från befintliga tillförlitliga samlingar). De flesta arbete i en tillståndskänslig tjänst utförs på den primära repliken. Sekundära repliker kan utföra skrivskyddade verifiering, rapportgenerering, datautvinning eller andra skrivskyddade jobb.

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Reliable Services](service-fabric-reliable-services-introduction.md)
* [Reliable Services-Snabbstart](service-fabric-reliable-services-quick-start-java.md)

