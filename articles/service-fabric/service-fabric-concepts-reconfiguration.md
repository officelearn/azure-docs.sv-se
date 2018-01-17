---
title: Omkonfiguration i Azure Service Fabric | Microsoft Docs
description: "Förstå Omkonfigurering av partitioner i Service Fabric"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 8371c4b268e1181e61542261ad7fc5fd04f6e59c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Omkonfiguration i Azure Service Fabric
En *configuration* definieras som replikerna och roller för en partition av en tillståndskänslig service.

En *omkonfiguration* är processen att flytta en konfiguration till en annan konfiguration. Den gör en ändring i replikuppsättningen för en partition av en tillståndskänslig service. Den gamla konfigurationen kallas den *tidigare konfigurationen (PC)*, och den nya konfigurationen kallas den *aktuella konfigurationen (CC)*. Omkonfiguration-protokollet i Azure Service Fabric bevarar konsekvens och underhåller tillgänglighet under ändringar replikuppsättningen.

Failover Manager initierar reconfigurations som svar på olika händelser i systemet. Till exempel om primärt misslyckas sedan en omkonfiguration initieras för att flytta upp en aktiv sekundär till en primär. Ett annat exempel är som svar på programuppgraderingar när det kan vara nödvändigt att flytta den primära servern till en annan nod för att uppgradera noden.

## <a name="reconfiguration-types"></a>Omkonfiguration typer
Reconfigurations kan delas i två typer:

- Reconfigurations där primärt ändras:
    - **Redundans**: växling vid fel är reconfigurations som svar på felet av en primär körs.
    - **SwapPrimary**: växlingar är reconfigurations där Service Fabric behöver flytta en körs som primär från en nod till en annan, vanligtvis i svar på belastningsutjämning eller en uppgradering.

- Reconfigurations där primärt inte ändras.

## <a name="reconfiguration-phases"></a>Omkonfiguration faser
En Omkonfigurering av fortsätter i flera faser:

- **Phase0**: den här fasen sker i swap-primära reconfigurations där den aktuella primärt överför dess tillstånd till den nya primära servern och övergångar för aktiva sekundära.

- **Phase1**: den här fasen sker under reconfigurations där primärt ändras. Under den här fasen identifierar Service Fabric rätt primära bland de aktuella replikerna. Det här steget krävs inte under växling primära reconfigurations eftersom den nya primärt har redan valts. 

- **Phase2**: under den här fasen Service Fabric garanterar att alla data är tillgängliga i en majoritet av replikerna för den aktuella konfigurationen.

Det finns flera faser som är endast för intern användning.

## <a name="stuck-reconfigurations"></a>Fast reconfigurations
Reconfigurations får *fastnat* för många olika skäl. Några vanliga orsaker är:

- **Ned repliker**: vissa omkonfiguration faser kräver en majoritet av replikerna i konfigurationen ska upp.
- **Problem med nätverket eller kommunikation**: Reconfigurations kräver nätverksanslutning mellan olika noder.
- **Misslyckade API**: protokollet omkonfiguration kräver att implementeringar av tjänsten avslutas vissa API: er. Till exempel gör inte respektera cancellation-token i en tillförlitlig tjänst SwapPrimary reconfigurations fastna.

Använd hälsorapporter från systemkomponenter, till exempel System.FM, System.RA och System.RAP, för att ta reda på om en konfigurationsändring har fastnat. Den [system hälsa rapportsidan](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) beskriver dessa hälsorapporter.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

- [Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)
- [System hälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
