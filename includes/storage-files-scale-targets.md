---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 99314538f90404d7c2b72da0dd2da2d8ac60a08a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805194"
---
| Resurs | Standard-filresurser | Premium-filresurser (förhandsversion) |
|----------|---------------|------------------------------------------|
| Minsta storlek på en filresurs | Någon lägsta gräns; betala | 100 giB; etablerad |
| Maximal storlek på en filresurs | 5 TiB | 5 TiB (offentlig förhandsversion), 100 TiB (begränsad offentlig förhandsversion) |
| Maximal storlek på en fil i en filresurs | 1 TiB | 1 TiB |
| Maximalt antal filer i en filresurs | Obegränsad | Obegränsad |
| Högsta IOPS per resurs | 1,000 IOPS | 5,120 grundläggande IOPS med 15 360 burst gräns (förhandsversion), 100 000 IOPS (begränsad offentlig förhandsversion)|
| Maximalt antal lagrade åtkomstprinciper per fil dela | 5 | 5 |
| Måldataflöde för en enskild filresurs | Upp till 60 MiB/sek | Se premium filen resurs ingående och utgående värden|
| Maximalt utgående trafik för en enskild filresurs | Se standardfil måldataflöde för resursen | Upp till 368 MiB/s (förhandsversion), upp till 6,204 MiB/s (begränsad offentlig förhandsversion) |
| Största ingående för en enskild filresurs | Se standardfil måldataflöde för resursen | Upp till 245 MiB/s (förhandsversion), upp till 4,136 MiB/s (begränsad offentlig förhandsversion) |
| Maximal öppna referenser per fil | 2 000 öppna referenser | 2 000 öppna referenser |
| Maximalt antal resursögonblicksbilder | 200 resursögonblicksbilder | 200 resursögonblicksbilder |
| Namnlängd för maximal objekt (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Maximal pathname komponent (i sökvägen \A\B\C\D, varje bokstav är en komponent) | 255 tecken | 255 tecken |