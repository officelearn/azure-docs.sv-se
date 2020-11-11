---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: a71762010984928b93c19c7256c2ba4f0fe0f64b
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503937"
---
| Resurs | Standard fil resurser\* | Premium fil resurser |
|----------|---------------|------------------------------------------|
| Minsta storlek på en fil resurs | Inget minimum; betala per användning | 100 GiB; etablerats |
| Maximal storlek på en fil resurs | 100 TiB \* \* , 5 TIB | 100 TiB |
| Maximal storlek på en fil i en fil resurs | 1 TiB | 4 TiB |
| Maximalt antal filer i en fil resurs | Obegränsad | Obegränsad |
| Maximalt antal IOPS per resurs | 10 000 IOPS \* \* , 1 000 IOPS-eller 100-begäranden i 100 MS | 100 000 IOPS |
| Maximalt antal lagrade åtkomst principer per fil resurs | 5 | 5 |
| Mål data flöde för en enskild fil resurs | upp till 300 MiB/s \* \* , upp till 60 MIB/s,  | Se ingress och utgående värden för Premium-filresurs|
| Maximalt utgående för en enskild fil resurs | Se standard data flöde för fil resurs | Upp till 6 204 MiB/s |
| Maximalt antal inkommande data för en enda fil resurs | Se standard data flöde för fil resurs | Upp till 4 136 MiB/s |
| Maximalt antal öppna referenser per fil eller katalog | 2 000 öppna referenser | 2 000 öppna referenser |
| Maximalt antal resurs ögonblicks bilder | 200 resurs ögonblicks bilder | 200 resurs ögonblicks bilder |
| Namn längd för maximalt objekt (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Maximal Sök vägs komponent (i sökvägen \A\B\C\D är varje bokstav en komponent) | 255 tecken | 255 tecken |
| Gräns för hårda länkar (endast NFS) | E.t. | 178 |

\* Gränserna för standard fil resurser gäller för alla tre av de nivåer som är tillgängliga för standard fil resurser: transaktion optimerad, frekvent och låg frekvent.

\*\* Standard-fil resurser är 5 TiB, se [Aktivera och skapa stora fil resurser](../articles/storage/files/storage-files-how-to-create-large-file-share.md) för information om hur du ökar standard fil resurserna skala upp till 100 TIB.
