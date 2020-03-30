---
title: Omkonfigurering i Azure Service Fabric
description: Lär dig mer om konfigurationer för tillståndskänsliga tjänstrepliker och processen för omkonfigurering service fabric använder för att upprätthålla konsekvens och tillgänglighet under ändringen.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610003"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Omkonfigurering i Azure Service Fabric
En *konfiguration* definieras som repliker och deras roller för en partition av en tillståndskänslig tjänst.

En *omkonfiguration* är processen att flytta en konfiguration till en annan konfiguration. Det gör en ändring av replikuppsättningen för en partition av en tillståndskänslig tjänst. Den gamla konfigurationen kallas *den tidigare konfigurationen (PC)* och den nya konfigurationen kallas den *aktuella konfigurationen (CC)*. Omkonfigurationsprotokollet i Azure Service Fabric bevarar konsekvensen och behåller tillgängligheten under eventuella ändringar i replikuppsättningen.

Redundanshanteraren initierar omkonfigurationer som svar på olika händelser i systemet. Om den primära misslyckas initieras till exempel en omkonfiguration för att befordra en aktiv sekundär till en primär. Ett annat exempel är som svar på programuppgraderingar när det kan vara nödvändigt att flytta den primära till en annan nod för att uppgradera noden.

## <a name="reconfiguration-types"></a>Omkonfigurationstyper
Omkonfigurationer kan delas in i två typer:

- Konfigurera om var primärt ändras:
    - **Redundans:** Redundans är omkonfigurationer som svar på felet för en primär körning.
    - **SwapPrimary**: Swappar är omkonfigurationer där Service Fabric behöver flytta en primär körning från en nod till en annan, vanligtvis som svar på belastningsutjämning eller en uppgradering.

- Konfigurera om där den primära inte ändras.

## <a name="reconfiguration-phases"></a>Omkonfigurationsfaser
En omkonfiguration fortsätter i flera faser:

- **Fas0**: Den här fasen inträffar i omkonfigurationer av växlingsprimärer där den aktuella primärt överför sitt tillstånd till den nya primär- och övergångarna till aktiv sekundär.

- **Fas 1**: Den här fasen inträffar under omkonfigurationer där primärt förändras. Under den här fasen identifierar Service Fabric rätt primär bland de aktuella replikerna. Den här fasen behövs inte under omkonfigurationer av växlingsprimär konfigurationer eftersom den nya primärnyckeln redan har valts. 

- **Fas2**: Under den här fasen säkerställer Service Fabric att alla data är tillgängliga i de flesta repliker av den aktuella konfigurationen.

Det finns flera andra faser som endast är för internt bruk.

## <a name="stuck-reconfigurations"></a>Fästa omkonfigurering
Omkonfigurationer kan *fastna* av olika anledningar. Några av de vanligaste orsakerna är:

- **Nedåtrepliker:** Vissa omkonfigurationsfaser kräver att en majoritet av replikerna i konfigurationen ska vara uppe.
- **Nätverks- eller kommunikationsproblem**: Omkonfigurationer kräver nätverksanslutning mellan olika noder.
- **API-fel**: Omkonfigurationsprotokollet kräver att tjänstimplementeringar slutför vissa API:er. Om du till exempel inte respekterar annulleringstoken i en tillförlitlig tjänst får SwapPrimary-omkonfigurationer att fastna.

Använd hälsorapporter från systemkomponenter, till exempel System.FM, System.RA och System.RAP, för att diagnostisera var en omkonfiguration har fastnat. Sidan [Systemhälsorapport](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) beskriver dessa hälsorapporter.

## <a name="next-steps"></a>Nästa steg
Mer information om service fabric-begrepp finns i följande artiklar:

- [Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)
- [Hälsorapporter för systemet](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
