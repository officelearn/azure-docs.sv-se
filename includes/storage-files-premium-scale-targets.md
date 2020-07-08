---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "71180076"
---
#### <a name="additional-premium-file-share-level-limits"></a>Ytterligare begränsningar för Premium fil resurs nivå

|Område  |Mål  |
|---------|---------|
|Minsta storlek för ökning/minskning    |1 GiB      |
|Bas linje IOPS    |1 IOPS per GiB, upp till 100 000|
|IOPS-burst    |3x IOPS per GiB, upp till 100 000|
|Utgående hastighet         |60 MiB/s + 0,06 * etablerad GiB        |
|Ingress-pris| 40 MiB/s + 0,04 * etablerad GiB |

#### <a name="file-level-limits"></a>Fil nivå begränsningar

|Område  |Premium-fil  |Standard fil |
|---------|---------|---------|
|Storlek                  |1 TiB         |1 TiB|
|Max IOPS per fil     |5 000         |1,000|
|Samtidiga referenser    |2 000         |2 000|
|Utgående  |300 MiB/SEK|      Se standard fil data flödes värden|
|Ingress  |200 MiB/SEK| Se standard fil data flödes värden|
|Dataflöde| Se ingångs-/utgående värden för Premium filen| Upp till 60 MiB/s|
