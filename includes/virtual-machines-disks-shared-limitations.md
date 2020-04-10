---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008375"
---
I förhandsgranskningen är det bara att aktivera delade diskar för en delmängd av disktyper. För närvarande kan endast ultradiskar och premium-SSD-enheter aktivera delade diskar. Varje hanterad disk som har aktiverat delade diskar omfattas av följande begränsningar, ordnade efter disktyp:

### <a name="ultra-disks"></a>Ultradiskar

Ultra diskar har sin egen separata lista över begränsningar, utan samband med delade diskar. För ultra disk begränsningar, se [Använda Azure ultra diskar](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

När du delar ultradiskar har de följande ytterligare begränsningar:

- För närvarande endast stöds i västra USA.
- För närvarande begränsad till Azure Resource Manager eller SDK-stöd.
- Endast grundläggande diskar kan användas med vissa versioner av Windows Server Failover Cluster, mer information se [Maskinvarukrav för redundanskluster och lagringsalternativ](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Premium SSD:er

- För närvarande endast stöds i västra centrala USA regionen.
- Alla virtuella datorer som delar en disk måste distribueras i samma [närhetsplaceringsgrupper](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Kan bara aktiveras på datadiskar, inte OS-diskar.
- Endast grundläggande diskar kan användas med vissa versioner av Windows Server Failover Cluster, mer information se [Maskinvarukrav för redundanskluster och lagringsalternativ](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- ReadOnly värdcache är inte tillgänglig för premium `maxShares>1`SSD med .
- Tillgänglighetsuppsättningar och skalningsuppsättningar `FaultDomainCount` för virtuella datorer kan endast användas med inställd 1.
- Azure Backup och Azure Site Recovery support är ännu inte tillgänglig.

Om du är intresserad av att prova delade diskar sedan [registrera dig för vår förhandsvisning](https://aka.ms/AzureSharedDiskPreviewSignUp).
