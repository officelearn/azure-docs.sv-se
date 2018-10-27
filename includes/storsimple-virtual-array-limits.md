---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164773"
---
| **Gräns för identifierare** | **Gränsen** | **Kommentarer** |
| --- | --- | --- |
| Total kapacitet (inklusive molnet) |Upp till 64 TB per virtuell enhet |Du kan redundansväxla en fullständig StorSimple Virtual Array till en annan tom-matris. Se till att du har tillräckligt med utrymme på enheten för att slutföra den här åtgärden om du försöker återställa till samma enhet. När du har överskridit 32 TB, kan du inte återställa till samma enhet. |
| Maximalt antal lagringskontouppgifter per enhet |1 | |
| Maximalt antal volymer/resurser |16 | |
| Minsta storlek på en nivåindelad resurs |500 GB | |
| Minsta storlek på en nivåindelad volym |500 GB | |
| Maximal storlek för en nivåindelad resurs |20 TB | |
| Maximal storlek på en nivåindelad volym |5 TB | |
| Minsta storlek på en lokalt Fäst resurs |50 GB | |
| Minsta storlek på en lokalt Fäst volym |50 GB | |
| Maximal storlek på en lokalt Fäst resurs |2 TB | |
| Maximal storlek på en lokalt Fäst volym |200 GB | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maxantalet åtkomstkontrollposter per enhet |64 | |
| Hur många säkerhetskopior som finns kvar av den virtuella enheten i *.backups* mapp för filserver |5 |Detta omfattar den senaste schemalagda (genereras av standardprincip för säkerhetskopiering) och manuella säkerhetskopieringar. |
| Maximala antalet schemalagda säkerhetskopieringar som kvarhålls av enheten |55 |30 daglig säkerhetskopiering<br>12 månatliga säkerhetskopior<br>13 årliga säkerhetskopior |
| Maxantalet manuella säkerhetskopior som finns kvar av enheten |45 | |
| Högsta antalet filer per resurs för en filserver |1 miljon |När du utför en återställning av enheten, är återställningen går proportion till antalet filer för alla resurser på enheten. |
| Högsta antalet filer per volym för en iSCSI-server |1 miljon | |
| Högsta antalet filer per virtuell matris |4 miljoner | |
| Återställ återställningstid |Snabb återställning |Återställning som baseras på den termiska kartan och beror på volymens storlek.<br>Säkerhetskopieringsåtgärder kan inträffa när en återställningsåtgärd pågår. |

