---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: 5ac7982d306125804fc5b7873e537f9381f717cb
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279975"
---
**Ohanterade virtuella Premium-datordiskar: gränser per konto**

| Resurs | Standardgräns |
| --- | --- |
| Total diskkapacitet per konto |35 TB |
| Total kapacitet för ögonblicksbilder per konto |10 TB |
| Högsta bandbredd per konto (ingående + utgående<sup>1</sup>) |<= 50 Gbit/s |

<sup>1</sup>*Ingående* syftar på alla data (förfrågningar) som skickas till ett lagringskonto. *Utgående* syftar på alla data (svar) som tas emot från ett lagringskonto.

**Ohanterade virtuella Premium-datordiskar: gränser per disk**

| Premium Storage-disktyp | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Diskstorlek |128 GiB |512 GiB |1 024 GiB (1 TB) |2 048 giB (2 TB)|4 095 giB (4 TB)|
| Högsta IOPS per disk |500 |2 300 |5000 |7500 |7500 |
| Högsta dataflöde per disk |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Högsta antal diskar per lagringskonto |280 |70 |35 | 17 | 8 |

**Ohanterade virtuella Premium-datordiskar: gränser per virtuell dator**

| Resurs | Standardgräns |
| --- | --- |
| Högsta IOPS per virtuell dator |80 000 IOPS med virtuell GS5-dator |
| Högsta dataflöde per virtuell dator |2 000 MB/s med virtuella GS5-datorer |

