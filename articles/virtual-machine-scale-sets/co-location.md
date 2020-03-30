---
title: Samlokalisera Azure-skalningsuppsättningar för virtuella datorer
description: Lär dig mer om hur samlokalisering av Azure-resurser för virtuell datorskala kan förbättra prestanda.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 9d6e4e15c552cc4467dd17221a1ba901fd8e4f58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250834"
---
# <a name="co-location"></a>Samlokalisering

En av de största bidragsgivarna till svarstid mellan virtuella datorer är helt enkelt avstånd.

## <a name="preview-proximity-placement-groups"></a>Förhandsgranskning: Grupper för platsplacering i närheten 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Nästa steg

Skapa en [närhetsplaceringsgrupp](proximity-placement-groups.md) för din skalningsuppsättning.

