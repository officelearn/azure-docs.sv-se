---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "67187332"
---
**Premium-ohanterade virtuella dator diskar: gränser per konto**

| Resurs | Standardgräns |
| --- | --- |
| Total diskkapacitet per konto |35 TB |
| Total kapacitet för ögonblicksbilder per konto |10 TB |
| Maximal bandbredd per konto (ingress + utgående)<sup>1</sup> |<= 50 Gbit/s |

<sup>1</sup>*inkommande* avser alla data från begär Anden som skickas till ett lagrings konto. *Utgående* avser alla data från svar som tas emot från ett lagrings konto.

**Premium-ohanterade virtuella dator diskar: gränser per disk**

| Premium Storage-disktyp | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Diskstorlek |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 GiB (2 TB)|4 095 GiB (4 TB)|
| Maximalt antal IOPS per disk |500 |2 300 |5 000 |7 500 |7 500 |
| Maximalt data flöde per disk |100 MB/sek. | 150 MB/sek |200 MB/sek |250 MB/sek |250 MB/sek |
| Maximalt antal diskar per lagrings konto |280 |70 |35 | 17 | 8 |

**Premium-ohanterade virtuella dator diskar: gränser per virtuell dator**

| Resurs | Standardgräns |
| --- | --- |
| Maximalt antal IOPS per virtuell dator |80 000 IOPS med GS5 VM |
| Maximalt data flöde per virtuell dator |2 000 MB/sekund med GS5 VM |

