---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 72b7bd722bc7634a707e10cbcd45768140ed527a
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55757549"
---
| Resurs | Standard-filresurser | Premium-filresurser (förhandsversion) |
|----------|---------------|------------------------------------------|
| Minsta storlek på en filresurs | (någon lägsta gräns, betala efter användning) | 100 giB |
| Maxstorlek på en filresurs | 5 TiB | 5 TiB |
| Maxstorlek på en fil i en filresurs | 1 TiB | 1 TiB |
| Maxantal filer i en filresurs | Obegränsad | Obegränsad |
| Max IOPS per resurs | 1000 IOPS | 5120 IOPS baslinje<br />15 360 IOPS med burst |
| Maxantal lagrade åtkomstprinciper per filresurs | 5 | 5 |
| Måldataflöde för en filresurs | Upp till 60 MiB/sek | Upp till 612 MiB/sek (etablerats) |
| Maximal öppna referenser per fil | 2 000 öppna referenser | 2 000 öppna referenser |
| Maximalt antal resursögonblicksbilder | 200 resursögonblicksbilder | 200 resursögonblicksbilder |
| Namnlängd för maximal objekt (kataloger och filer) | 2048 tecken | 2048 tecken |
| Maximal pathname komponent (i sökvägen \A\B\C\D, varje bokstav är en komponent) | 255 tecken | 255 tecken |