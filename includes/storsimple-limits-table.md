---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553829"
---
| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal autentiseringsuppgifter för lagringskonto |64 | |
| Maximalt antal volymbehållare |64 | |
| Maxantalet volymer |255 | |
| Maximalt antal scheman per bandbreddsmall |168 |Ett schema för varje timme, varje dag i veckan. |
| Maximal storlek på en nivåindelad volym på fysiska enheter |64 TB för StorSimple 8100 och StorSimple 8600 |StorSimple 8100 och StorSimple 8600 är fysiska enheter. |
| Maximal storlek på en nivåindelad volym på virtuella enheter i Azure |30 TB för StorSimple 8010 <br></br> 64 TB för StorSimple 8020 |StorSimple 8010 och StorSimple 8020 är virtuella enheter i Azure som använder Standard storage och Premium storage kan respektive. |
| Maximal storlek på en lokalt Fäst volym på fysiska enheter |9 TB för StorSimple 8100 <br></br> 24 TB för StorSimple 8600 |StorSimple 8100 och StorSimple 8600 är fysiska enheter. |
| Maximalt antal iSCSI-anslutningar |512 | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maxantalet åtkomstkontrollposter per enhet |64 | |
| Maxantalet volymer per princip för säkerhetskopiering |24 | |
| Hur många säkerhetskopior som finns kvar per princip för säkerhetskopiering |64 | |
| Maximalt antal scheman per princip för säkerhetskopiering |10 | |
| Högsta antalet ögonblicksbilder av valfri typ som kan bevaras per volym |256 |Den här mängden innehåller lokala ögonblicksbilder och molnbaserade ögonblicksbilder. |
| Maximalt antal ögonblicksbilder som kan finnas i valfri enhet |10 000 | |
| Högsta antalet volymer som kan bearbetas parallellt för säkerhetskopiering, återställning eller klona |16 |<ul><li>Om det finns fler än 16 volymer, de är i ordningsföljd när bearbetning fack blir tillgängliga.</li><li>Nya säkerhetskopior av en klonade eller en återställd nivåindelad volym kan inte ske förrän åtgärden är klar. För en lokal volym tillåts säkerhetskopior när volymen är online.</li></ul> |
| Återställa och klona Återställ tiden för nivåindelade volymer |< 2 minuter |<ul><li>Volymen är tillgänglig inom två minuter för en återställning eller Kloningen, oavsett volymens storlek.</li><li>Prestanda för volymen kanske inledningsvis långsammare än normalt eftersom de flesta data och metadata som fortfarande finns i molnet. Prestanda kan öka som dataflöden från molnet till StorSimple-enheten.</li><li>Total tid för att hämta metadata beror på allokerade volymens storlek. Metadata hämtas automatiskt till enheten i bakgrunden med en hastighet av 5 minuter per TB allokerade volymdata. Priset kan påverkas av internetbandbredd till molnet.</li><li>Restore- eller kopieringen är klar när alla metadata som finns på enheten.</li><li>Säkerhetskopieringsåtgärder kan inte utföras förrän återställningen eller Kloningen är helt klar. |
| Återställ återställningstid för lokalt fästa volymer |< 2 minuter |<ul><li>Volymen är tillgänglig inom två minuter för återställningsåtgärden, oavsett volymens storlek.</li><li>Prestanda för volymen kanske inledningsvis långsammare än normalt eftersom de flesta data och metadata som fortfarande finns i molnet. Prestanda kan öka som dataflöden från molnet till StorSimple-enheten.</li><li>Total tid för att hämta metadata beror på allokerade volymens storlek. Metadata hämtas automatiskt till enheten i bakgrunden med en hastighet av 5 minuter per TB allokerade volymdata. Priset kan påverkas av internetbandbredd till molnet.</li><li>Till skillnad från nivåindelade volymer om det finns lokalt fixerade volymer hämtas volymdata också lokalt på enheten. Återställningen är klar när alla volymdata har trätt till enheten.</li><li>Återställningsåtgärden kan vara långt och total tid för att slutföra återställningen beror på storleken på den etablerade lokal volymen, internetbandbredden och befintliga data på enheten. Säkerhetskopieringsåtgärder på lokalt fixerad volym tillåts medan återställningen pågår. |
| Tunn-återställning tillgänglighet |Senaste redundans | |
| Maximalt antal klienter skrivning/dataflöde, när hanteras från SSD-nivån * |920/720 MB per sekund med en enda 10 gigabit Ethernet-gränssnitt |Upp till två gånger med MPIO och två nätverksgränssnitt. |
| Maximalt antal klienter skrivning/dataflöde, när hanteras från HDD-nivån * |120/250 MB/sek | |
| Maximalt antal klienter skrivning/dataflöde, när hanteras från molnet nivån * |11/41 MB/sek |Läs dataflödet beror på klienter genererar och bevara tillräcklig i/o-ködjup. |

&#42;Maximalt dataflöde per i/o-typ har mätt med 100 procent Läs- och 100 procent skrivåtgärder scenarier. Det verkliga dataflödet kan vara lägre och beror på i/o blanda och nätverks-villkor.

