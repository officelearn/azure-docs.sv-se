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
ms.openlocfilehash: 52dbf03c7d4c2c2de565d6793fc867cf5b886db3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74002776"
---
| Premium SSD storlekar | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Disk storlek i GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1 024 | 2 048 | 4 096 | 8 192 | 16 384 | 32 767 |
| IOPS per disk | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2 300 | 5 000 | 7 500 | 7 500 | 16 000 | 18 000 | 20 000 |
| Dataflöde per disk | 25 MiB/SEK | 25 MiB/SEK | 25 MiB/SEK | 25 MiB/SEK | 50 MiB/SEK | 100 MiB/SEK | 125 MiB/SEK | 150 MiB/SEK | 200 MiB/SEK | 250 MiB/SEK | 250 MiB/SEK| 500 MiB/SEK | 750 MiB/SEK | 900 MiB/SEK |
| Max burst-IOPS per disk * * | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 |
| Max burst-genomflöde per disk * * | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK |
| Maximal burst-varaktighet * * | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*anger en disk storlek som för närvarande finns i för hands versions information se [nya disk storlekar: hanterade och ohanterade](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).

\** anger en funktion som för närvarande finns i för hands version. mer information finns i [disk burst](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) .
