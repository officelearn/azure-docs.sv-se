---
title: "Översikt över livscykeln för Azure Service Fabric Reliable Services | Microsoft Docs"
description: "Lär dig mer om livscykeln för olika händelser i Service Fabric reliable services"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 93fd003ff5ba7673e5a719fb1ced0cbb97034610
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Översikt över livscykeln Reliable services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Grunderna i livscykeln är viktigast när du tänker livscykler för Reliable Services. I allmänhet:

* Under Start
  * Tjänster är uppbyggda
  * De har möjlighet att skapa och returnera noll eller flera lyssnare
  * Alla returnerade lyssnare öppnas kommunikation med tjänsten
  * Tjänstens runAsync-metoden anropas, vilket gör att tjänsten för att göra länge körs eller bakgrund arbete
* Vid avstängning
  * Annullering-token som skickades till runAsync avbryts och lyssnarna stängs
  * När detta är klar, själva tjänstobjektet är destructed

Det finns information runt den exakta sorteringen av dessa händelser. I synnerhet kan ordningen för händelser ändras lite beroende på om tjänsten tillförlitliga är Stateless eller Stateful. Dessutom för tillståndskänsliga tjänster måste vi hantera primära växlingen scenario. Under den här sekvensen rollen som primär överförs till en annan replik (eller kommer tillbaka) utan att tjänsten avslutas. Slutligen behöver vi tänka på felet villkor.

## <a name="stateless-service-startup"></a>Tillståndslösa tjänsten startades
Livscykeln för tillståndslösa tjänsten är ganska enkelt. Här är ordningen för händelser:

1. Tjänsten har skapats
2. Görs sedan i parallella två saker:
    - `StatelessService.createServiceInstanceListeners()`har anropats och eventuella returnerade lyssnare öppnas (`CommunicationListener.openAsync()` anropas på varje lyssnare)
    - Tjänstens runAsync metod (`StatelessService.runAsync()`) anropas
3. Om den finns tjänstens egna onOpenAsync-metoden anropas (särskilt `StatelessService.onOpenAsync()` anropas. Det här är en ovanligt åsidosättning men det är tillgängligt).

Det är viktigt att notera att det finns ingen ordning mellan anrop för att skapa och öppna lyssnare och runAsync. Lyssnarna kan öppna innan runAsync har startats. På liknande sätt kan runAsync hamna anropas innan kommunikationslyssnarna är öppna eller även har skapats. Om ingen synkronisering krävs, måste lämnas den som Övning till Implementeraren. Vanliga lösningar:

* Lyssnare fungerar ibland inte förrän annan information som har skapats eller arbete som gjorts. För tillståndslösa tjänster som arbete kan vanligtvis utföras i tjänstens konstruktorn under den `createServiceInstanceListeners()` anropar, eller som en del av konstruktionen för lyssnare sig själv.
* Ibland vill koden i runAsync inte startas förrän lyssnarna är öppna. I så fall krävs ytterligare samordning. En vanlig lösning är vissa flagga i lyssnare som anger när de har slutfört, som är markerad i runAsync innan du fortsätter verkligt arbete.

## <a name="stateless-service-shutdown"></a>Tillståndslösa tjänsten avstängning
När du stänger en tillståndslös tjänst följt samma mönster bara i omvänd:

1. Parallellt
    - Stängs alla öppna lyssnare (`CommunicationListener.closeAsync()` anropas på varje lyssnare)
    - Annullering token som skickades till `runAsync()` har avbrutits (kontrollerar token annullering `isCancelled` -egenskap returnerar true, och om den anropas token `throwIfCancellationRequested` metoden returnerar en `CancellationException`)
2. En gång `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också har slutförts tjänstens `StatelessService.onCloseAsync()` metoden anropas, om det finns (igen det här är en ovanligt åsidosättning).
3. När `StatelessService.onCloseAsync()` är klar serviceobjektet destructed

## <a name="stateful-service-startup"></a>Tillståndskänsliga startades
Tillståndskänsliga tjänster har ett liknande mönster tillståndslösa tjänster med några ändringar. För att starta en tillståndskänslig service är ordningen för händelser:

1. Tjänsten har skapats.
2. `StatefulServiceBase.onOpenAsync()`anropas. Det här anropet ofta inte har åsidosatts i tjänsten.
3. Följande saker parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()`anropas. 
      - Om tjänsten är en primär service, öppnas alla returnerade lyssnare. `CommunicationListener.openAsync()`anropas på varje lyssnare.
      - Om tjänsten är en sekundär service kan endast dessa lyssnare markerad som `listenOnSecondary = true` öppnas. Med lyssnare som är öppna på sekundärservrar är mindre vanliga.
    - Om tjänsten är en primär, tjänstens `StatefulServiceBase.runAsync()` metoden anropas.
