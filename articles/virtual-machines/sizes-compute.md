---
title: Azure VM-storlekar – Beräkning optimerad | Microsoft-dokument
description: Visar en lista över de olika beräkningsoptimerade storlekarna som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, datadiskar och nätverkskort samt lagringsdataflöde och nätverksbandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269643"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Beräkningsoptimerade storlekar för virtuella datorer

Beräkningsoptimerade VM-storlekar har ett högt förhållande mellan processor och minne. Dessa storlekar är bra för mellantrafik webbservrar, nätverksenheter, batchprocesser och programservrar. Den här artikeln innehåller information om antalet virtuella processorer, datadiskar och nätverkskort. Den innehåller också information om lagringsdataflöde och nätverksbandbredd för varje storlek i den här gruppningen.

[Fsv2-serien](fsv2-series.md) är baserad på Intel® Xeon® Platinum 8168-processorn. Den har en ihållande all core Turbo klockfrekvens på 3,4 GHz och en maximal single-core turbo frekvens på 3,7 GHz. Intel® AVX-512-instruktioner är nya på Intel Scalable-processorer. Dessa instruktioner ger upp till en 2X prestandaökning till arbetsbelastningar för vektorbearbetning på flyttalsoperationer med både enkel och dubbel precision. Med andra ord, de är riktigt snabba för alla beräkningsarbetsbelastning.

Med ett lägre listpris per timme är Fsv2-serien det bästa värdet i prisprestanda i Azure-portföljen baserat på Azure Compute Unit (ACU) per vCPU.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
