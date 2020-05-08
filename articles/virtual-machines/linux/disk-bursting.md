---
title: Hanterad disk-bursting
description: Lär dig mer om disk burst för Azure-diskar och disk burst för virtuella Azure-datorer
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594754"
---
# <a name="disk-bursting"></a>Diskutvidgning
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burst-överföring på virtuell dator
Stöd för burst-överföring på virtuell dator har Aktiver ATS i alla regioner i det offentliga molnet med de storlekar som stöds: 
- [Lsv2-serien](../lsv2-series.md)

Bursting är aktiverat som standard för virtuella datorer som har stöd för det.

## <a name="disk-level-bursting"></a>Burst-överföring på disk nivå
Bursting är också tillgängligt på vår [Premium-SSD](disks-types.md#premium-ssd) för disk storlekar P20 och mindre i alla regioner. Disk-burst är aktiverat som standard på nya distributioner av disk storlekarna som stöder det. Befintliga disk storlekar, om de har stöd för disk burst, kan aktivera burst via någon av följande metoder: 
- **Starta om den virtuella datorn** 
- **Koppla från och återanslut disken**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
