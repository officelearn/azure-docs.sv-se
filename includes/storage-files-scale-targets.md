---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536459"
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

\*Standard på standardfilresurser är 5 TiB, se [Aktivera och skapa stora filresurser](../articles/storage/files/storage-files-how-to-create-large-file-share.md) för information om hur du ökar standardfilresurser skala upp till 100 TiB.
