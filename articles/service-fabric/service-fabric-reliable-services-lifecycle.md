---
title: Översikt över livscykeln för Azure Service Fabric Reliable Services | Microsoft Docs
description: Lär dig mer om för olika Livscykelhändelser i Service Fabric Reliable Services
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek;
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ebc7aec63b34630b606178aa17e2ae7fdd0fc87f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723581"
---
# <a name="reliable-services-lifecycle-overview"></a>Översikt av programlivscykel Reliable Services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

När du funderar livscykler för Azure Service Fabric Reliable Services är grunderna i livscykeln viktigast. I allmänhet omfattar livscykeln följande:

- Under start:
  - Tjänster skapas.
  - Tjänsterna har möjlighet att konstruera och returnerar noll eller flera lyssnare.
  - Alla returnerade lyssnare öppnas, kommunikation med tjänsten.
  - Tjänstens **RunAsync** metoden anropas, vilket gör att tjänsten för att göra långvariga aktiviteter eller bakgrundsjobbet.
- Under avstängning:
  - Annulleringen token som skickas till **RunAsync** har avbrutits och lyssnarna är stängda.
  - När lyssnarna stängt destructed själva serviceobjektet.

Det finns information kring exakta sorteringen av dessa händelser. Ordningen för händelser kan ändras lite beroende på om tillförlitlig tjänst är tillståndslösa eller tillståndskänsliga. Dessutom måste vi hantera med primära swap-scenario för tillståndskänsliga tjänster. Under den här sekvensen rollen som primär överförs till en annan replik (eller kommer tillbaka) utan att tjänsten avslutas. Slutligen måste vi tänka fel eller fel villkor.

## <a name="stateless-service-startup"></a>Tillståndslösa tjänsten startades
Det är enkelt att livscykeln för en tillståndslös tjänst. Här är händelseordningen:

1. Tjänsten skapas.
2. Sedan parallellt saker två:
    - `StatelessService.CreateServiceInstanceListeners()` har anropats och eventuella returneras lyssnare är öppna. `ICommunicationListener.OpenAsync()` anropas på varje lyssnare.
    - Tjänstens `StatelessService.RunAsync()` metoden anropas.
3. Om den finns på tjänstens `StatelessService.OnOpenAsync()` metoden anropas. Det här anropet är en ovanlig åsidosättning, men den är tillgänglig. Du kan starta utökade service initieringsaktiviteterna just nu.

Tänk på att det finns ingen ordning mellan anrop för att skapa och öppna lyssnarna och **RunAsync**. Lyssnarna kan öppna innan **RunAsync** har startats. På samma sätt kan du anropa **RunAsync** innan kommunikationslyssnarna är öppna eller även konstruerade. Om ingen synkronisering krävs, är det värt som en övning Implementeraren. Här följer ett antal vanliga lösningar:

  - Lyssnare fungerar ibland inte förrän vissa andra information skapas eller jobbet är klart. För tillståndslösa tjänster fungerar vanligtvis att göra på andra platser, till exempel följande: 
    - I tjänstens konstruktorn.
    - Under den `CreateServiceInstanceListeners()` anropa.
    - Som en del av produktionen av lyssnaren själva.
  - Ibland koden i **RunAsync** inte startar förrän lyssnarna är öppna. I så fall krävs ytterligare samordning. En vanlig lösning är att det finns en flagga i lyssnarna som anger när de har slutförts. Den här flaggan sedan har checkats in **RunAsync** innan du fortsätter att faktiskt arbete.

## <a name="stateless-service-shutdown"></a>Tillståndslös tjänst avstängning
För att stänga en tillståndslös tjänst följs samma mönster just-in-omvänd:

1. Parallellt:
    - Alla öppna lyssnare stängs. `ICommunicationListener.CloseAsync()` anropas på varje lyssnare.
    - Annulleringen token som skickas till `RunAsync()` har avbrutits. En kontroll av token annullering `IsCancellationRequested` egenskapen returnerar SANT, och om den anropas token `ThrowIfCancellationRequested` metoden returnerar en `OperationCanceledException`.
2. Efter `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` också är klar tjänstens `StatelessService.OnCloseAsync()` metoden anropas, om sådan finns.  OnCloseAsync anropas när tillståndslösa tjänstinstansen kommer att vara stängs. Detta kan inträffa när tjänstens kod håller på att uppgraderas, tjänstinstansen flyttas på grund av belastningsutjämning eller ett tillfälligt fel identifieras. Det är ovanligt att åsidosätta `StatelessService.OnCloseAsync()`, men den kan användas för att Stäng resurser, stoppa behandling i bakgrunden, Slutför sparar externa tillstånd i eller stänga ned befintliga anslutningar på ett säkert sätt.
3. Efter `StatelessService.OnCloseAsync()` har slutförts serviceobjektet destructed.

## <a name="stateful-service-startup"></a>Tillståndskänsliga tjänsten startades
Tillståndskänsliga tjänster har ett liknande mönster för tillståndslösa tjänster med några ändringar. För att starta en tillståndskänslig tjänst, är ordningen för händelser på följande sätt:

