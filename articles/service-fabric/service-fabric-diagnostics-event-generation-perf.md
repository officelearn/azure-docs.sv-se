---
title: Prestandaövervakning av Azure Service Fabric
description: Lär dig mer om prestandaräknare för övervakning och diagnostik av Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464701"
---
# <a name="performance-metrics"></a>Prestandamått

Mått bör samlas in för att förstå prestanda för klustret samt de program som körs i det. För Service Fabric-kluster rekommenderar vi att du samlar in följande prestandaräknare.

## <a name="nodes"></a>Noder

För datorerna i klustret bör du överväga att samla in följande prestandaräknare för att bättre förstå belastningen på varje dator och fatta lämpliga beslut om klusterskalning.

| Räknare kategori | Motnamn |
| --- | --- |
| Logisk disk | Ledigt utrymme för logisk disk |
| PhysicalDisk(per Disk) | Genomsnittlig kölängd för diskläsning |
| PhysicalDisk(per Disk) | Genomsnittlig kö för diskskrivning |
| PhysicalDisk(per Disk) | Genomsnittlig disk sek/läs |
| PhysicalDisk(per Disk) | Genomsnittlig disk sek/skrivning |
| PhysicalDisk(per Disk) | Diskläsningar/sek |
| PhysicalDisk(per Disk) | Diskläs byte per sekund |
| PhysicalDisk(per Disk) | Diskskrivningar per sekund |
| PhysicalDisk(per Disk) | Byte för diskskrivning/sek |
| Minne | Tillgängliga MBytes |
| Växlingsfil | % användning |
| Processor(totalt) | % processortid |
| Process (per tjänst) | % processortid |
| Process (per tjänst) | ID-process |
| Process (per tjänst) | Privata byte |
| Process (per tjänst) | Antal trådar |
| Process (per tjänst) | Virtuella byte |
| Process (per tjänst) | Sidmängd |
| Process (per tjänst) | Arbetsset - Privat |
| Nätverksgränssnitt (alla instanser) | Byte recd |
| Nätverksgränssnitt (alla instanser) | Skickade byte |
| Nätverksgränssnitt (alla instanser) | Totalt antal byte |
| Nätverksgränssnitt (alla instanser) | Längd på utdatakö |
| Nätverksgränssnitt (alla instanser) | Slutgående paket som ignorerats |
| Nätverksgränssnitt (alla instanser) | Mottagna paket ignoreras |
| Nätverksgränssnitt (alla instanser) | Utgående paket-fel |
| Nätverksgränssnitt (alla instanser) | Mottagna paketfel |

## <a name="net-applications-and-services"></a>.NET-program och -tjänster

Samla in följande räknare om du distribuerar .NET-tjänster till klustret. 

| Räknare kategori | Motnamn |
| --- | --- |
| .NET CLR-minne (per tjänst) | Process-ID |
| .NET CLR-minne (per tjänst) | # Totalt genomförda byte |
| .NET CLR-minne (per tjänst) | # Totalt reserverade byte |
| .NET CLR-minne (per tjänst) | # Bytes i alla heaps |
| .NET CLR-minne (per tjänst) | Stor objekthögstorlek |
| .NET CLR-minne (per tjänst) | # GC Handtag |
| .NET CLR-minne (per tjänst) | # Gen 0 Samlingar |
| .NET CLR-minne (per tjänst) | # Gen 1 Samlingar |
| .NET CLR-minne (per tjänst) | # Gen 2 Samlingar |
| .NET CLR-minne (per tjänst) | % tid i GC |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric anpassade prestandaräknare

Service Fabric genererar en betydande mängd anpassade prestandaräknare. Om du har SDK installerat kan du se den omfattande listan på din Windows-dator i prestandaövervakaren (Start > Performance Monitor). 

I de program som du distribuerar till klustret, om `Service Fabric Actor` du `Service Fabric Actor Method` använder Reliable Actors, lägger till räknare från och kategorier (se [Service Fabric Reliable Actors Diagnostics](service-fabric-reliable-actors-diagnostics.md)).

Om du använder Reliable Services eller Service Remoting `Service Fabric Service` har vi på samma sätt och `Service Fabric Service Method` räknarkategorier som du bör samla in räknare från, se övervakning med [serviceåtermoting](service-fabric-reliable-serviceremoting-diagnostics.md) och tillförlitliga prestandaräknare för [tjänster.](service-fabric-reliable-services-diagnostics.md#performance-counters) 

Om du använder tillförlitliga samlingar `Avg. Transaction ms/Commit` rekommenderar `Service Fabric Transactional Replicator` vi att du lägger till från för att samla in den genomsnittliga genomförandesvarstiden per transaktionsmått.


## <a name="next-steps"></a>Nästa steg

* Läs mer om [händelsegenerering på plattformsnivå](service-fabric-diagnostics-event-generation-infra.md) i Service Fabric
* Samla in resultatmått via [Log Analytics-agent](service-fabric-diagnostics-oms-agent.md)
