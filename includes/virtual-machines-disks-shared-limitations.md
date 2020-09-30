---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9aa8857ab10423f460874870a3663929e8e5a5c5
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566964"
---
Att aktivera delade diskar är bara tillgängligt för en delmängd disk typer. För närvarande kan endast Ultra disks och Premium-SSD aktivera delade diskar. Varje hanterad disk som har delade diskar aktiverade omfattas av följande begränsningar, ordnade efter disk typ:

### <a name="ultra-disks"></a>Ultradiskar

Ultra disks har en separat lista över begränsningar, som inte är relaterade till delade diskar. För extremt disk begränsningar, se [använda Azure Ultra disks](../articles/virtual-machines/disks-enable-ultra-ssd.md).

När du delar Ultra disks har de följande ytterligare begränsningar:

- För närvarande begränsad till Azure Resource Manager-eller SDK-support. 
- Det går bara att använda standard diskar med vissa versioner av Windows Server-redundanskluster, mer information finns i [maskin varu krav och lagrings alternativ för redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

Delade Ultra disks är tillgängliga i alla regioner som stöder Ultra disks som standard och kräver inte att du registrerar dig för åtkomst för att använda dem.

### <a name="premium-ssds"></a>Premium SSD

- För närvarande begränsad till Azure Resource Manager-eller SDK-support. 
- Kan bara aktive ras på data diskar, inte på OS-diskar.
- **ReadOnly** -cachelagring av värden är inte tillgängligt för Premium-SSD med `maxShares>1` .
- Disk-burst är inte tillgängligt för Premium-SSD med `maxShares>1` .
- När du använder tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer med Azure delade diskar, tillämpas inte [justeringen av lagrings fel domänen](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) med den virtuella dator fel domänen för den delade data disken.
- När du använder [närhets placerings grupper (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)måste alla virtuella datorer som delar en disk ingå i samma PPG.
- Det går bara att använda standard diskar med vissa versioner av Windows Server-redundanskluster, mer information finns i [maskin varu krav och lagrings alternativ för redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Azure Backup och Azure Site Recovery support är ännu inte tillgängligt.

#### <a name="regional-availability"></a>Regional tillgänglighet

Delade Premium-SSD är tillgängliga i alla regioner som Managed disks är tillgängliga.