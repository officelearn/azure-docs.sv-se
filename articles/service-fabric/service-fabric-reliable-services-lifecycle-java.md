---
title: Azure Service Fabric Reliable Services livscykel | Microsoft Docs
description: Läs mer om livscykeln för händelser i Service Fabric Reliable Services.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 9c2ce75b2bfb4b8ddab11ac94e5a8e50c2fad6ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="reliable-services-lifecycle"></a>Reliable Services-livscykel
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java i Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services är en av programmeringsmodeller finns i Azure Service Fabric. När utbildning om livscykeln för Reliable Services är det viktigaste att förstå enkel livscykel-händelser. Den exakta sorteringen av händelser beror på konfigurationsinformation. 

I allmänhet inkluderar livscykeln för Reliable Services följande händelser:

* Under start:
  * Tjänster skapas.
  * Tjänster har möjlighet att skapa och returnera noll eller flera lyssnare.
  * Alla returnerade lyssnare är öppna för kommunikation med tjänsten.
  * Tjänstens `runAsync` metoden anropas, så att tjänsten kan göra tidskrävande eller background arbete.
* Under stängs av:
  * Annullering token som skickades till `runAsync` har avbrutits, och lyssnarna har avslutats.
  * Serviceobjektet destructed.

Ordningen på händelser i Reliable Services kan ändras lite beroende på om tjänsten tillförlitliga är tillståndslösa och tillståndskänsliga. 

Du måste också lösa primära växlingen scenario för tillståndskänsliga tjänster. Under den här sekvensen rollen som primär överförs till en annan replik (eller kommer tillbaka) utan att tjänsten avslutas. 

Slutligen kan behöver du tänka på felet villkor.

## <a name="stateless-service-startup"></a>Tillståndslösa tjänsten startades
Livscykeln för tillståndslösa tjänsten är ganska enkelt. Här är ordningen för händelser:

1. Tjänsten har skapats.
2. Dessa händelser inträffar parallellt:
    - `StatelessService.createServiceInstanceListeners()` har anropats och eventuella returneras lyssnare öppnas. `CommunicationListener.openAsync()` anropas på varje lyssnare.
    - Tjänstens `runAsync` metod (`StatelessService.runAsync()`) anropas.
3. Om den finns i tjänstens egna `onOpenAsync` metoden anropas. Mer specifikt `StatelessService.onOpenAsync()` anropas. Det här är en ovanligt åsidosättning, men den är tillgänglig.

Det är viktigt att Observera att det finns ingen ordning mellan anropet för att skapa och öppna lyssnarna och anrop till `runAsync`. Lyssnarna kan öppna innan `runAsync` har startats. På liknande sätt `runAsync` kan anropas innan kommunikationslyssnarna är öppna eller innan de även har skapats. Om ingen synkronisering krävs, måste du göra det genom Implementeraren. Här följer några vanliga lösningar:

* Lyssnare fungerar ibland inte förrän annan information som har skapats eller andra arbetet. För tillståndslösa tjänster som fungerar normalt kan göras i tjänstens konstruktor. Det kan också ske under den `createServiceInstanceListeners()` anropar, eller som en del av konstruktionen för lyssnare sig själv.
* Ibland koden i `runAsync` startar inte förrän lyssnarna är öppna. I så fall krävs ytterligare samordning. En vanlig lösning är att lägga till en flagga i lyssnarna. Flaggan anger när lyssnarna är klar. Den `runAsync` metoden kontrollerar detta innan du fortsätter med det faktiska arbetet.

## <a name="stateless-service-shutdown"></a>Tillståndslösa tjänsten avstängning
När du stänger en tillståndslös tjänst är samma mönster följt, men i omvänd:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare stängs. `CommunicationListener.closeAsync()` anropas på varje lyssnare.
    - Annullering token som skickades till `runAsync()` har avbrutits. Kontrollera token annullering `isCancelled` egenskapen returnerar `true`, och om den anropas token `throwIfCancellationRequested` metoden returnerar en `CancellationException`.
2. När `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också är klar tjänstens `StatelessService.onCloseAsync()` metoden anropas, om den finns. Igen, det är inte en gemensam åsidosättning, men den kan användas för att på ett säkert sätt stänga resurser stoppa behandling i bakgrunden, Slutför sparar externa tillstånd eller stänga ned befintliga anslutningar.
3. Efter `StatelessService.onCloseAsync()` slutförs, serviceobjektet destructed.

## <a name="stateful-service-startup"></a>Tillståndskänsliga startades
Tillståndskänsliga tjänster ha ett mönster som liknar tillståndslösa tjänster med några ändringar.  Här är ordningen för händelser för att starta en tillståndskänslig tjänst:

1. Tjänsten har skapats.
2. `StatefulServiceBase.onOpenAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.
3. Dessa händelser inträffar parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()` anropas. 
      - Om tjänsten är en primär service, öppnas alla returnerade lyssnare. `CommunicationListener.openAsync()` anropas på varje lyssnare.
      - Om tjänsten är en sekundär service kan endast lyssnare markerad som `listenOnSecondary = true` öppnas. Med lyssnare som är öppna på sekundärservrar är mindre vanliga.
    - Om tjänsten är en primär, tjänstens `StatefulServiceBase.runAsync()` metoden anropas.
4. När alla replik lyssnarens `openAsync()` anropar Slutför och `runAsync()` anropas, `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

