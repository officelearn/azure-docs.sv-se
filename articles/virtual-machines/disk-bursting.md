---
title: Hanterad disk-bursting
description: Lär dig mer om disk burst för Azure-diskar och virtuella Azure-datorer.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a8d1a69070628248fb710a614369ff9311252c05
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103038"
---
# <a name="managed-disk-bursting"></a>Hanterad disk-bursting
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burst-överföring på virtuell dator
Stöd för burst-överföring på virtuell dator har Aktiver ATS i alla regioner i det offentliga molnet med de storlekar som stöds: 
- [Lsv2-serien](lsv2-series.md)

Burst-överföring på VM-nivå är också tillgängligt i USA, västra centrala, för följande storlekar som stöds:
- [Dsv3-serien](dv3-dsv3-series.md)
- [Esv3-serien](ev3-esv3-series.md)

Bursting är aktiverat som standard för virtuella datorer som har stöd för det.

## <a name="disk-level-bursting"></a>Burst-överföring på disk nivå
Bursting är också tillgängligt på vårt [Premium-SSD](disks-types.md#premium-ssd) för disk storlekar P20 och mindre i alla regioner i Azures offentliga, offentliga myndigheter och i Kina. Disk-burst är aktiverat som standard på alla nya och befintliga distributioner av disk storlekarna som stöder det. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]