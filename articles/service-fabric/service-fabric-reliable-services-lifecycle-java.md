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
ms.openlocfilehash: 80eb68346dd05c256c60725eb082aa0651fe7cbd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
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

## <a name="notes-on-service-lifecycle"></a>Information om tjänsten livscykel
* Både den `runAsync()` metod och `createServiceInstanceListeners` anrop är valfria. En tjänst kan ha ett av dem, båda eller inget. Till exempel om tjänsten gör allt arbete som svar på användaren anropar det behövs ingen att implementera `runAsync()`. Endast kommunikationslyssnarna och deras associerade kod krävs. På liknande sätt är att skapa och returnera kommunikationslyssnarna valfritt, eftersom tjänsten kan ha endast bakgrundsjobbet att göra och så att endast måste implementera`runAsync()`
* Det är giltigt för en tjänst att slutföra `runAsync()` har och RETUR från den. Detta anses inte vara ett fel inträffar och representerar bakgrundsjobbet service slutföra. För tillståndskänsliga reliable services `runAsync()` skulle anropas igen om tjänsten har nedgraderas från primära och sedan befordras till primära.
* Om en tjänst avslutas från `runAsync()` genom att vissa oväntade undantag, detta är ett fel och serviceobjektet stängs av och ett hälsotillstånd fel rapporteras.
* När det finns ingen tidsgräns på returneras från dessa metoder kan du att förlora omedelbart möjligheten att skriva och därför slutföra inte något verkligt arbete. Vi rekommenderar att du returnera så snabbt som möjligt vid mottagning av begäran om att avbryta. Om tjänsten inte svarar på dessa API-anrop i rimlig tid Service Fabric tvång att avbryta tjänsten. Vanligtvis sker detta endast under programuppgraderingar eller när en tjänst tas bort. Det här är 15 minuter som standard.
* Fel i den `onCloseAsync()` sökväg resultatet i `onAbort()` anropas som är ett sista chansen bästa möjligheten för tjänsten för att rensa upp och frigör alla resurser som de har begärts.

> [!NOTE]
> Tillståndskänsliga reliable services stöds inte i java ännu.
>
>

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Reliable Services](service-fabric-reliable-services-introduction.md)
* [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Reliable Services avancerade användning](service-fabric-reliable-services-advanced-usage.md)
