---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.openlocfilehash: 213ecee34df46c0a408e7034a07ab864c96b340b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114530"
---
| Resource | Standard-filresurser | Premium-filresurser (förhandsversion) |
|----------|---------------|------------------------------------------|
| Minsta storlek på en filresurs | Någon lägsta gräns; betala | 100 giB; etablerad |
| Maximal storlek på en filresurs | 5 TiB | 100 TiB |
| Maximal storlek på en fil i en filresurs | 1 TiB | 1 TiB |
| Maximalt antal filer i en filresurs | Ingen gräns | Ingen gräns |
| Högsta IOPS per resurs | 1,000 IOPS | 100,000 IOPS |
| Maximalt antal lagrade åtkomstprinciper per fil dela | 5 | 5 |
| Måldataflöde för en enskild filresurs | Upp till 60 MiB/sek | Se premium filen resurs ingående och utgående värden|
| Maximalt utgående trafik för en enskild filresurs | Se standardfil måldataflöde för resursen | Upp till 6,204 MiB/s |
| Största ingående för en enskild filresurs | Se standardfil måldataflöde för resursen | Upp till 4,136 MiB/s |
| Maximal öppna referenser per fil | 2 000 öppna referenser | 2 000 öppna referenser |
| Maximalt antal resursögonblicksbilder | 200 resursögonblicksbilder | 200 resursögonblicksbilder |
| Namnlängd för maximal objekt (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Maximal pathname komponent (i sökvägen \A\B\C\D, varje bokstav är en komponent) | 255 tecken | 255 tecken |