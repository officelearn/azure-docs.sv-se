---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: df5d8ff941c742d546208c27c7af4e7bcbeec08a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073751"
---
| Resource | Standard-filresurser | Premium-filresurser (förhandsversion) |
|----------|---------------|------------------------------------------|
| Minsta storlek på en filresurs | Någon lägsta gräns; betala | 100 giB; etablerad |
| Maximal storlek på en filresurs | 5 TiB | 100 TiB |
| Maximal storlek på en fil i en filresurs | 1 TiB | 1 TiB |
| Maximalt antal filer i en filresurs | Obegränsad | Obegränsad |
| Högsta IOPS per resurs | 1,000 IOPS | 100,000 IOPS |
| Maximalt antal lagrade åtkomstprinciper per fil dela | 5 | 5 |
| Måldataflöde för en enskild filresurs | Upp till 60 MiB/sek | Se premium filen resurs ingående och utgående värden|
| Maximalt utgående trafik för en enskild filresurs | Se standardfil måldataflöde för resursen | Upp till 6,204 MiB/s |
| Största ingående för en enskild filresurs | Se standardfil måldataflöde för resursen | Upp till 4,136 MiB/s |
| Maximal öppna referenser per fil | 2 000 öppna referenser | 2 000 öppna referenser |
| Maximalt antal resursögonblicksbilder | 200 resursögonblicksbilder | 200 resursögonblicksbilder |
| Namnlängd för maximal objekt (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Maximal pathname komponent (i sökvägen \A\B\C\D, varje bokstav är en komponent) | 255 tecken | 255 tecken |