Liksom för tillståndslösa tjänster i tillståndskänslig service, det finns inga samordning mellan den ordning som lyssnarna skapas och öppnas och när `runAsync` anropas. Om du behöver samordning är ungefär lösningarna. Men det finns ett ytterligare fall för stateful-tjänsten. Säg att anrop som når kommunikationslyssnarna kräver information som sparas i vissa [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). Eftersom kommunikationslyssnarna kan öppna innan den tillförlitliga samlingar är läsbart eller skrivbart och före `runAsync` startar vissa ytterligare samordning krävs. Den enklaste och de vanligaste lösningen är för kommunikationslyssnarna att returnera en felkod. Klienten använder felkoden för att försöka.

## <a name="stateful-service-shutdown"></a>Tillståndskänslig service avstängning
Precis som tillståndslösa tjänster Livscykelhändelser vid avstängningen är samma som under starten men ångras. När en tillståndskänslig service stängs av, händer följande:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare stängs. `CommunicationListener.closeAsync()` anropas på varje lyssnare.
    - Annullering token som skickades till `runAsync()` avbryts. Ett anrop till token för annullering `isCancelled()` metoden returnerar `true`, och om den anropas token `throwIfCancellationRequested()` metoden returnerar en `OperationCanceledException`.
2. Efter `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också är klar tjänstens `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

   > [!NOTE]  
   > Väntar på att `runAsync` ska slutföras krävs endast om repliken är en primär replik.

3. Efter den `StatefulServiceBase.onChangeRoleAsync()` metoden slutförs den `StatefulServiceBase.onCloseAsync()` metoden anropas. Det här anropet är en ovanligt åsidosättning, men den är tillgänglig.
3. Efter `StatefulServiceBase.onCloseAsync()` slutförs, serviceobjektet destructed.

## <a name="stateful-service-primary-swaps"></a>Tillståndskänslig service primära växlingar
När en tillståndskänslig service körs, kommunikationslyssnarna öppnas och `runAsync` metoden anropas endast för de primära replikerna av de tillståndskänsliga tjänsterna. Sekundära repliker skapas, men det finns inga ytterligare anrop. När en tillståndskänslig service körs, repliken som för närvarande är den primära servern kan ändra. Livscykel-händelser som en tillståndskänslig replik ser beror på om det är repliken som nedgraderas eller uppgraderas under växlingen.

### <a name="for-the-demoted-primary"></a>För sänkta primär
Service Fabric måste den primära repliken nedgraderas för att stoppa behandlar meddelanden och stoppa alla bakgrundsjobbet. Det här steget liknar när tjänsten är avstängd. En skillnaden är att tjänsten inte är destructed eller closed, eftersom det fortfarande som en sekundär. Följande sker:

1. Dessa händelser inträffar parallellt:
    - Alla öppna lyssnare stängs. `CommunicationListener.closeAsync()` anropas på varje lyssnare.
    - Annullering token som skickades till `runAsync()` har avbrutits. En kontroll av token annullering `isCancelled()` metoden returnerar `true`. Om den anropas token `throwIfCancellationRequested()` metoden returnerar en `OperationCanceledException`.
