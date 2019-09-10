---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "67187332"
---
**Premium-ohanterade virtuella dator diskar: Gräns per konto**

| Resource | Standardgräns |
| --- | --- |
| Total diskkapacitet per konto |35 TB |
| Total kapacitet för ögonblicksbilder per konto |10 TB |
| Maximal bandbredd per konto (ingress + utgående)<sup>1</sup> |<= 50 Gbit/s |

<sup>1</sup> *Ingress* syftar på alla data från begär Anden som skickas till ett lagrings konto. *Utgående* avser alla data från svar som tas emot från ett lagrings konto.

**Premium-ohanterade virtuella dator diskar: Begränsningar per disk**

| Premium Storage-disktyp | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Diskstorlek |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 GiB (2 TB)|4 095 GiB (4 TB)|
| Maximalt antal IOPS per disk |500 |2,300 |5 000 |7 500 |7,500 |
| Maximalt data flöde per disk |100 MB/SEK | 150 MB/SEK |200 MB/SEK |250 MB/SEK |250 MB/SEK |
| Maximalt antal diskar per lagrings konto |280 |70 |35 | 17 | 8 |

**Premium-ohanterade virtuella dator diskar: Gränser per virtuell dator**

| Resource | Standardgräns |
| --- | --- |
| Maximalt antal IOPS per virtuell dator |80 000 IOPS med GS5 VM |
| Maximalt data flöde per virtuell dator |2 000 MB/sekund med GS5 VM |

