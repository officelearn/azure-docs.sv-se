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
ms.openlocfilehash: cafd5a6de148b445031730d512b7927bea8c3740
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73795229"
---
| Premium SSD storlekar | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Disk storlek i GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1 024 | 2 048 | 4 096 | 8 192 | 16 384 | 32 767 |
| IOPS per disk | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2 300 | 5 000 | 7 500 | 7 500 | 16 000 | 18 000 | 20 000 |
| Dataflöde per disk | 25 MiB/SEK | 25 MiB/SEK | 25 MiB/SEK | 25 MiB/SEK | 50 MiB/SEK | 100 MiB/SEK | 125 MiB/SEK | 150 MiB/SEK | 200 MiB/SEK | 250 MiB/SEK | 250 MiB/SEK| 500 MiB/SEK | 750 MiB/SEK | 900 MiB/SEK |
| Max burst-IOPS per disk * * | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 | 3 500 |
| Max burst-genomflöde per disk * * | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK | 170 MiB/SEK |
| Maximal burst-varaktighet * * | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*anger en disk storlek som för närvarande finns i för hands versions information se [Premium diskar: hanterade och ohanterade](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#premium-disks-managed-and-unmanaged).

\** anger en funktion som för närvarande finns i för hands version. mer information finns i [disk burst](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) .
