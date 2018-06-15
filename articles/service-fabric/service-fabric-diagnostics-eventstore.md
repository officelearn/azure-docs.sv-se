---
title: Azure Service Fabric händelse Store | Microsoft Docs
description: Lär dig mer om Azure Service Fabric-EventStore
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 1d235d5a75975c8d58cad1bbde0f16df2b1b3e7a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206524"
---
# <a name="eventstore-service-overview"></a>Översikt över EventStore

>[!NOTE]
>Från och med Service Fabric version 6.2. EventStore APIs finns för närvarande i förhandsvisning för Windows-kluster som kör bara på Azure. Vi arbetar på att överföra den här funktionen till Linux, samt våra fristående kluster.

## <a name="overview"></a>Översikt

Tjänsten EventStore är introduceras i version 6.2, ett alternativ för övervakning i Service Fabric som gör det möjligt för dig att förstå ditt kluster eller arbetsbelastningar vid en viss tidpunkt. Tjänsten EventStore exponerar Service Fabric-händelser via API: er som du kan göra anrop. Dessa EventStore APIs kan du fråga klustret direkt för att få diagnostikdata för entiteter i klustret och ska användas för att:
* Diagnostisera problem på utveckling eller testning eller som du kan använda en övervakning pipeline
* Bekräfta att hanteringsåtgärder som du tar i klustret bearbetas korrekt av klustret

En fullständig lista över händelser som är tillgängliga i EventStore finns [Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

Tjänsten EventStore kan efterfrågas för händelser som är tillgängliga för varje entitet och entitetstypen i klustret. Det innebär att du kan fråga efter händelser på följande nivåer.
* Kluster: alla kluster händelser
* Noder: alla nod händelser
* Nod: händelser som är specifika för en nod, baserat på `nodeName`
* Program: alla nivå programhändelser
* Program: händelser som är specifika för ett program
* Tjänster: händelser från alla tjänster i ditt kluster
* Tjänst: händelser från en specifik tjänst-
* Partitioner: händelser från alla partitioner
* Partition: händelser från en specifik partition
* Repliker: händelser från alla repliker / instanser
* Repliken: händelser från en specifik repliken / instansen


Tjänsten EventStore har också möjlighet att sammanföra händelserna i klustret. Genom att titta på händelser som har skrivits i taget från olika enheter som kan ha påverkat varandra, kan tjänsten EventStore länka dessa händelser för att identifiera orsaker till aktiviteter i klustret. Till exempel sker något av dina program blir ohälsosamt utan ändringar framkallat EventStore kommer också titta på andra händelser som visas av plattformen och kan ha att göra detta med en `NodeDown` händelse. Detta hjälper till med snabbare felidentifiering och rotorsaker analys.

Om du vill komma igång med att använda tjänsten EventStore finns [frågan EventStore APIs för klusterhändelser](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Nästa steg
* Översikt över övervakning och diagnostik i Service Fabric - [övervaknings- och diagnostikfunktionerna för Service Fabric](service-fabric-diagnostics-overview.md)
* Lär dig mer om hur du övervakar ditt kluster- [övervakning av kluster och plattform](service-fabric-diagnostics-event-generation-infra.md).