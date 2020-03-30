---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187374"
---
| **Gräns för identifierare** | **Gräns** | **Kommentar** |
| --- | --- | --- |
| Total kapacitet (inklusive moln) |Upp till 64 TB per virtuell enhet |Du kan växla över en fullständig StorSimple Virtual Array till en annan tom matris. Om du försöker återställa till samma enhet kontrollerar du att du har tillräckligt med utrymme på enheten för att slutföra den här åtgärden. När du har överskridit 32 TB kan du inte återställa till samma enhet. |
| Maximalt antal autentiseringsuppgifter för lagringskonto per enhet |1 | |
| Maximalt antal volymer/aktier |16 | |
| Minsta storlek för en nivåindelad resurs |500 GB | |
| Minsta storlek för en nivåindelad volym |500 GB | |
| Maximal storlek på en nivåindelad resurs |20 TB | |
| Maximal storlek för en nivåindelad volym |5 TB | |
| Minsta storlek för en lokalt fäst resurs |50 GB | |
| Minsta storlek för en lokalt fäst volym |50 GB | |
| Maximal storlek för en lokalt fäst resurs |2 TB | |
| Maximal storlek för en lokalt fäst volym |200 GB | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maximalt antal åtkomstkontrollposter per enhet |64 | |
| Maximalt antal säkerhetskopior som behålls av den virtuella enheten i mappen *säkerhetskopior* för filserver |5 |Detta inkluderar de senaste schemalagda (som genereras av standardsäkerhetsprincipen för säkerhetskopiering) och manuella säkerhetskopior. |
| Maximalt antal schemalagda säkerhetskopior som behålls av enheten |55 |30 dagliga säkerhetskopior<br>12 månatliga säkerhetskopior<br>13 årliga säkerhetskopior |
| Maximalt antal manuella säkerhetskopior som enheten behåller |45 | |
| Maximalt antal filer per resurs för en filserver |1 miljon |När du utför en enhetsåterställning är återställningstiderna proportionella mot antalet filer över alla resurser på enheten. |
| Maximalt antal filer per volym för en iSCSI-server |1 miljon | |
| Maximalt antal filer per virtuell matris |4 miljoner | |
| Återställa återställningstid |Snabb återställning |Återställningen är baserad på värmekartan och beror på volymstorleken.<br>Säkerhetskopieringsåtgärder kan utföras medan en återställningsåtgärd pågår. |

