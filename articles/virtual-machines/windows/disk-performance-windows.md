---
title: Prestanda för virtuella datorer och diskar
description: Lär dig mer om hur virtuella datorer och deras anslutna diskar fungerar i kombination för prestanda
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 88cf9c28a9d325d617f4b049015f0cd238a2fb31
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016554"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestanda för virtuella datorer och diskar
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Den virtuella datorn har cachelagrats jämfört med cachelagrade gränser
 Virtuella datorer som har både Premium Storage aktiverat och Premium Storage-cachelagring aktiverat har två olika begränsningar för lagrings bandbredd. Nu ska vi fortsätta att titta på den Standard_D8s_v3 virtuella datorn som ett exempel. Här är dokumentationen om [Dsv3-serien](../dv3-dsv3-series.md) och Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Nu ska vi köra ett benchmarking-test på denna VM-och disk kombination som gör att du kan skapa IO-aktivitet och lära dig allt om hur du kan mäta lagrings-i/o på Azure [här](disks-benchmarks.md). Från benchmarking-verktyget kan du se att kombinationen av virtuell dator och disk kan uppnå 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]