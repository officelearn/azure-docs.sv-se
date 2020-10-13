---
title: Hanterad disk-bursting
description: Lär dig mer om disk burst för Azure-diskar och disk burst för virtuella Azure-datorer
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 547834542b605b226ebffd68e05296ee847dc6de
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974336"
---
# <a name="disk-bursting"></a>Diskutvidgning
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burst-överföring på virtuell dator
Stöd för burst-överföring på virtuell dator har Aktiver ATS i alla regioner i det offentliga molnet med de storlekar som stöds: 
- [Lsv2-serien](../lsv2-series.md)

Burst-överföring på VM-nivå är också tillgängligt i USA, västra centrala, för följande storlekar som stöds:
- [Dsv3-serien](../dv3-dsv3-series.md)
- [Esv3-serien](../ev3-esv3-series.md)

Bursting är aktiverat som standard för virtuella datorer som har stöd för det.

## <a name="disk-level-bursting"></a>Burst-överföring på disk nivå
Bursting är också tillgängligt på vår [Premium-SSD](../disks-types.md#premium-ssd) för disk storlekar P20 och mindre i alla regioner. Disk-burst är aktiverat som standard på nya distributioner av disk storlekarna som stöder det. Befintliga disk storlekar, om de har stöd för disk burst, kan aktivera burst via någon av följande metoder: 
- **Starta om den virtuella datorn** 
- **Koppla från och återanslut disken**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]