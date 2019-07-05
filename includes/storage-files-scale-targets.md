---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9f259c3e403e933c847ac56000b1db2cd594caf5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449943"
---
| Resource | Standard-filresurser | Premium-filresurser |
|----------|---------------|------------------------------------------|
| Minsta storlek på en filresurs | Någon lägsta gräns; betala | 100 giB; etablerad |
| Maximal storlek på en filresurs | 5 TiB (GA), 100 TiB (förhandsversion) | 100 TiB |
| Maximal storlek på en fil i en filresurs | 1 TiB | 1 TiB |
| Maximalt antal filer i en filresurs | Obegränsad | Obegränsad |
| Högsta IOPS per resurs | 1 000 IOPS (GA), 10 000 IOPS (förhandsversion) | 100,000 IOPS |
| Maximalt antal lagrade åtkomstprinciper per fil dela | 5 | 5 |
| Måldataflöde för en enskild filresurs | Upp till 60 MiB/sek (GA), upp till 300 MiB/sek (förhandsversion) | Se premium filen resurs ingående och utgående värden|
| Maximalt utgående trafik för en enskild filresurs | Se standardfil måldataflöde för resursen | Upp till 6,204 MiB/s |
| Största ingående för en enskild filresurs | Se standardfil måldataflöde för resursen | Upp till 4,136 MiB/s |
| Maximal öppna referenser per fil | 2 000 öppna referenser | 2 000 öppna referenser |
| Maximalt antal resursögonblicksbilder | 200 resursögonblicksbilder | 200 resursögonblicksbilder |
| Namnlängd för maximal objekt (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Maximal pathname komponent (i sökvägen \A\B\C\D, varje bokstav är en komponent) | 255 tecken | 255 tecken |