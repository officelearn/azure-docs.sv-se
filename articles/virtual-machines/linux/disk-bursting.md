---
title: Hanterad disk-bursting
description: Lär dig mer om disk burst för Azure-diskar och disk burst för virtuella Azure-datorer
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: baabad550f5e6b0ae39936fc182e6d9789d189d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650934"
---
# <a name="disk-bursting"></a>Diskutvidgning
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burst-överföring på virtuell dator
Stöd för burst-överföring på virtuell dator har Aktiver ATS i alla regioner i det offentliga molnet med de storlekar som stöds: 
- [Lsv2-serien](../lsv2-series.md)

Bursting är aktiverat som standard för virtuella datorer som har stöd för det.

## <a name="disk-level-bursting"></a>Burst-överföring på disk nivå
Bursting är också tillgängligt på vårt [Premium-SSD](disks-types.md#premium-ssd) för disk storlekar P20 och mindre i alla regioner i Azures offentliga, offentliga myndigheter och i Kina. Disk-burst är aktiverat som standard på nya distributioner av disk storlekarna som stöder det. Befintliga disk storlekar, om de har stöd för disk burst, kan aktivera burst via någon av följande metoder: 
- **Starta om den virtuella datorn** 
- **Koppla från och återanslut disken**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
