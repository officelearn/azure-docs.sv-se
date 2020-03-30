---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187741"
---
| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal autentiseringsuppgifter för lagringskonto |64 | |
| Maximalt antal volymbehållare |64 | |
| Maximalt antal volymer |255 | |
| Maximalt antal scheman per bandbreddsmall |168 |Ett schema för varje timme, alla dagar i veckan. |
| Maximal storlek på en nivåindelad volym på fysiska enheter |64 TB för StorSimple 8100 och StorSimple 8600 |StorSimple 8100 och StorSimple 8600 är fysiska enheter. |
| Maximal storlek för en nivåindelad volym på virtuella enheter i Azure |30 TB för StorSimple 8010 <br></br> 64 TB för StorSimple 8020 |StorSimple 8010 och StorSimple 8020 är virtuella enheter i Azure som använder standardlagring respektive Premium-lagring. |
| Maximal storlek på en lokalt fäst volym på fysiska enheter |9 TB för StorSimple 8100 <br></br> 24 TB för StorSimple 8600 |StorSimple 8100 och StorSimple 8600 är fysiska enheter. |
| Maximalt antal iSCSI-anslutningar |512 | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maximalt antal åtkomstkontrollposter per enhet |64 | |
| Maximalt antal volymer per säkerhetskopieringsprincip |24 | |
| Maximalt antal säkerhetskopior som behålls per säkerhetskopieringsprincip |64 | |
| Maximalt antal scheman per säkerhetskopieringsprincip |10 | |
| Maximalt antal ögonblicksbilder av alla typer som kan behållas per volym |256 |Det här beloppet inkluderar lokala ögonblicksbilder och ögonblicksbilder av molnet. |
| Maximalt antal ögonblicksbilder som kan finnas i en enhet |10 000 | |
| Maximalt antal volymer som kan bearbetas parallellt för säkerhetskopiering, återställning eller klon |16 |<ul><li>Om det finns fler än 16 volymer bearbetas de sekventiellt när bearbetningsplatser blir tillgängliga.</li><li>Nya säkerhetskopior av en klonad eller en återställd nivåindelade volym kan inte inträffa förrän åtgärden är klar. För en lokal volym tillåts säkerhetskopior när volymen är online.</li></ul> |
| Återställa och klona återställningstid för nivåindelad volym |<2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter efter en återställning eller kloning, oavsett volymstorlek.</li><li>Volymprestandan kan inledningsvis vara långsammare än normalt eftersom de flesta data och metadata fortfarande finns i molnet. Prestanda kan öka när data flödar från molnet till StorSimple-enheten.</li><li>Den totala tiden för hämtning av metadata beror på den allokerade volymstorleken. Metadata förs automatiskt in i enheten i bakgrunden med en hastighet av 5 minuter per TB allokerade volymdata. Den här hastigheten kan påverkas av Internet-bandbredd till molnet.</li><li>Återställnings- eller klonningsåtgärden är klar när alla metadata finns på enheten.</li><li>Säkerhetskopieringsåtgärder kan inte utföras förrän återställnings- eller klonningsåtgärden är helt klar. |
| Återställa återställningstid för lokalt fästa volymer |<2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter efter återställningen, oavsett volymstorlek.</li><li>Volymprestandan kan inledningsvis vara långsammare än normalt eftersom de flesta data och metadata fortfarande finns i molnet. Prestanda kan öka när data flödar från molnet till StorSimple-enheten.</li><li>Den totala tiden för hämtning av metadata beror på den allokerade volymstorleken. Metadata förs automatiskt in i enheten i bakgrunden med en hastighet av 5 minuter per TB allokerade volymdata. Den här hastigheten kan påverkas av Internet-bandbredd till molnet.</li><li>Till skillnad från nivåindelade volymer hämtas volymdata också lokalt på enheten om det finns lokalt fästa volymer. Återställningen är klar när alla volymdata har förts till enheten.</li><li>Återställningsåtgärderna kan vara långa och den totala tiden för att slutföra återställningen beror på storleken på den etablerade lokala volymen, internetbandbredden och befintliga data på enheten. Säkerhetskopieringsåtgärder på den lokalt fästa volymen tillåts medan återställningen pågår. |
| Tillgänglighet för tunn återställning |Senaste redundans | |
| Maximalt klientläsnings-/skrivflöde när det visas från SSD-nivån* |920/720 MB/sek med ett enda Ethernet-nätverksgränssnitt på 10 gigabit |Upp till två gånger med MPIO och två nätverksgränssnitt. |
| Maximalt klientläsnings-/skrivflöde när det visas från hårddisknivån* |120/250 MB/sek | |
| Maximalt klientläsnings-/skrivflöde när det visas från molnnivån* |11/41 MB/sek |Läsdataflöde beror på klienter som genererar och underhåller tillräckligt I/O-ködjup. |

&#42;Maximalt dataflöde per I/O-typ mättes med 100 procent läsning och 100 procent skrivscenarier. Det faktiska dataflödet kan vara lägre och beror på I/O-mix och nätverksförhållanden.

