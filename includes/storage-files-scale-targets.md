---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8a8619da831dfa5b240bd93d3a046c49cc30affa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901402"
---
| Resurs | Standard fil resurser | Premium fil resurser |
|----------|---------------|------------------------------------------|
| Minsta storlek på en fil resurs | Inget minimum; betala per användning | 100 GiB; etablerats |
| Maximal storlek på en fil resurs | 100 TiB *, 5 TiB | 100 TiB |
| Maximal storlek på en fil i en fil resurs | 1 TiB | 1 TiB |
| Maximalt antal filer i en fil resurs | Obegränsat | Obegränsat |
| Maximalt antal IOPS per resurs | 10 000 IOPS *, 1 000 IOPS | 100 000 IOPS |
| Maximalt antal lagrade åtkomst principer per fil resurs | 5 | 5 |
| Mål data flöde för en enskild fil resurs | upp till 300 MiB/SEK *, upp till 60 MiB/s,  | Se ingress och utgående värden för Premium-filresurs|
| Maximalt utgående för en enskild fil resurs | Se standard data flöde för fil resurs | Upp till 6 204 MiB/s |
| Maximalt antal inkommande data för en enda fil resurs | Se standard data flöde för fil resurs | Upp till 4 136 MiB/s |
| Maximalt antal öppna referenser per fil | 2 000 öppna referenser | 2 000 öppna referenser |
| Maximalt antal resurs ögonblicks bilder | 200 resurs ögonblicks bilder | 200 resurs ögonblicks bilder |
| Namn längd för maximalt objekt (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Maximal Sök vägs komponent (i sökvägen \A\B\C\D är varje bokstav en komponent) | 255 tecken | 255 tecken |

\* tillgängliga i de flesta regioner, se [regional tillgänglighet](../articles/storage/files/storage-files-planning.md#regional-availability) för information om tillgängliga regioner.
