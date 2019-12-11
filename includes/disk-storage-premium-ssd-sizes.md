---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c88cba57ed987074c0dde3c8b251f54393667740
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "75002681"
---
| Premium SSD storlekar | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Disk storlek i GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2 048 | 4 096 | 8 192 | 16 384 | 32 767 |
| IOPS per disk | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2 300 | 5 000 | 7 500 | 7 500 | 16,000 | 18 000 | 20,000 |
| Dataflöde per disk | 25 MiB/SEK | 25 MiB/SEK | 25 MiB/SEK | 25 MiB/SEK | 50 MiB/SEK | 100 MiB/SEK | 125 MiB/SEK | 150 MiB/sec | 200 MiB/SEK | 250 MiB/SEK | 250 MiB/SEK| 500 MiB/SEK | 750 MiB/SEK | 900 MiB/SEK |
| Max burst-IOPS per disk * * | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 |
| Max burst-genomflöde per disk * * | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK |
| Maximal burst-varaktighet * * | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*anger en disk storlek som för närvarande finns i för hands versions information se [nya disk storlekar: hanterade och ohanterade](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).

\*\*anger en funktion som för närvarande finns i för hands version, se [disk burst](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) för mer information.
