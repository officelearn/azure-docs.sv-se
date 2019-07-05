---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542667"
---
#### <a name="additional-premium-file-share-level-limits"></a>Ytterligare premium filen resursen på gränser

|Område  |Mål  |
|---------|---------|
|Minimistorlek öka/minska    |1 GiB      |
|Baslinje-IOPS    |1 IOPS per GiB, upp till 100 000|
|IOPS bursting    |3 x IOPS per GiB, upp till 100 000|
|Priset för utgående trafik         |60 MiB/s + 0.06 * etablerats GiB        |
|Ingångshändelser| 40 MiB/s + 0.04 * etablerats GiB |

#### <a name="file-level-limits"></a>Filen på gränser

|Område  |Premium-fil  |Standard-fil |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|Max IOPS per fil     |5,000         |1,000|
|Samtidiga handtag    |2,000         |2,000|
|Ingress  |300 MiB/sec|      Se standardfil dataflöde värden|
|Egress   |200 Mib/sec| Se standardfil dataflöde värden|
|Dataflöde| Se premium filvärden för ingående/utgående trafik| Upp till 60 MiB/sek|