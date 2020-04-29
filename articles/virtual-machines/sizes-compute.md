---
title: Storlekar för virtuella Azure-datorer – Compute-optimerad | Microsoft Docs
description: Visar en lista över de olika optimerings storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269643"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Beräkna optimerade storlekar för virtuella datorer

Compute-optimerade VM-storlekar har ett högt förhållande mellan processor och minne. De här storlekarna är lämpliga för medel stora webb servrar, nätverks enheter, batch-processer och program servrar. Den här artikeln innehåller information om antalet virtuella processorer, data diskar och nätverkskort. Den innehåller också information om lagrings data flöde och nätverks bandbredd för varje storlek i grupperingen.

[Fsv2-serien](fsv2-series.md) baseras på Intel® Xeon® Platinum 8168-processorn. Den har en hållbar alla kärnor för Turbo Turbo klock hastighet på 3,4 GHz och en maximal Turbo frekvens på 3,7 GHz. Intel® AVX-512-instruktioner är nya på Intel Scalable processors. Dessa instruktioner ger till gång till en dubbel prestanda ökning till vektor bearbetning av arbets belastningar i både enkla och dubbla precisions åtgärder. Med andra ord är de verkligen snabba för alla beräknings arbets belastningar.

I ett lägre pris per timme-lista är Fsv2-serien det bästa värdet för pris prestanda i Azure-portföljen baserat på Azure Compute Unit (ACU) per vCPU.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
