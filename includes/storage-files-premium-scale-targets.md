---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057789"
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

|Område  |Standard fil  |Premium-fil  |
|---------|---------|---------|
|Storlek     |1 TiB         |4 TiB         |
|Max IOPS per fil      |1 000         |5 000         |
|Samtidiga referenser     |2 000         |2 000         |
|Utgående     |Se standard fil data flödes värden         |300 MiB/SEK         |
|Ingress     |Se standard fil data flödes värden         |200 MiB/SEK         |
|Dataflöde     |Upp till 60 MiB/s         |Se ingångs-/utgående värden för Premium filen         |