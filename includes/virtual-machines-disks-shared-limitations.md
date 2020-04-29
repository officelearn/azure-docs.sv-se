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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008375"
---
I för hands versionen är aktivering av delade diskar bara tillgänglig för en delmängd disk typer. För närvarande kan endast Ultra disks och Premium-SSD aktivera delade diskar. Varje hanterad disk som har delade diskar aktiverade omfattas av följande begränsningar, ordnade efter disk typ:

### <a name="ultra-disks"></a>Ultradiskar

Ultra disks har en separat lista över begränsningar, som inte är relaterade till delade diskar. För extremt disk begränsningar, se [använda Azure Ultra disks](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

När du delar Ultra disks har de följande ytterligare begränsningar:

- Stöds för närvarande endast i västra USA.
- För närvarande begränsad till Azure Resource Manager-eller SDK-support.
- Det går bara att använda standard diskar med vissa versioner av Windows Server-redundanskluster, mer information finns i [maskin varu krav och lagrings alternativ för redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Premium SSD:er

- Stöds för närvarande endast i regionen västra centrala USA.
- Alla virtuella datorer som delar en disk måste distribueras i samma [närhets placerings grupper](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Kan bara aktive ras på data diskar, inte på OS-diskar.
- Det går bara att använda standard diskar med vissa versioner av Windows Server-redundanskluster, mer information finns i [maskin varu krav och lagrings alternativ för redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- ReadOnly-cachelagring av värden är inte tillgängligt för `maxShares>1`Premium-SSD med.
- Tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer kan bara `FaultDomainCount` användas med värdet 1.
- Azure Backup och Azure Site Recovery support är ännu inte tillgängligt.

Om du är intresse rad av att prova delade diskar kan du [Registrera dig för vår för hands version](https://aka.ms/AzureSharedDiskPreviewSignUp).
