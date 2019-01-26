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
ms.openlocfilehash: 8e9d04c7d374aabf3870a3260bc1f5d808b8ee9c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54906347"
---
**Standard HDD hanterade diskar**

| Disk av standardtyp  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60 *             | S70 *             | S80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Diskstorleken i GiB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8 192     | 16,384     | 32,767     |
| IOPS per disk       | Upp till 500              | Upp till 500              | Upp till 500              | Upp till 500 | Upp till 500              | Upp till 500              | Upp till 500             | Upp till 500              | Upp till 1 300              | Upp till 2 000              | Upp till 2 000              |
| Dataflöde per disk | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek | Upp till 60 MiB/sek| Upp till 300 MiB/sek | Upp till 500 MiB/sek | Upp till 500 MiB/sek |

**Standard SSD Managed Disks**

| Standard SSD-disktyp | E4                | E6                | E10               | E15               | E20             | E30              | E40              | E50              | E60 *             | E70 *             | E80 *             |
|------------------------|-------------------|-------------------|-------------------|-------------------|-----------------|------------------|------------------|------------------|-------------------|-------------------|-------------------|
| Diskstorleken i GiB       | 32                | 64                | 128               | 256               | 512             | 1,024            | 2,048            | 4,095            | 8 192             | 16,384            | 32,767            |
| IOPS per disk          | Upp till 120         | Upp till 240         | Upp till 500         | Upp till 500         | Upp till 500       | Upp till 500        | Upp till 500        | Upp till 500        | Upp till 1 300       | Upp till 2 000       | Upp till 2 000       |
| Dataflöde per disk    | Upp till 25 MB/sek   | Upp till 50 MB/sek   | Upp till 60 MB per sekund   | Upp till 60 MB per sekund   | Upp till 60 MB per sekund | Upp till 60 MB per sekund  | Upp till 60 MB per sekund  | Upp till 60 MB per sekund  | Upp till 300 MiB/sek | Upp till 500 MiB/sek | Upp till 500 MiB/sek |

**Premium SSD Managed Disks: gränser per disk**

| Premium-disktyp  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60 *             | P70 *             | P80 *             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Diskstorleken i GiB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8 192     | 16,384     | 32,767     |
| IOPS per disk       | Upp till 120 | Upp till 240              | Upp till 500              | Upp till 1 100 | Upp till 2 300              | Upp till 5 000              | Upp till 7 500             | Upp till 7 500              | Upp till 12 500              | Upp till 15 000              | Upp till 20 000              |
| Dataflöde per disk | Upp till 25 MiB/sek | Upp till 50 MiB/sek | Upp till 100 MiB/sek | Upp till 125 MiB/sek | Upp till 150 MiB/sek | Upp till 200 MiB/sek | Upp till 250 MiB/sek | Upp till 250 MiB/sek| Upp till 480 MiB/sek | Upp till 750 MiB/sek | Upp till 750 MiB/sek |

**Premium SSD Managed Disks: per virtuell dator begränsar**

| Resurs | Standardgräns |
| --- | --- |
| Högsta IOPS per virtuell dator |80 000 IOPS med virtuell GS5-dator |
| Högsta dataflöde per virtuell dator |2 000 MB/s med virtuella GS5-datorer |
