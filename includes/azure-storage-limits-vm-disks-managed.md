---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/12/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7d1f75df9318c53a6d9e38c4d7b68587cf9a0d4b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057427"
---
**Standard hanterade virtuella hårddiskar**

| Disk av standardtyp  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60 *             | S70 *             | S80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Diskstorleken i GiB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2 048     | 4 095    | 8 192     | 16 384     | 32 767     |
| IOPS per disk       | Upp till 500              | Upp till 500              | Upp till 500              | Upp till 500 | Upp till 500              | Upp till 500              | Upp till 500             | Upp till 500              | Upp till 1 300              | Upp till 2 000              | Upp till 2 000              |
| Dataflöde per disk | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek| Upp till 300 MiB/sek | Upp till 500 MiB/sek | Upp till 500 MiB/sek |

**Hanterade virtuella datorn SSD: er**

| Standard SSD-disktyp  | E10               | E15               | E20             | E30              | E40              | E50              | E60 *             | E70 *             | E80 *             |
|---------------------|---------------------|---------------------|-----------------|------------------|------------------|------------------|-------------------|-------------------|-------------------|
| Diskstorleken i GiB    | 128                 | 256                 | 512             | 1,024            | 2 048            | 4 095            | 8 192             | 16 384            | 32 767            |
| IOPS per disk       | Upp till 500           | Upp till 500           | Upp till 500       | Upp till 500        | Upp till 500        | Upp till 500        | Upp till 1 300       | Upp till 2 000       | Upp till 2 000       |
| Dataflöde per disk | Upp till 60 MB per sekund     | Upp till 60 MB per sekund     | Upp till 60 MB per sekund | Upp till 60 MB per sekund  | Upp till 60 MB per sekund  | Upp till 60 MB per sekund  | Upp till 300 MiB/sek | Upp till 500 MiB/sek | Upp till 500 MiB/sek |

**Hanterade premiumdatordiskar för virtuella datorer: gränser per disk**

| Premium-disktyp  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60 *             | P70 *             | P80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Diskstorleken i GiB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2 048     | 4 095    | 8 192     | 16 384     | 32 767     |
| IOPS per disk       | Upp till 120 | Upp till 240              | Upp till 500              | Upp till 1 100 | Upp till 2 300              | Upp till 5 000              | Upp till 7 500             | Upp till 7 500              | Upp till 12 500              | Upp till 15 000              | Upp till 20 000              |
| Dataflöde per disk | Upp till 25 MiB/sek | Upp till 50 MiB/sek | Upp till 100 MiB/sek | Upp till 125 MiB/sek | Upp till 150 MiB/sek | Upp till 200 MiB/sek | Upp till 250 MiB/sek | Upp till 250 MiB/sek| Upp till 480 MiB/sek | Upp till 750 MiB/sek | Upp till 750 MiB/sek |

**Hanterade virtuella Premium-datordiskar: gränser per virtuell dator**

| Resurs | Standardgräns |
| --- | --- |
| Högsta IOPS per virtuell dator |80 000 IOPS med virtuell GS5-dator |
| Högsta dataflöde per virtuell dator |2 000 MB/s med virtuella GS5-datorer |
