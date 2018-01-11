---
title: "Översikt över livscykeln för Azure Service Fabric Reliable Services | Microsoft Docs"
description: "Lär dig mer om livscykeln för olika händelser i Service Fabric Reliable Services"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ebfe23ea1e07e7578e8bd352a482ecb1016829de
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Tillförlitliga tjänstöversikt livscykel
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Grunderna i livscykeln är viktigast när du funderar livscykler av Azure Service Fabric Reliable Services. I allmänhet omfattar livscykeln följande:

- Under start:
  - Tjänster skapas.
  - Tjänsterna har möjlighet att skapa och returnera noll eller flera lyssnare.
  - Alla returnerade lyssnare öppnas kommunikation med tjänsten.
  - Tjänstens **RunAsync** metoden anropas, vilket gör att tjänsten för att göra tidskrävande uppgifter eller bakgrundsjobbet.
- Under stängs av:
  - Annullering-token som skickades till **RunAsync** har avbrutits, och lyssnarna har avslutats.
  - När lyssnarna stängt destructed serviceobjektet sig själv.

Det finns information runt den exakta sorteringen av dessa händelser. Ordningen för händelser kan ändra något beroende på om tjänsten tillförlitliga är tillståndslösa och tillståndskänsliga. Dessutom måste vi omfattar med det primära växling scenariot för tillståndskänsliga tjänster. Under den här sekvensen rollen som primär överförs till en annan replik (eller kommer tillbaka) utan att tjänsten avslutas. Slutligen måste vi tror om felet villkor.

## <a name="stateless-service-startup"></a>Tillståndslösa tjänsten startades
Livscykeln för tillståndslösa tjänsten är enkelt. Här är ordningen för händelser:

1. Tjänsten har skapats.
2. Sedan parallellt saker två:
    - `StatelessService.CreateServiceInstanceListeners()`har anropats och eventuella returneras lyssnare öppnas. `ICommunicationListener.OpenAsync()`anropas på varje lyssnare.
    - Tjänstens `StatelessService.RunAsync()` metoden anropas.
3. Om den finns i tjänstens `StatelessService.OnOpenAsync()` metoden anropas. Det här anropet är en ovanligt åsidosättning, men den är tillgänglig.

Tänk på att det finns ingen ordning mellan anrop för att skapa och öppna lyssnarna och **RunAsync**. Lyssnarna kan öppna innan **RunAsync** har startats. På samma sätt som du kan anropa **RunAsync** innan kommunikationslyssnarna är öppna eller även konstruerade. Om ingen synkronisering krävs, måste lämnas den som Övning till Implementeraren. Här följer några vanliga lösningar:

  - Lyssnare fungerar ibland inte förrän annan information som har skapats eller arbetet. För tillståndslösa tjänster som arbete kan vanligtvis utföras på andra platser, till exempel följande: 
    - I tjänstens konstruktor.
    - Under den `CreateServiceInstanceListeners()` anropa.
    - Som en del av konstruktionen för lyssnare sig själv.
  - Ibland koden i **RunAsync** inte startas förrän lyssnarna är öppna. I så fall krävs ytterligare samordning. En vanlig lösning är att det finns en flagga i lyssnarna som anger när de är klar. Den här flaggan kontrolleras **RunAsync** innan du fortsätter verkligt arbete.

## <a name="stateless-service-shutdown"></a>Tillståndslösa tjänsten avstängning
För att stänga en tillståndslös tjänst följs samma mönster bara i omvänd:

1. Parallellt:
    - Alla öppna lyssnare stängs. `ICommunicationListener.CloseAsync()`anropas på varje lyssnare.
    - Annullering-token som skickades till `RunAsync()` har avbrutits. En kontroll av token annullering `IsCancellationRequested` -egenskap returnerar true, och om den anropas token `ThrowIfCancellationRequested` metoden returnerar en `OperationCanceledException`.
2. Efter `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` också är klar tjänstens `StatelessService.OnCloseAsync()` metoden anropas, om sådan finns. Det är ovanligt att åsidosätta `StatelessService.OnCloseAsync()`.
3. Efter `StatelessService.OnCloseAsync()` slutförs, serviceobjektet destructed.

## <a name="stateful-service-startup"></a>Tillståndskänsliga startades
Tillståndskänsliga tjänster har ett liknande mönster tillståndslösa tjänster med några ändringar. För att starta en tillståndskänslig service är ordningen för händelser:

