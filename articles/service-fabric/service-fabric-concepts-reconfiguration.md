---
title: Omkonfiguration i Azure Service Fabric | Microsoft Docs
description: Förstå omkonfiguration av partitioner i Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882205"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Omkonfiguration i Azure Service Fabric
En *configuration* definieras som replikerna och deras roller för en partition av en tillståndskänslig tjänst.

En *omkonfiguration* är processen att flytta en konfiguration till en annan konfiguration. Den gör en ändring för replikuppsättningen för en partition av en tillståndskänslig tjänst. Gammal konfiguration kallas den *tidigare konfigurationen (PC)*, och den nya konfigurationen kallas den *aktuella konfigurationen (kopia)*. Protokollet omkonfiguration i Azure Service Fabric bevarar konsekvens och underhåller tillgänglighet under ändringar till replikuppsättningen.

Redundanshanteraren initierar reconfigurations som svar på olika händelser i systemet. Till exempel om primärt misslyckas sedan en av initieras för att flytta upp en aktiva sekundära till en primär. Ett annat exempel är som svar på programuppgraderingar när det kan vara nödvändigt att flytta den primära servern till en annan nod för att uppgradera noden.

## <a name="reconfiguration-types"></a>Omkonfiguration typer
Reconfigurations kan delas i två typer:

- Reconfigurations där primärt ändras:
    - **Redundans**: Redundans är reconfigurations som svar på felet av en primär körs.
    - **SwapPrimary**: Växlingar är reconfigurations där Service Fabric behöver flytta en körs som primär från en nod till en annan, sker vanligtvis i svar på belastningsutjämning eller en uppgradering.

- Reconfigurations där primärt inte ändras.

## <a name="reconfiguration-phases"></a>Omkonfiguration faser
En av fortsätter i flera faser:

- **Phase0**: Den här fasen sker i swap-primära reconfigurations där den aktuella primärt överför dess tillstånd till den nya primära servern och övergångar till aktiv sekundär.

- **Phase1**: Den här fasen sker under reconfigurations där primärt ändras. Under den här fasen identifierar Service Fabric den rätta primärt bland de aktuella replikerna. Det här steget behövs inte under swap-primära reconfigurations eftersom den nya primärt har redan valts. 

- **Phase2**: Under den här fasen säkerställer Service Fabric att alla data är tillgängliga i en majoritet av repliker av den aktuella konfigurationen.

Det finns flera faser som är endast för internt bruk.

## <a name="stuck-reconfigurations"></a>Har fastnat reconfigurations
Reconfigurations får *fastnat* av olika skäl. Några vanliga orsaker är:

- **Ned repliker**: Vissa omkonfiguration faser kräver en majoritet av repliker i konfigurationen att vara igång.
- **Problem med nätverket eller kommunikation**: Reconfigurations kräver nätverksanslutning mellan olika noder.
- **Misslyckade API**: Protokollet omkonfiguration kräver att tjänstimplementeringar Slutför vissa API: er. Till exempel gör inte respekterar annullering token i en tillförlitlig tjänst SwapPrimary reconfigurations fastnar.

Använda hälsorapporter från systemkomponenter, till exempel System.FM, System.RA och System.RAP, för att diagnostisera där en omkonfiguration har fastnat. Den [system health rapportsida](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) beskriver dessa rapporter om hälsotillstånd.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

- [Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)
- [Systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
