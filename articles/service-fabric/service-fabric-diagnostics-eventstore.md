---
title: Azure Service Fabric händelse Store | Microsoft Docs
description: Lär dig mer om Azure Service Fabric EventStore
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: cd58e24a51b153d6bf217f7d32a82e882183ed73
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290679"
---
# <a name="eventstore-service-overview"></a>Översikt över EventStore-tjänsten

## <a name="overview"></a>Översikt

Tjänsten EventStore är introduceras i version 6.2, ett alternativ för övervakning i Service Fabric, som gör det möjligt för dig att förstå tillståndet för dina kluster eller arbetsbelastningar vid en viss tidpunkt. EventStore-tjänsten exponerar Service Fabric händelser via en uppsättning API: er (komma åt via REST-slutpunkter eller via klientbiblioteket). Dessa EventStore APIs kan du fråga klustret direkt för att få diagnostikdata för entiteter i ditt kluster och bör användas för att:

* Diagnostisera problem på utveckling eller testning eller som du kan använda en övervakningspipelinen
* Bekräfta att hanteringsåtgärder som du vidtar i ditt kluster bearbetas korrekt av ditt kluster

Om du vill se en fullständig lista över händelser som är tillgängliga i EventStore Se [Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Från och med Service Fabric version 6.2. the EventStore APIs finns för närvarande i förhandsversion för Windows-kluster som körs på Azure endast. Vi arbetar på att porta den här funktionen på Linux, samt våra fristående kluster.

Tjänsten EventStore kan frågas för händelser som är tillgängliga för varje entitet och enhetstyp i klustret. Det innebär att du kan fråga efter händelser på följande nivåer;
* Kluster: alla kluster händelser på programnivå
* Noder: alla noden händelser på programnivå
* Nod: händelser som är specifika för en nod, baserat på `nodeName`
* Program: alla händelser på programnivå
* Program: händelser som är specifika för ett program
* Tjänster: händelser från alla tjänster i ditt kluster
* Tjänsten: händelser från en specifik tjänst
* Partitioner: händelser från alla partitioner
* Partition: händelser från en specifik partition
* Repliker: händelser från alla repliker / -instanser
* Replikering: händelser från en specifik replik / instans


Tjänsten EventStore har också möjlighet att korrelera händelser i klustret. Genom att titta på händelser som har skrivits på samma gång från olika entiteter som kan påverka varandra, kan tjänsten EventStore att länka dessa händelser för att identifiera orsaker till aktiviteter i ditt kluster. Till exempel sker något av dina program blivit defekt utan framkallat ändringar i EventStore ska också titta på andra händelser som exponeras av plattformen och kan kombinera detta med en `NodeDown` händelse. Detta underlättar snabbare felsökning och analys av rotorsaker.

Vi rekommenderar att du använder EventStore för snabb analys och för att få en ögonblicksbild uppfattning av hur klustret fungerar och om händer saker som förväntat.

Kom igång med att använda tjänsten EventStore, se [fråga EventStore APIs för klusterhändelser](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Nästa steg
* Översikt över övervakning och diagnostik i Service Fabric - [övervakning och diagnostik för Service Fabric](service-fabric-diagnostics-overview.md)
* Läs mer om hur du övervakar ditt kluster- [kluster och plattformen](service-fabric-diagnostics-event-generation-infra.md).