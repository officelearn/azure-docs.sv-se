---
title: Hanterad disk-bursting
description: Lär dig mer om disk burst för Azure-diskar och disk burst för virtuella Azure-datorer
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: f92fae38d49c51dfe87a68b023ba779e89b0e0bc
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295464"
---
# <a name="disk-bursting"></a>Diskutvidgning
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burst-överföring på virtuell dator
Stöd för burst-överföring på virtuell dator har Aktiver ATS i alla regioner i det offentliga molnet med de storlekar som stöds: 
- [Lsv2-serien](../lsv2-series.md)

Bursting är aktiverat som standard för virtuella datorer som har stöd för det.

## <a name="disk-level-bursting"></a>Burst-överföring på disk nivå
Bursting är också tillgängligt på vårt [Premium-SSD](disks-types.md#premium-ssd) för disk storlekar P20 och mindre i alla regioner i Azures offentliga, offentliga myndigheter och i Kina. Disk-burst är aktiverat som standard på alla nya och befintliga distributioner av disk storlekarna som stöder det. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
