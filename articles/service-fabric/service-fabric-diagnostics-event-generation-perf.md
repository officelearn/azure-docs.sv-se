---
title: Azure Service Fabric prestanda övervakning | Microsoft Docs
description: Mer information om prestandaräknare för övervakning och diagnostik av Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: b19a2db85b2e1cc4c5f79f6b0dee97965f40ef88
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="performance-metrics"></a>Prestandamått

Mått ska samlas in för att förstå prestanda för klustret, samt de program som körs i den. För Service Fabric-kluster rekommenderar vi att samla in följande prestandaräknare.

## <a name="nodes"></a>Noder

Överväg att samla in följande prestandaräknare för att bättre förstå belastningen på varje dator och att rätt kluster skalning beslut för datorer i klustret.

| Räknaren kategori | Räknarens namn |
| --- | --- |
| Fysisk disk (per Disk) | Avg. Läs diskkölängd |
| Fysisk disk (per Disk) | Avg. Diskkölängd för skrivning |
| Fysisk disk (per Disk) | Avg. Disk sek/läsning |
| Fysisk disk (per Disk) | Avg. Disk sek/skrivning |
| Fysisk disk (per Disk) | Diskläsningar/sek |
| Fysisk disk (per Disk) | Disk – lästa byte/sek |
| Fysisk disk (per Disk) | Diskskrivningar/sek |
| Fysisk disk (per Disk) | Disk – skrivna byte/sek |
| Minne | Tillgängliga megabyte |
| Växling fil | % Användning |
| Processor(total) | % Processortid |
| Processen (per service) | % Processortid |
| Processen (per service) | ID Process |
| Processen (per service) | Privata byte |
| Processen (per service) | Antal trådar |
| Processen (per service) | Virtuell storlek-byte |
| Processen (per service) | Sidmängd |
| Processen (per service) | Privat arbetsminne |
| Nätverk Interface(all-instances) | Kölängd-utdata |
| Nätverk Interface(all-instances) | Utgående ignorerade paket |
| Nätverk Interface(all-instances) | Mottagna paket |
| Nätverk Interface(all-instances) | Utgående paket-fel |
| Nätverk Interface(all-instances) | Mottagna paket-fel |

## <a name="net-applications-and-services"></a>.NET-program och tjänster

Samla in följande räknare om du distribuerar .NET services i klustret. 

| Räknaren kategori | Räknarens namn |
| --- | --- |
| .NET CLR-minne (per service) | Process-ID |
| .NET CLR-minne (per service) | # Totalt antal allokerade byte |
| .NET CLR-minne (per service) | # Totalt antal reserverade byte |
| .NET CLR-minne (per service) | # Byte i alla Heaps |
| .NET CLR-minne (per service) | Antal generation 0-insamlingar |
| .NET CLR-minne (per service) | Antal generation 1-samlingar |
| .NET CLR-minne (per service) | # Generation 2-insamlingar |
| .NET CLR-minne (per service) | Tid i GC i procent |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric anpassade prestandaräknare

Service Fabric genererar en stor mängd anpassade prestandaräknare. Om du har installerat SDK kan du se omfattande lista på din Windows-dator i tillämpningsprogrammet Prestandaövervakaren (Start > Prestandaövervakaren). 

I program som du distribuerar till ditt kluster om du använder Reliable Actors, lägga till countes från `Service Fabric Actor` och `Service Fabric Actor Method` kategorier (se [Service Fabric tillförlitliga aktörer diagnostik](service-fabric-reliable-actors-diagnostics.md)).

Om du använder Reliable Services på samma sätt har vi `Service Fabric Service` och `Service Fabric Service Method` räknaren kategorier som du bör samla in prestandaräknare från. 

Om du använder tillförlitliga samlingar, rekommenderar vi att lägga till den `Avg. Transaction ms/Commit` från den `Service Fabric Transactional Replicator` att samla in den genomsnittliga commit svarstiden per transaktion mått.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [händelse genereras på nivån plattform](service-fabric-diagnostics-event-generation-infra.md) i Service Fabric
* Samla in prestandavärden via [Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md)
