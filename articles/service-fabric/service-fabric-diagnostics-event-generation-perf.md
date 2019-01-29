---
title: Azure Service Fabric prestanda övervakning | Microsoft Docs
description: Läs mer om prestandaräknare för övervakning och diagnostik för Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 114d71227e3cf3d0f235fc63135534b1030d4adc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188351"
---
# <a name="performance-metrics"></a>Prestandamått

Mått ska samlas in för att förstå prestanda för ditt kluster, samt de program som körs i den. För Service Fabric-kluster rekommenderar vi att samla in följande prestandaräknare.

## <a name="nodes"></a>Noder

Överväg att samla in följande prestandaräknare för att bättre förstå belastningen på varje dator och göra lämplig klusterskalning beslut för datorer i klustret.

| Räknaren kategori | Räknarnamn |
| --- | --- |
| Logisk Disk | Ledigt utrymme för logisk Disk |
| Fysisk disk (per Disk) | Genomsn. Läs diskkölängd |
| Fysisk disk (per Disk) | Genomsn. Diskkölängd för skrivning |
| Fysisk disk (per Disk) | Genomsn. S/diskläsning |
| Fysisk disk (per Disk) | Genomsn. S/diskskrivning |
| Fysisk disk (per Disk) | Diskläsningar/sek |
| Fysisk disk (per Disk) | Disk – lästa byte/sek |
| Fysisk disk (per Disk) | Diskskrivningar/sek |
| Fysisk disk (per Disk) | Disk – skrivna byte/sek |
| Minne | Tillgängliga megabyte |
| PagingFile | % Användning |
| Processor(total) | Tid i procent för processor |
| Process (per tjänst) | Tid i procent för processor |
| Process (per tjänst) | ID Process |
| Process (per tjänst) | Privata byte |
| Process (per tjänst) | Antal trådar |
| Process (per tjänst) | Virtuella byte |
| Process (per tjänst) | Arbetsminne |
| Process (per tjänst) | Privat arbetsminne |
| Nätverk Interface(all-instances) | Byte recd |
| Nätverk Interface(all-instances) | Skickade byte |
| Nätverk Interface(all-instances) | Totalt antal byte |
| Nätverk Interface(all-instances) | Kölängd-utdata |
| Nätverk Interface(all-instances) | Utgående paket som tagits bort |
| Nätverk Interface(all-instances) | Mottagna paket |
| Nätverk Interface(all-instances) | Utgående paket-fel |
| Nätverk Interface(all-instances) | Mottagna paket-fel |

## <a name="net-applications-and-services"></a>.NET-program och tjänster

Samla in följande räknare om du distribuerar .NET-tjänster till ditt kluster. 

| Räknaren kategori | Räknarnamn |
| --- | --- |
| .NET CLR-minne (per tjänst) | Process-ID |
| .NET CLR-minne (per tjänst) | # Totalt antal allokerade byte |
| .NET CLR-minne (per tjänst) | # Totalt antal reserverade byte |
| .NET CLR-minne (per tjänst) | Antal byte i alla Heapar |
| .NET CLR-minne (per tjänst) | Stora objekt. generace |
| .NET CLR-minne (per tjänst) | # GC hanterar |
| .NET CLR-minne (per tjänst) | Antal generation 0-insamlingar |
| .NET CLR-minne (per tjänst) | Antal generation 1-insamlingar |
| .NET CLR-minne (per tjänst) | Antal generation 2-insamlingar |
| .NET CLR-minne (per tjänst) | Tid i GC i procent |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric anpassade prestandaräknare

Service Fabric genererar en stor mängd anpassade prestandaräknare. Om du har installerat SDK kan du se en uttömmande lista på din Windows-dator i ditt program i Prestandaövervakaren (starta > Performance Monitor). 

I program som du distribuerar till ditt kluster om du använder Reliable Actors lägga till räknare från `Service Fabric Actor` och `Service Fabric Actor Method` kategorier (se [Service Fabric tillförlitliga aktörer-diagnostik](service-fabric-reliable-actors-diagnostics.md)).

Om du använder Reliable Services eller Service Remoting, på samma sätt har vi `Service Fabric Service` och `Service Fabric Service Method` räknare kategorier som du bör samla in prestandaräknare från Se [övervakning med service remoting](service-fabric-reliable-serviceremoting-diagnostics.md) och [tillförlitlig Services prestandaräknare](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Om du använder tillförlitliga samlingar, rekommenderar vi att lägga till den `Avg. Transaction ms/Commit` från den `Service Fabric Transactional Replicator` att samla in den genomsnittliga commit svarstiden per transaktion mått.


## <a name="next-steps"></a>Nästa steg

* Läs mer om [händelsegenerering på nivån plattform](service-fabric-diagnostics-event-generation-infra.md) i Service Fabric
* Samla in prestandavärden via [Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md)
