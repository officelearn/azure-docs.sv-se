---
title: Azure VM-storlekar – lagring | Microsoft-dokument
description: Visar en lista över de olika lagringsoptimerade storlekarna som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, datadiskar och nätverkskort samt lagringsdataflöde och nätverksbandbredd för storlekar i den här serien.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913344"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Lagringsoptimerade storlekar för virtuella datorer

Lagringsoptimerade VM-storlekar erbjuder hög diskgenomströmning och IO, och är idealiska för Big Data, SQL, NoSQL-databaser, datalagring och stora transaktionsdatabaser.  Exempel är Cassandra, MongoDB, Cloudera och Redis. Den här artikeln innehåller information om antalet virtuella processorer, datadiskar och nätverkskort samt lokalt lagringsdataflöde och nätverksbandbredd för varje optimerad storlek.

[Lsv2-serien](lsv2-series.md) har hög genomströmning, låg latens, direkt mappad lokal NVMe-lagring som körs på [AMD EPYC<sup>TM</sup> 7551-processorn](https://www.amd.com/en/products/epyc-7000-series) med en all core boost på 2,55 GHz och en maximal boost på 3,0 GHz. Virtuella datorer i Lsv2-serien finns i storlekar från 8 till 80 vCPU i en samtidig konfiguration med flera trådar.  Det finns 8 GiB minne per vCPU, och en 1.92TB NVMe SSD M.2 enhet per 8 vCPU, med upp till 19.2TB (10x1.92TB) tillgänglig på L80s v2.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.

Lär dig hur du optimerar prestanda på virtuella datorer i Lsv2-serien för [Windows](windows/storage-performance.md) eller [Linux](linux/storage-performance.md).