4. När alla replik lyssnarens `openAsync()` anropar Slutför och `runAsync()` anropas, `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

Liksom för tillståndslösa tjänster, det finns inga samordning mellan den ordning som lyssnarna skapas och öppnas och när **runAsync** anropas. Om du behöver samordning är ungefär lösningarna. Det finns en ytterligare fall för stateful-tjänsten. Säg att anrop som når kommunikationslyssnarna kräver information som sparas i vissa [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). Eftersom kommunikationslyssnarna gick att öppna innan de tillförlitliga samlingarna är läsbart eller skrivbart och innan **runAsync** kunde starta vissa ytterligare samordning krävs. Den enklaste och de vanligaste lösningen är att returnera en felkod som klienten använder för att försöka kommunikationslyssnarna.

## <a name="stateful-service-shutdown"></a>Tillståndskänslig service avstängning
Precis som tillståndslösa tjänster Livscykelhändelser vid avstängningen är samma som under starten men ångras. När en tillståndskänslig service stängs av, händer följande:

1. Parallellt:
    - Alla öppna lyssnare stängs. `CommunicationListener.closeAsync()`anropas på varje lyssnare.
    - Annullering-token som skickades till `runAsync()` avbryts. Ett anrop till token för annullering `isCancelled()` metoden returnerar SANT, och om den anropas token `throwIfCancellationRequested()` metoden returnerar en `OperationCanceledException`.
2. Efter `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också är klar tjänstens `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

   > [!NOTE]  
   > Behovet av att vänta tills **runAsync** Slutför behövs bara om repliken är en primär replik.

3. Efter den `StatefulServiceBase.onChangeRoleAsync()` metoden slutförs den `StatefulServiceBase.onCloseAsync()` metoden anropas. Det här anropet är en ovanligt åsidosättning, men den är tillgänglig.
3. Efter `StatefulServiceBase.onCloseAsync()` slutförs, serviceobjektet destructed.

## <a name="stateful-service-primary-swaps"></a>Tillståndskänslig service primära växlingar
När en tillståndskänslig service körs, de primära replikerna av de tillståndskänsliga tjänsterna har sina kommunikationslyssnarna öppnas och deras **runAsync** anropade metoden. Sekundära repliker skapas, men det finns inga ytterligare anrop. När en tillståndskänslig service körs, repliken som för närvarande är den primära servern kan ändra. Vad innebär det i villkoren för händelserna livscykel som en replik kan se? Beteendet tillståndskänslig repliken ser beror på om det är repliken som nedgraderas eller uppgraderas under växlingen.

### <a name="for-the-primary-thats-demoted"></a>För den primära degraderas
För den primära repliken nedgraderas, måste Service Fabric replikeringen att avbryta behandlar meddelanden och avsluta alla bakgrundsjobbet avbrytas. Därför kan det här steget ser ut som om när tjänsten är avstängd. En skillnaden är att tjänsten inte är destructed eller stängts eftersom det fortfarande som en sekundär. Följande API: er anropas:

1. Parallellt:
    - Alla öppna lyssnare stängs. `CommunicationListener.closeAsync()`anropas på varje lyssnare.
    - Annullering-token som skickades till `runAsync()` har avbrutits. En kontroll av token annullering `isCancelled()` metoden returnerar SANT, och om den anropas token `throwIfCancellationRequested()` metoden returnerar en `OperationCanceledException`.
2. Efter `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också är klar tjänstens `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

### <a name="for-the-secondary-thats-promoted"></a>För den sekundära som höjs
Service Fabric måste dessutom den sekundära repliken som höjs så här startar lyssnar efter meddelanden på kabeln bakgrundsaktiviteter som behövs för att slutföra. Därför kan den här processen ser ut som den visades när tjänsten skapas, förutom att repliken själva finns redan. Följande API: er anropas:

1. Parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()`har anropats och eventuella returneras lyssnare öppnas. `CommunicationListener.openAsync()`anropas på varje lyssnare.
    - Tjänstens `StatefulServiceBase.runAsync()` metoden anropas.
