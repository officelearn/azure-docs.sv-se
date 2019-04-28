---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386343"
---
**Premium ohanterade virtuella diskar: Gränserna per konto**

| Resurs | Standardgräns |
| --- | --- |
| Total diskkapacitet per konto |35 TB |
| Total kapacitet för ögonblicksbilder per konto |10 TB |
| Maximal bandbredd per konto (ingående + utgående)<sup>1</sup> |<= 50 Gbit/s |

<sup>1</sup>*ingående* syftar på alla data från förfrågningar som skickas till ett lagringskonto. *Utgående* syftar på alla data från svar som tas emot från ett lagringskonto.

**Premium ohanterade virtuella diskar: Gränser per disk**

| Premium Storage-disktyp | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Diskstorlek |128 GiB |512 GiB |1 024 giB (1 TB) |2 048 giB (2 TB)|4 095 giB (4 TB)|
| Högsta IOPS per disk |500 |2 300 |5 000 |7 500 |7 500 |
| Högsta dataflöde per disk |100 MB per sekund | 150 MB/sec |200 MB/sek |250 MB/sec |250 MB/sec |
| Maximalt antal diskar per lagringskonto |280 |70 |35 | 17 | 8 |

**Premium ohanterade virtuella diskar: Gränserna per virtuell dator**

| Resurs | Standardgräns |
| --- | --- |
| Högsta IOPS per virtuell dator |80 000 IOPS med virtuell GS5-dator |
| Maximalt dataflöde per virtuell dator |2 000 MB/s med virtuell GS5-dator |

