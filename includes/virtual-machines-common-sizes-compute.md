---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 21b982389b186e949b21352f4b11bd6b4aa06dcb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279175"
---
<!-- F-series, Fs-series* -->

Compute-optimerade VM-storlekar har ett högt förhållande mellan processor och minne. De här storlekarna är lämpliga för medel stora webb servrar, nätverks enheter, batch-processer och program servrar. Den här artikeln innehåller information om antalet virtuella processorer, data diskar och nätverkskort. Den innehåller också information om lagrings data flöde och nätverks bandbredd för varje storlek i grupperingen.

Fsv2-serien baseras på Intel® Xeon® Platinum 8168-processorn. Den har en hållbar alla kärnor för Turbo Turbo klock hastighet på 3,4 GHz och en maximal Turbo frekvens på 3,7 GHz. Intel® AVX-512-instruktioner är nya på Intel Scalable processors. Dessa instruktioner ger till gång till en dubbel prestanda ökning till vektor bearbetning av arbets belastningar i både enkla och dubbla precisions åtgärder. Med andra ord är de verkligen snabba för alla beräknings arbets belastningar.

I ett lägre pris per timme-lista är Fsv2-serien det bästa värdet för pris prestanda i Azure-portföljen baserat på Azure Compute Unit (ACU) per vCPU.

## <a name="fsv2-series-sup1sup"></a>Fsv2-serie <sup>1</sup>

ACU: 195-210

Premium Storage: stöds

Premium Storage caching: stöds

| Storlek             | Virtuella processorer | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31 (32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200/750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000 / 768 (768)        | 76800 / 1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |

<sup>1</sup> Fsv2 VM-funktioner Intel® Hyper-Threading-teknik.

<sup>2</sup> användningen av fler än 64 vCPU kräver ett av följande gäst operativ system: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 och Red Hat Enterprise Linux, CentOS 7,3 eller Oracle Linux 7,3 med Lis 4.2.1.

<sup>3</sup> -instansen är isolerad till maskin vara som är dedikerad till en enda kund.