2. När alla replik lyssnarens `openAsync()` anropar Slutför och `runAsync()` anropas, `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.


### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem vid avstängning av tjänst tillståndskänsliga och primära degradering
Service Fabric ändringar av en tillståndskänslig tjänst för en mängd olika skäl. De vanligaste är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [Programuppgradering](service-fabric-application-upgrade.md). Under dessa åtgärder (samt under normal drift avslutning, som du vill se om tjänsten har tagits bort), är det viktigt att tjänsten följer den `cancellationToken`. 

Tjänster som inte hanterar annullering korrekt kan få problem med flera. Dessa åtgärder är långsam eftersom Service Fabric väntar tjänster ska avslutas. Slutligen kan detta leda till misslyckade uppgraderingar tiden och återställa. Ta hänsyn till annullering token kan också medföra imbalanced kluster. Kluster bli Obalanserat eftersom noder hämta hot men kan inte vara genomförs tjänsterna eftersom det tar för lång tid att flytta dem någon annanstans. 

Eftersom tjänsterna är tillståndskänslig, det är också troligt att de använder den [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). När en primär degraderas i Service Fabric är en av de första sakerna som händer skrivåtkomst till tillståndet för underliggande återkallas. Detta leder till en annan uppsättning problem som kan påverka service lifecycle. Samlingar returnerade undantag baserat på tiden och om repliken flyttas eller stängs av. Sådana undantag ska hanteras på rätt sätt. Undantag från Service Fabric är indelade i permanent [(`FabricException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception) och tillfälligt [(`FabricTransientException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception._fabric_transient_exception) kategorier. Permanent undantag ska loggas och utlöstes vid tillfälliga undantag kan göras baserat på vissa logik.

Hantering av undantag som härrör från användning av den `ReliableCollections` tillsammans med tjänsten Livscykelhändelser är en viktig del av testa och validera en tillförlitlig tjänst. Vi rekommenderar att du alltid köra tjänsten under belastning under uppgraderingar och [chaos testning](service-fabric-controlled-chaos.md) innan du distribuerar till produktionen. De grundläggande stegen för att säkerställa att din tjänst har implementerats korrekt och hanterar Livscykelhändelser på rätt sätt.

## <a name="notes-on-service-lifecycle"></a>Information om tjänsten livscykel
* Både den `runAsync()` metod och `createServiceInstanceListeners/createServiceReplicaListeners` anrop är valfria. En tjänst kan ha ett av dem, båda eller inget. Till exempel om tjänsten gör allt arbete som svar på användaren anropar det behövs ingen att implementera `runAsync()`. Endast kommunikationslyssnarna och deras associerade kod krävs. På liknande sätt är att skapa och returnera kommunikationslyssnarna valfritt, eftersom tjänsten kan ha endast bakgrundsjobbet att göra och så att endast måste implementera`runAsync()`
* Det är giltigt för en tjänst att slutföra `runAsync()` har och RETUR från den. Detta anses inte vara ett fel inträffar och representerar bakgrundsjobbet service slutföra. För tillståndskänsliga reliable services `runAsync()` skulle anropas igen om tjänsten har nedgraderas från primära och sedan befordras till primära.
* Om en tjänst avslutas från `runAsync()` genom att vissa oväntade undantag, detta är ett fel och serviceobjektet stängs av och ett hälsotillstånd fel rapporteras.
* När det finns ingen tidsgräns på returneras från dessa metoder kan du att förlora omedelbart möjligheten att skriva och därför slutföra inte något verkligt arbete. Vi rekommenderar att du returnera så snabbt som möjligt vid mottagning av begäran om att avbryta. Om tjänsten inte svarar på dessa API-anrop i rimlig tid Service Fabric tvång att avbryta tjänsten. Vanligtvis sker detta endast under programuppgraderingar eller när en tjänst tas bort. Det här är 15 minuter som standard.
* Fel i den `onCloseAsync()` sökväg resultatet i `onAbort()` anropas som är ett sista chansen bästa möjligheten för tjänsten för att rensa upp och frigör alla resurser som de har begärts.

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Reliable Services](service-fabric-reliable-services-introduction.md)
* [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start-java.md)
* [Reliable Services avancerade användning](service-fabric-reliable-services-advanced-usage.md)
