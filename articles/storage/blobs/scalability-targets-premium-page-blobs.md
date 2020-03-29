---
title: Skalbarhetsmål för premium-sidblobblagringskonton
titleSuffix: Azure Storage
description: Ett premiumprestandasida blob storage-konto är optimerat för läs-/skrivåtgärder. Den här typen av lagringskonto stöder en ohanterlig disk för en virtuell Azure-dator.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756257"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Skalbarhets- och prestandamål för premium-sidblobblagringskonton

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Skala mål för premium-sidblobbkonton

Ett premium-prestanda sida blob lagringskonto är optimerad för läs-/skrivåtgärder. Den här typen av lagringskonto stöder en ohanterlig disk för en virtuell Azure-dator.

> [!NOTE]
> Microsoft rekommenderar att du använder hanterade diskar med virtuella Azure-datorer (VMs) om möjligt. Mer information om hanterade diskar finns i [Azure Disk Storage översikt för Windows virtuella datorer](../../virtual-machines/windows/managed-disks-overview.md).

Premium-konton för bloblagringskonton har följande skalbarhetsmål:

| Total kontokapacitet                            | Total bandbredd för ett lokalt redundant lagringskonto                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Diskkapacitet: 4 TB (enskild disk)/ 35 TB (ackumulerad total summa av alla diskar) <br>Snapshot kapacitet: 10 TB | Upp till 50 gigabit per sekund för inkommande<sup>1</sup> + utgående<sup>2</sup> |

<sup>1</sup> Alla data (begäranden) som skickas till ett lagringskonto

<sup>2</sup> Alla data (svar) som tas emot från ett lagringskonto

Ett premium-sidblobkonto är ett allmänt konto som konfigurerats för premiumprestanda. Allmänna v2-lagringskonton rekommenderas.

Om du använder premium-sidblobblagringskonton för ohanterade diskar och ditt program överskrider skalbarhetsmålen för ett enskilt lagringskonto rekommenderar Microsoft att du migrerar till hanterade diskar. Mer information om hanterade diskar finns i [Azure Disk Storage översikt för Windows virtuella datorer](../../virtual-machines/windows/managed-disks-overview.md) eller Azure Disk Storage översikt för [Linux-virtuella datorer](../../virtual-machines/linux/managed-disks-overview.md).

Om du inte kan migrera till hanterade diskar kan du skapa programmet så att det använder flera lagringskonton och partitionerar dina data över dessa lagringskonton. Om du till exempel vill koppla 51 TB-diskar över flera virtuella datorer kan du sprida dem över två lagringskonton. 35 TB är gränsen för ett enda premiumlagringskonto. Se till att ett enda premium-prestandalagringskonto aldrig har mer än 35 TB etablerade diskar.

## <a name="see-also"></a>Se även

- [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md)
- [Skalbarhetsmål för premiumblockbloblagringskonton](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure-prenumerationsbegränsningar och kvoter](../../azure-resource-manager/management/azure-subscription-service-limits.md)
