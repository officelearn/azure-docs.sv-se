---
title: Prestanda övervakning av Azure Service Fabric
description: Lär dig mer om prestanda räknare för övervakning och diagnostik av Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464701"
---
# <a name="performance-metrics"></a>Prestandamått

Mått ska samlas in för att förstå klustrets prestanda samt de program som körs i det. För Service Fabric kluster rekommenderar vi att du samlar in följande prestanda räknare.

## <a name="nodes"></a>Noder

För datorerna i klustret kan du överväga att samla in följande prestanda räknare för att bättre förstå belastningen på varje dator och fatta lämpliga beslut om kluster skalning.

| Räknar kategori | Räknar namn |
| --- | --- |
| Logisk Disk | Ledigt utrymme för logisk disk |
| Fysisk disk (per disk) | Genomsnittlig längd på disk läsnings kön |
| Fysisk disk (per disk) | Genomsnittlig längd på disk skrivnings kön |
| Fysisk disk (per disk) | Medel s/disk läsning |
| Fysisk disk (per disk) | Medel s/disk skrivning |
| Fysisk disk (per disk) | Diskläsningar/sek |
| Fysisk disk (per disk) | Disk – lästa byte/sek |
| Fysisk disk (per disk) | Diskskrivningar/sek |
| Fysisk disk (per disk) | Disk – skrivna byte/sek |
| Minne | Tillgängliga megabyte |
| PagingFile | Användning av% |
| Processor (totalt) | Tid i procent för processor |
| Process (per tjänst) | Tid i procent för processor |
| Process (per tjänst) | Process-ID |
| Process (per tjänst) | Privata byte |
| Process (per tjänst) | Räkning av trådar |
| Process (per tjänst) | Virtuella byte |
| Process (per tjänst) | Sidmängd |
| Process (per tjänst) | Arbets minne – privat |
| Nätverks gränssnitt (alla instanser) | Byte recd |
| Nätverks gränssnitt (alla instanser) | Skickade byte |
| Nätverks gränssnitt (alla instanser) | Totalt antal byte |
| Nätverks gränssnitt (alla instanser) | Kölängd för utdata |
| Nätverks gränssnitt (alla instanser) | Utgående paket som ignorerats |
| Nätverks gränssnitt (alla instanser) | Mottagna paket som tagits bort |
| Nätverks gränssnitt (alla instanser) | Utgående paket fel |
| Nätverks gränssnitt (alla instanser) | Mottagna paket-fel |

## <a name="net-applications-and-services"></a>.NET-program och-tjänster

Samla in följande räknare om du distribuerar .NET-tjänster till klustret. 

| Räknar kategori | Räknar namn |
| --- | --- |
| .NET CLR-minne (per tjänst) | Process-ID |
| .NET CLR-minne (per tjänst) | Totalt antal allokerade byte |
| .NET CLR-minne (per tjänst) | Totalt antal reserverade byte |
| .NET CLR-minne (per tjänst) | Antal byte i alla heapar |
| .NET CLR-minne (per tjänst) | Heap-storlek för stora objekt |
| .NET CLR-minne (per tjänst) | Antal GC-referenser |
| .NET CLR-minne (per tjänst) | # Generation 0-samlingar |
| .NET CLR-minne (per tjänst) | # Gen 1-samlingar |
| .NET CLR-minne (per tjänst) | # Gen 2-samlingar |
| .NET CLR-minne (per tjänst) | Tid i GC i procent |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric anpassade prestanda räknare

Service Fabric genererar en betydande mängd anpassade prestanda räknare. Om du har installerat SDK kan du se den omfattande listan på din Windows-dator i prestanda övervaknings programmet (Starta > prestanda övervakaren). 

Om du använder Reliable Actors i de program som du distribuerar till klustret lägger du till räknare från `Service Fabric Actor` och `Service Fabric Actor Method` kategorier (se [Service Fabric Reliable Actors diagnostik](service-fabric-reliable-actors-diagnostics.md)).

Om du använder Reliable Services eller tjänstens fjärrstyrning har vi på samma sätt `Service Fabric Service` och `Service Fabric Service Method` räknar kategorier som du bör samla in räknare från, se [övervakning med service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md) och [Reliable Services-prestandaräknare](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Om du använder pålitliga samlingar rekommenderar vi att du lägger till `Avg. Transaction ms/Commit` från `Service Fabric Transactional Replicator` för att samla in den genomsnittliga svars tiden per transaktions mått.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [att generera händelser på plattforms nivå](service-fabric-diagnostics-event-generation-infra.md) i Service Fabric
* Samla in prestanda mått via [Log Analytics agent](service-fabric-diagnostics-oms-agent.md)
