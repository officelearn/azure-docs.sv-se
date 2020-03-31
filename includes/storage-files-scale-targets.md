---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8a8619da831dfa5b240bd93d3a046c49cc30affa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76901402"
---
| Resurs | Standardfilresurser | Premium-filresurser |
|----------|---------------|------------------------------------------|
| Minsta storlek på en filresurs | Inget minimum; betala när du går | 100 Gib; avsättningar |
| Maximal storlek på en filresurs | 100 TiB*, 5 TiB | 100 TiB (100) |
| Maximal storlek för en fil i en filresurs | 1 TiB | 1 TiB |
| Maximalt antal filer i en filresurs | Obegränsad | Obegränsad |
| Maximalt IOPS per aktie | 10 000 IOPS*, 1 000 IOPS | 100 000 IOPS |
| Maximalt antal lagrade åtkomstprinciper per filresurs | 5 | 5 |
| Måldataflöde för en enskild filresurs | upp till 300 MiB/sek*, upp till 60 MiB/sek,  | Se värden för ingående in- och utgående premiumfilar|
| Maximal utgående för en enskild filresurs | Se standarddataflöde för filresursmål | Upp till 6 204 MiB/s |
| Maximal inträngning för en enskild filresurs | Se standarddataflöde för filresursmål | Upp till 4 136 MiB/s |
| Maximalt öppet handtag per fil | 2 000 öppna handtag | 2 000 öppna handtag |
| Maximalt antal ögonblicksbilder av resurs | 200 ögonblicksbilder av resurs | 200 ögonblicksbilder av resurs |
| Maximal objektnamnslängd (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Maximal sökvägskomponent (i sökvägen \A\B\C\D är varje bokstav en komponent) | 255 tecken | 255 tecken |

\*Tillgänglig i de flesta regioner, se [Regional tillgänglighet](../articles/storage/files/storage-files-planning.md#regional-availability) för information om tillgängliga regioner.
