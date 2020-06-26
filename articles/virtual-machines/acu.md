---
title: Översikt över Azure Compute Unit
description: 'Översikt över konceptet med Azure Compute units. ACU är ett sätt att jämföra CPU-prestanda i Azure SKU: er.'
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: 74c0f5be7998450b0fb868ff4969e412fdaa4788
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85414478"
---
# <a name="azure-compute-unit-acu"></a>Azure Compute Unit (ACU)

Konceptet för Azure Compute Unit (ACU) är ett sätt att jämföra beräknings prestanda (CPU) i Azure SKU: er. På så sätt blir det lättare att identifiera vilken SKU som bäst uppfyller dina behov. ACU är för närvarande standardiserad på en liten (Standard_A1) virtuell dator som 100 och alla andra SKU: er representerar ungefär hur mycket snabbare som SKU: n kan köra en standard-benchmark

* ACUs använder Intel® Turbo teknik för att öka CPU-frekvensen och ge en bättre prestanda.  Mängden prestanda ökning kan variera beroende på storleken på den virtuella datorn, arbets belastningen och andra arbets belastningar som körs på samma värd.

* * ACUs använder AMD® Boost-teknik för att öka CPU-frekvensen och ge en bättre prestanda.  Mängden prestanda ökning kan variera beroende på storleken på den virtuella datorn, arbets belastningen och andra arbets belastningar som körs på samma värd.

Hyper-threaded och kan köra kapslad virtualisering

> [!IMPORTANT]
> ACU är endast en riktlinje. Resultatet med din arbetsbelastning kan variera.
<br>

| SKU-familj | ACU\vCPU | vCPU: Core |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 – A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5-A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2-A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2-A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 – A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 – D14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [D1_v2-D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1-DS14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [DS1_v2-DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230 – 260 * * | 2:1 |
| [Dasv4](dav4-dasv4-series.md) |230 – 260 * * | 2:1 |
| [Dv4](dv4-dsv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 – 190 * | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230 – 260 * * | 2:1 |
| [Easv4](eav4-easv4-series.md) | 230 – 260 * * | 2:1 |
| [Ev4](ev4-esv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [F2s_v2-F72s_v2](fsv2-series.md) |195 – 210 * | 2:1\*\*\* |
| [F1 – F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1-enheter – F16-enheter](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1-G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 – GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [&](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 – 216 * * | 1:1 |
| [HC](hc-series.md) |297 – 315 * | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2-L80s_v2](lsv2-series.md) |150 – 175 * * | 2:1 |
| [Avstånd](m-series.md) | 160 – 180 | 2:1\*\*\* |

Här finns länkar till mer information om de olika storlekarna:

- [Allmänt-syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Lagringsoptimerad](sizes-storage.md)
