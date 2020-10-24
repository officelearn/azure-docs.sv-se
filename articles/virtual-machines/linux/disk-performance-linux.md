---
title: Prestanda för virtuella datorer och diskar
description: Lär dig mer om hur virtuella datorer och deras anslutna diskar fungerar i kombination för prestanda
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 092368bb66784a00d5116da0b6be6513f8ebb261
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518099"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestanda för virtuella datorer och diskar
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Den virtuella datorn har cachelagrats jämfört med cachelagrade gränser
Virtuella datorer som är aktiverade för både Premium Storage och Premium Storage-cachelagring har två olika begränsningar för lagrings bandbredd. Nu ska vi titta på den Standard_D8s_v3 virtuella datorn som ett exempel. Här är dokumentationen om [Dsv3-serien](../dv3-dsv3-series.md) och Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Nu ska vi köra ett benchmarking-test på den här virtuella datorn och den disk kombination som skapar IO-aktivitet. Information om hur du kan mäta lagrings-i/o i Azure finns i avsnittet [om att mäta ditt program på Azure-disklagring](disks-benchmarks.md). Från benchmarking-verktyget kan du se att kombinationen av virtuell dator och disk kan uppnå 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