2. Efter `closeAsync()` har slutförts på varje lyssnare och `runAsync()` också är klar tjänstens `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

### <a name="for-the-promoted-secondary"></a>För den upphöjda sekundärt
Service Fabric måste dessutom den sekundära repliken som höjs för att starta lyssnar efter meddelanden på nätverket och starta alla bakgrundsaktiviteter som behöver slutföras. Den här processen liknar när tjänsten skapas. Skillnaden är att de själva finns redan. Följande sker:

1. Dessa händelser inträffar parallellt:
    - `StatefulServiceBase.createServiceReplicaListeners()` har anropats och eventuella returneras lyssnare öppnas. `CommunicationListener.openAsync()` anropas på varje lyssnare.
    - Tjänstens `StatefulServiceBase.runAsync()` metoden anropas.
2. När alla replik lyssnarens `openAsync()` anropar Slutför och `runAsync()` anropas, `StatefulServiceBase.onChangeRoleAsync()` anropas. Det här anropet ofta inte har åsidosatts i tjänsten.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Vanliga problem vid avstängning av tjänst tillståndskänsliga och primära degradering
Service Fabric ändringar av en tillståndskänslig service av flera skäl. De vanligaste orsakerna är [kluster ombalansering](service-fabric-cluster-resource-manager-balancing.md) och [Programuppgradering](service-fabric-application-upgrade.md). Under dessa åtgärder är det viktigt att tjänsten respekterar det `cancellationToken`. Detta gäller även under normal drift avstängning, till exempel om tjänsten har tagits bort.

Tjänster som inte hanteras att korrekt kan få problem med flera. Dessa åtgärder är långsam eftersom Service Fabric väntar tjänster ska avslutas. Detta kan slutligen leda till misslyckade uppgraderingar som timeout och återställning. Underlåtenhet att respektera annullering token kan även orsaka imbalanced kluster. Kluster bli Obalanserat eftersom noder hämta hot. Tjänsterna kan inte vara genomförs eftersom det tar för lång tid att flytta dem någon annanstans. 

Eftersom tjänsterna är tillståndskänslig, är det också sannolikt att tjänsterna använder [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). När en primär degraderas i Service Fabric är en av de första sakerna som händer skrivåtkomst till tillståndet för underliggande återkallas. Detta leder till en annan uppsättning problem som kan påverka service lifecycle. Samlingar returnerade undantag baserat på tiden och om repliken flyttas eller stängs av. Det är viktigt att hantera dessa korrekt. 

Undantag från Service Fabric är antingen permanenta [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) eller tillfälligt [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception._fabric_transient_exception). Permanent undantag ska loggas och utlöstes. Tillfälligt undantag kan göras baserat på logik.

En viktig del av testa och validera Reliable Services hanterar undantag som kommer från med hjälp av den `ReliableCollections` tillsammans med tjänsten Livscykelhändelser. Vi rekommenderar att du alltid köra tjänsten under belastning. Du bör också utföra uppgraderingar och [chaos testning](service-fabric-controlled-chaos.md) innan du distribuerar till produktionen. De grundläggande stegen för att säkerställa att din tjänst har implementerats korrekt och att den hanterar Livscykelhändelser på rätt sätt.

## <a name="notes-on-service-lifecycle"></a>Information om tjänsten livscykel
* Både den `runAsync()` metod och `createServiceInstanceListeners/createServiceReplicaListeners` anrop är valfria. En tjänst kan ha en båda eller inget. Till exempel om tjänsten gör allt arbete som svar på användaren anropar det behövs ingen att implementera `runAsync()`. Endast kommunikationslyssnarna och deras associerade kod krävs.  På liknande sätt är att skapa och returnera kommunikationslyssnarna valfritt. Tjänsten kan ha bakgrundsjobbet att göra, så den behöver implementera `runAsync()`.
* Det är giltigt för en tjänst att slutföra `runAsync()` har och RETUR från den. Detta är inte anses vara ett fel inträffar. Den representerar bakgrundsjobbet tjänsten avslutas. För tillståndskänsliga Reliable Services `runAsync()` skulle anropas igen om tjänsten degraderas från primära och sedan befordras till primära.
* Om en tjänst avslutas från `runAsync()` genom att vissa oväntade undantag, detta är ett fel. Serviceobjektet stängs av och ett hälsotillstånd fel rapporteras.
* Även om det finns ingen tidsgräns på returneras från dessa metoder kan du omedelbart att förlora möjligheten att skriva. Därför kan slutföra du inte något verkligt arbete. Vi rekommenderar att du returnera så snabbt som möjligt vid mottagning av begäran om att avbryta. Om tjänsten inte svarar till dessa API-anrop i rimlig tid, kan Service Fabric framtvingar stänga av tjänsten. Detta sker vanligtvis bara under programuppgraderingar eller när en tjänst tas bort. Det här är 15 minuter som standard.
* Fel i den `onCloseAsync()` sökväg resultatet i `onAbort()` anropas. Det här anropet är en sista chansen, bästa möjligheten för tjänsten för att rensa och frigör alla resurser som de har begärts. Detta kallas vanligtvis när ett permanent fel identifieras på noden, eller när Service Fabric inte kan på ett tillförlitligt sätt hantera livscykeln för instansen för tjänsten på grund av ett internt fel.
* `OnChangeRoleAsync()` anropas när repliken tillståndskänslig service är rollen ändras, till exempel till primär eller sekundär. Primära repliker får skrivstatus (ska kunna skapa och skriva till tillförlitliga samlingar). Sekundära repliker ges Lässtatus (kan bara läsa från befintliga tillförlitliga samlingar). De flesta arbetsobjekt i en tillståndskänslig service utförs på den primära repliken. Sekundära repliker kan utföra skrivskyddade validering rapportgenerering, datautvinning eller andra skrivskyddade jobb.

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Reliable Services](service-fabric-reliable-services-introduction.md)
* [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start-java.md)