1. Tjänsten har skapats.
2. `StatefulServiceBase.OnOpenAsync()`anropas. Det här anropet ofta inte har åsidosatts i tjänsten.
3. Följande saker parallellt:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`anropas. 
      - Om tjänsten är en primär service, öppnas alla returnerade lyssnare. `ICommunicationListener.OpenAsync()`anropas på varje lyssnare.
      - Om tjänsten är en sekundär service kan endast dessa lyssnare markerad som `ListenOnSecondary = true` öppnas. Med lyssnare som är öppna på sekundärservrar är mindre vanliga.
    - Om tjänsten är en primär, tjänstens `StatefulServiceBase.RunAsync()` metoden anropas.
4. När alla replik lyssnarens `OpenAsync()` anropar Slutför och `RunAsync()` anropas, `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

Liksom för tillståndslösa tjänster, det finns inga samordning mellan den ordning som lyssnarna skapas och öppnas och när **RunAsync** anropas. Om du behöver samordning är ungefär lösningarna. Det finns en ytterligare fall för stateful-tjänsten. Säg att anrop som når kommunikationslyssnarna kräver information som sparas i vissa [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). Eftersom kommunikationslyssnarna gick att öppna innan de tillförlitliga samlingarna är läsbart eller skrivbart och innan **RunAsync** kunde starta vissa ytterligare samordning krävs. Den enklaste och de vanligaste lösningen är att returnera en felkod som klienten använder för att försöka kommunikationslyssnarna.

## <a name="stateful-service-shutdown"></a>Tillståndskänslig service avstängning
Precis som tillståndslösa tjänster Livscykelhändelser vid avstängningen är samma som under starten men ångras. När en tillståndskänslig service stängs av, händer följande:

1. Parallellt:
    - Alla öppna lyssnare stängs. `ICommunicationListener.CloseAsync()`anropas på varje lyssnare.
    - Annullering-token som skickades till `RunAsync()` har avbrutits. En kontroll av token annullering `IsCancellationRequested` -egenskap returnerar true, och om den anropas token `ThrowIfCancellationRequested` metoden returnerar en `OperationCanceledException`.
2. Efter `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` också är klar tjänstens `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

   > [!NOTE]  
   > Behovet av att vänta tills **RunAsync** Slutför behövs bara om repliken är en primär replik.

3. Efter den `StatefulServiceBase.OnChangeRoleAsync()` metoden slutförs den `StatefulServiceBase.OnCloseAsync()` metoden anropas. Det här anropet är en ovanligt åsidosättning, men den är tillgänglig.
3. Efter `StatefulServiceBase.OnCloseAsync()` slutförs, serviceobjektet destructed.

## <a name="stateful-service-primary-swaps"></a>Tillståndskänslig service primära växlingar
När en tillståndskänslig service körs, de primära replikerna av de tillståndskänsliga tjänsterna har sina kommunikationslyssnarna öppnas och deras **RunAsync** anropade metoden. Sekundära repliker skapas, men det finns inga ytterligare anrop. När en tillståndskänslig service körs, repliken som för närvarande är den primära servern kan ändra. Vad innebär det i villkoren för händelserna livscykel som en replik kan se? Beteendet tillståndskänslig repliken ser beror på om det är repliken som nedgraderas eller uppgraderas under växlingen.

### <a name="for-the-primary-thats-demoted"></a>För den primära degraderas
För den primära repliken nedgraderas, måste Service Fabric replikeringen att avbryta behandlar meddelanden och avsluta alla bakgrundsjobbet avbrytas. Därför kan det här steget ser ut som om när tjänsten är avstängd. En skillnaden är att tjänsten inte är destructed eller stängts eftersom det fortfarande som en sekundär. Följande API: er anropas:

1. Parallellt:
    - Alla öppna lyssnare stängs. `ICommunicationListener.CloseAsync()`anropas på varje lyssnare.
    - Annullering-token som skickades till `RunAsync()` har avbrutits. En kontroll av token annullering `IsCancellationRequested` -egenskap returnerar true, och om den anropas token `ThrowIfCancellationRequested` metoden returnerar en `OperationCanceledException`.
2. Efter `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` också är klar tjänstens `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

### <a name="for-the-secondary-thats-promoted"></a>För den sekundära som höjs
Service Fabric måste dessutom den sekundära repliken som höjs så här startar lyssnar efter meddelanden på kabeln bakgrundsaktiviteter som behövs för att slutföra. Därför kan den här processen ser ut som den visades när tjänsten skapas, förutom att repliken själva finns redan. Följande API: er anropas:

