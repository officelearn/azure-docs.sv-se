---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334974"
---
**Premium ohanterat virtuella datordiskar: Gränser per konto**

| Resurs | Gräns |
| --- | --- |
| Total diskkapacitet per konto |35 TB |
| Total kapacitet för ögonblicksbilder per konto |10 TB |
| Maximal bandbredd per konto (ingående + utgående)<sup>1</sup> |<= 50 Gbit/s |

<sup>1</sup>*Ingress* refererar till alla data från begäranden som skickas till ett lagringskonto. *Utgående* refererar till alla data från svar som tas emot från ett lagringskonto.

**Premium ohanterat virtuella datordiskar: Gränser per disk**

| Premium Storage-disktyp | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Diskstorlek |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 GiB (2 TB)|4 095 GiB (4 TB)|
| Maximal IOPS per disk |500 |2 300 |5 000 |7 500 |7 500 |
| Maximalt dataflöde per disk |100 MB/sek | 150 MB/sek |200 MB/sek |250 MB/sek. |250 MB/sek. |
| Maximalt antal diskar per lagringskonto |280 |70 |35 | 17 | 8 |

**Premium ohanterat virtuella datordiskar: Gränser per virtuell dator**

| Resurs | Gräns |
| --- | --- |
| Maximal IOPS per virtuell dator |80 000 IOPS med GS5 VM |
| Maximalt dataflöde per virtuell dator |2 000 MB/sek med GS5 VM |

