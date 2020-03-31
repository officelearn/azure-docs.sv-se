---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180076"
---
#### <a name="additional-premium-file-share-level-limits"></a>Ytterligare begränsningar för delningsnivå för premiumfiler

|Område  |Mål  |
|---------|---------|
|Minsta storlek öka /minska    |1 GiB (gib)      |
|Baslinje IOPS    |1 IOPS per GiB, upp till 100 000|
|IOPS spricker    |3x IOPS per GiB, upp till 100 000|
|Utgående ränta         |60 MiB/s + 0,06 * avsedd GiB        |
|Inträngningshastighet| 40 MiB/s + 0,04 * avsedd GiB |

#### <a name="file-level-limits"></a>Begränsningar för filnivå

|Område  |Premium-fil  |Standardfil |
|---------|---------|---------|
|Storlek                  |1 TiB         |1 TiB|
|Max IOPS per fil     |5 000         |1,000|
|Samtidiga handtag    |2 000         |2 000|
|Utgående  |300 MiB/sek|      Visa standardvärden för fildataflöde|
|Ingress  |200 MiB/sek| Visa standardvärden för fildataflöde|
|Dataflöde| Se värden för ingående/utgående premiumfiler| Upp till 60 MiB/sek|