1. Tjänsten skapas.
2. `StatefulServiceBase.OnOpenAsync()` kallas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.
3. Följande saker parallellt:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` anropas. 
      - Om tjänsten är en primär tjänst, öppnas alla returnerade lyssnare. `ICommunicationListener.OpenAsync()` anropas på varje lyssnare.
      - Om tjänsten är en sekundär service kan endast dessa lyssnare märkta `ListenOnSecondary = true` öppnas. Det är mindre vanligt att ha lyssnare som är öppna på sekundära databaser.
    - Om tjänsten för närvarande är en primär, tjänstens `StatefulServiceBase.RunAsync()` metoden anropas.
4. När du har alla repliken lyssningsfunktionens `OpenAsync()` anropar Slutför och `RunAsync()` anropas `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.

Liknar tillståndslösa tjänster, det finns inga samordning mellan den ordning i vilken lyssnarna skapas och öppnas och när **RunAsync** anropas. Om du behöver samordning är ungefär samma lösningarna. Det finns en ytterligare fallet för tillståndskänsliga tjänster. Säger att de anrop som når kommunikationslyssnarna kräver information sparas i vissa [Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Eftersom kommunikationslyssnarna kunde öppna innan tillförlitliga samlingar är läsbart eller skrivbart och innan **RunAsync** kunde starta vissa ytterligare samordning krävs. Den enklaste och vanligaste lösningen är att returnera en felkod som klienten använder för att försöka kommunikationslyssnarna.

## <a name="stateful-service-shutdown"></a>Avstängning av tillståndskänslig tjänst
Precis som tillståndslösa tjänster Livscykelhändelser vid avstängningen är samma som under starten, men ångras. När en tillståndskänslig tjänst har stängts ned, händer följande:

1. Parallellt:
    - Alla öppna lyssnare stängs. `ICommunicationListener.CloseAsync()` anropas på varje lyssnare.
    - Annulleringen token som skickas till `RunAsync()` har avbrutits. En kontroll av token annullering `IsCancellationRequested` egenskapen returnerar SANT, och om den anropas token `ThrowIfCancellationRequested` metoden returnerar en `OperationCanceledException`.
2. Efter `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` också är klar tjänstens `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.

   > [!NOTE]  
   > Behovet av att vänta tills **RunAsync** ska slutföras krävs bara om repliken är en primär replik.

3. Efter den `StatefulServiceBase.OnChangeRoleAsync()` metoden har slutförts i `StatefulServiceBase.OnCloseAsync()` metoden anropas. Det här anropet är en ovanlig åsidosättning, men den är tillgänglig.
3. Efter `StatefulServiceBase.OnCloseAsync()` har slutförts serviceobjektet destructed.

## <a name="stateful-service-primary-swaps"></a>Tillståndskänslig tjänst primära växlingar
När en tillståndskänslig tjänst körs de primära replikerna av de tillståndskänsliga tjänsterna har sina kommunikationslyssnarna öppnas och deras **RunAsync** metod som heter. Sekundära repliker skapas, men det finns inga ytterligare anrop. När en tillståndskänslig tjänst körs, kan ändra som för närvarande är primärt repliken på grund av fel eller belastningsutjämning optimering-kluster. Vad betyder detta i Användarvillkor Livscykelhändelser för en replik kan se? Beteendet tillståndskänsliga repliken ser beror på om det är repliken att degraderas eller uppgraderas under växlingen.

### <a name="for-the-primary-thats-demoted"></a>För den primära degraderas
Service Fabric måste den här repliken för att avbryta bearbetat meddelandena och avsluta alla bakgrundsjobbet som det går för den primära repliken degraderas. Därför kan det här steget ser ut som den visades när tjänsten är avstängd. En skillnad är att tjänsten inte är destructed eller stängts eftersom den fortfarande som en sekundär. Följande API: er anropas:

1. Parallellt:
    - Alla öppna lyssnare stängs. `ICommunicationListener.CloseAsync()` anropas på varje lyssnare.
    - Annulleringen token som skickas till `RunAsync()` har avbrutits. En kontroll av token annullering `IsCancellationRequested` egenskapen returnerar SANT, och om den anropas token `ThrowIfCancellationRequested` metoden returnerar en `OperationCanceledException`.
2. Efter `CloseAsync()` har slutförts på varje lyssnare och `RunAsync()` också är klar tjänstens `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.

### <a name="for-the-secondary-thats-promoted"></a>För den sekundära som höjs
Service Fabric måste på samma sätt kan den sekundära repliken befordras för att starta lyssnar efter meddelanden i ledningen och starta alla bakgrundsaktiviteter som behövs för att slutföra. Därför kan den här processen ser ut som den visades när tjänsten skapas, förutom att repliken själva finns redan. Följande API: er anropas:

1. Parallellt:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` har anropats och eventuella returneras lyssnare är öppna. `ICommunicationListener.OpenAsync()` anropas på varje lyssnare.
    - Tjänstens `StatefulServiceBase.RunAsync()` metoden anropas.
2. När du har alla repliken lyssningsfunktionens `OpenAsync()` anropar Slutför och `RunAsync()` anropas `StatefulServiceBase.OnChangeRoleAsync()` anropas. Det här anropet är vanligtvis inte åsidosätts i tjänsten.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem vid avstängning av tillståndskänslig tjänst eller primära degradering
Service Fabric ändras av en tillståndskänslig tjänst av olika skäl. De vanligaste är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [Programuppgradering](service-fabric-application-upgrade.md). Vid dessa åtgärder (även under normal drift avstängning, som ser du om tjänsten har tagits bort), är det viktigt att tjänsten följer den `CancellationToken`. 

Tjänster som inte hanterar avbryta korrekt kan uppleva flera problem. De här åtgärderna är långsam eftersom Service Fabric väntar för tjänster att stoppa ett smidigt sätt. Slutligen kan detta leda till misslyckade uppgraderingar tiden och återställa. Det gick inte att respektera annullering token kan även orsaka imbalanced kluster. Kluster blir obalanserade eftersom noder får heta, men det går inte att vara genomförs tjänsterna eftersom det tar för lång tid att flytta dem någon annanstans. 

Eftersom tjänsterna är tillståndskänsliga, är det också sannolikt att de använder den [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). När en primär degraderas i Service Fabric är en av de första sakerna som sker att skrivbehörighet till det underliggande tillståndet har återkallats. Detta leder till en annan uppsättning av problem som kan påverka service-livscykeln. Samlingar returnerade undantag baserat på tiden och om repliken flyttas eller avstängning. De här undantagen bör hanteras korrekt. Undantag från Service Fabric faller permanent [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) och tillfälliga [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategorier. Permanent undantag ska loggas och uppstod medan tillfälliga undantag kan göras baserat på logiken försök igen.

Hantering av undantag som kommer från användning av den `ReliableCollections` tillsammans med Livscykelhändelser för tjänsten är en viktig del av testar och validerar en tillförlitlig tjänst. Vi rekommenderar att du alltid köra din tjänst under belastning när du utför uppgraderingar och [chaos testning](service-fabric-controlled-chaos.md) innan du distribuerar till produktion. De grundläggande stegen för att säkerställa att din tjänst har implementerats korrekt och hanterar Livscykelhändelser för korrekt.


## <a name="notes-on-the-service-lifecycle"></a>Information om livscykeln för tjänsten
  - Både den `RunAsync()` metod och `CreateServiceReplicaListeners/CreateServiceInstanceListeners` anrop är valfria. En tjänst kan ha en av dem, båda eller inget. Till exempel om tjänsten gör allt arbete som svar på användaren anrop, finns inget behov av den för att implementera `RunAsync()`. Endast kommunikationslyssnarna och deras associerade kod krävs. På samma sätt skapa och returnera kommunikationslyssnarna är valfritt, eftersom tjänsten kan ha endast bakgrundsjobbet att göra så att endast behov och implementera `RunAsync()`.
  - Den är giltig för en tjänst för att slutföra `RunAsync()` har och gå tillbaka från den. Du har slutfört är inte ett fel inträffar. Du har slutfört `RunAsync()` betyder det att BITS verk som tillhör tjänsten har slutförts. För tillståndskänsliga reliable services `RunAsync()` anropas igen om repliken är nedgraderas från primära till sekundära och sedan befordras till primär.
  - Om en tjänst avslutas från `RunAsync()` genom att utlösa ett oväntat undantag, utgör detta ett fel. Objektet stängs av och ett hälsotillstånd fel rapporteras.
  - Även om det finns ingen tidsgräns på återgång från dessa metoder kan du förlora omedelbart möjligheten att skriva till tillförlitliga samlingar och därför kan inte slutföra några riktiga arbetet. Vi rekommenderar att du kommer tillbaka så snabbt som möjligt när tas emot på avbrottsbegäran. Om tjänsten inte svarar till dessa API-anrop i rimlig tid, avsluta Service Fabric tvång din tjänst. Vanligtvis det här inträffar bara under programuppgraderingar eller när en tjänst tas bort. Den här timeouten är 15 minuter som standard.
  - Fel i den `OnCloseAsync()` leda till sökvägen `OnAbort()` som anropas, vilket är en sista chansen mån möjlighet för tjänsten att rensa och frigöra resurser som de har ägs. Detta kallas vanligtvis när ett permanent fel upptäcks på noden, eller när Service Fabric på ett tillförlitligt sätt inte kan hantera den tjänstinstansen livscykel på grund av ett internt fel.
  - `OnChangeRoleAsync()` anropas när repliken tillståndskänslig tjänst är ändrar roll, till exempel till primär eller sekundär. Primära repliker får skrivstatus (ska kunna skapa och skriva till tillförlitliga samlingar). Sekundära repliker ges Lässtatus (kan bara läsa från befintliga tillförlitliga samlingar). De flesta arbete i en tillståndskänslig tjänst utförs på den primära repliken. Sekundära repliker kan utföra skrivskyddade verifiering, rapportgenerering, datautvinning eller andra skrivskyddade jobb.

## <a name="next-steps"></a>Nästa steg
- [Introduktion till Reliable Services](service-fabric-reliable-services-introduction.md)
- [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