1. Parallellt:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`har anropats och eventuella returneras lyssnare öppnas. `ICommunicationListener.OpenAsync()`anropas på varje lyssnare.
    - Tjänstens `StatefulServiceBase.RunAsync()` metoden anropas.
2. När alla replik lyssnarens `OpenAsync()` anropar Slutför och `RunAsync()` anropas, `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem vid avstängning av tjänst tillståndskänsliga och primära degradering
Service Fabric ändringar av en tillståndskänslig tjänst för en mängd olika skäl. De vanligaste är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [Programuppgradering](service-fabric-application-upgrade.md). Under dessa åtgärder (samt under normal drift avslutning, som du vill se om tjänsten har tagits bort), är det viktigt att tjänsten följer den `CancellationToken`. 

Tjänster som inte hanterar annullering korrekt kan få problem med flera. Dessa åtgärder är långsam eftersom Service Fabric väntar tjänster ska avslutas. Slutligen kan detta leda till misslyckade uppgraderingar tiden och återställa. Ta hänsyn till annullering token kan också medföra imbalanced kluster. Kluster bli Obalanserat eftersom noder hämta hot men kan inte vara genomförs tjänsterna eftersom det tar för lång tid att flytta dem någon annanstans. 

Eftersom tjänsterna är tillståndskänslig, det är också troligt att de använder den [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). När en primär degraderas i Service Fabric är en av de första sakerna som händer skrivåtkomst till tillståndet för underliggande återkallas. Detta leder till en annan uppsättning problem som kan påverka service lifecycle. Samlingar returnerade undantag baserat på tiden och om repliken flyttas eller stängs av. Sådana undantag ska hanteras på rätt sätt. Undantag från Service Fabric är indelade i permanent [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) och tillfälligt [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategorier. Permanent undantag ska loggas och utlöstes vid tillfälliga undantag kan göras baserat på vissa logik.

Hantering av undantag som härrör från användning av den `ReliableCollections` tillsammans med tjänsten Livscykelhändelser är en viktig del av testa och validera en tillförlitlig tjänst. Vi rekommenderar att du alltid köra tjänsten under belastning under uppgraderingar och [chaos testning](service-fabric-controlled-chaos.md) innan du distribuerar till produktionen. De grundläggande stegen för att säkerställa att din tjänst har implementerats korrekt och hanterar Livscykelhändelser på rätt sätt.


## <a name="notes-on-the-service-lifecycle"></a>Information om livscykeln för tjänsten
  - Både den `RunAsync()` metod och `CreateServiceReplicaListeners/CreateServiceInstanceListeners` anrop är valfria. En tjänst kan ha en av dem, båda eller inget. Till exempel om tjänsten gör allt arbete som svar på användaren anropar det behövs ingen att implementera `RunAsync()`. Endast kommunikationslyssnarna och deras associerade kod krävs. På liknande sätt att skapa och returnera kommunikationslyssnarna är valfritt, eftersom tjänsten kan ha endast bakgrundsjobbet att göra och så att endast måste implementera `RunAsync()`.
  - Det är giltigt för en tjänst att slutföra `RunAsync()` har och RETUR från den. Slutför är inte ett fel inträffar. Slutför `RunAsync()` betyder det att bakgrundsjobbet för tjänsten har slutförts. För tillståndskänsliga reliable services `RunAsync()` anropas igen om repliken är nedgraderas från primär till sekundär och sedan befordras till primära.
  - Om en tjänst avslutas från `RunAsync()` genom att vissa oväntat undantag utgör detta ett fel. Serviceobjektet stängs av och ett hälsotillstånd fel rapporteras.
  - Även om det finns ingen tidsgräns på returneras från dessa metoder kan du att förlora omedelbart möjligheten att skriva till tillförlitliga samlingar, och därför kan slutföra inte något verkligt arbete. Vi rekommenderar att du returnera så snabbt som möjligt vid mottagning av begäran om att avbryta. Om tjänsten inte svarar på dessa API-anrop i rimlig tid, Service Fabric framtvingar säga upp din tjänst. Vanligtvis sker detta endast under programuppgraderingar eller när en tjänst tas bort. Det här är 15 minuter som standard.
  - Fel i den `OnCloseAsync()` sökväg resultatet i `OnAbort()` anropas, vilket är ett sista chansen bästa möjlighet för tjänsten för att rensa och frigör alla resurser som de har begärts.

## <a name="next-steps"></a>Nästa steg
- [Introduktion till Reliable Services](service-fabric-reliable-services-introduction.md)
- [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Reliable Services avancerade användning](service-fabric-reliable-services-advanced-usage.md)
- [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
