---
title: Prestanda för virtuella datorer och diskar
description: Lär dig mer om hur virtuella datorer och deras anslutna diskar fungerar i kombination för prestanda
author: albecker1
ms.author: albecker
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6621efe6d0b391ed38ab49f147d791a11db14301
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89665201"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestanda för virtuella datorer och diskar
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Den virtuella datorn har cachelagrats jämfört med cachelagrade gränser
Virtuella datorer som har både Premium Storage aktiverat och Premium Storage-cachelagring aktiverat har två olika begränsningar för lagrings bandbredd. Nu ska vi fortsätta att titta på den Standard_D8s_v3 virtuella datorn som ett exempel. Här är dokumentationen om [Dsv3-serien](../dv3-dsv3-series.md) och Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
