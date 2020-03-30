---
title: Systemgränser för StorSimple 8000-serien | Microsoft-dokument
description: Beskriver systemgränser och rekommenderade storlekar för komponenter och anslutningar i StorSimple 8000-serien.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68963374"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Vad är Systemgränser för StorSimple 8000-serien?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

StorSimple tillhandahåller skalbar och flexibel lagring för ditt datacenter. Det finns dock vissa gränser som du bör tänka på när du planerar, distribuerar och använder din StorSimple-lösning. I följande tabell beskrivs dessa gränser och några rekommendationer så att du kan få ut mesta möjliga av din StorSimple-lösning.

| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal autentiseringsuppgifter för lagringskonto |64 | |
| Maximalt antal volymbehållare |64 | |
| Maximalt antal volymer |255 | |
| Maximalt antal lokalt fästa volymer |32 | |
| Maximalt antal scheman per bandbreddsmall |168 |Ett schema för varje timme, alla dagar i veckan (24 * 7). |
| Maximal storlek på en nivåindelad volym på fysiska enheter |64 TB för 8100 och 8600 |8100 och 8600 är fysiska enheter. |
| Maximal storlek för en nivåindelad volym på virtuella enheter i Azure |30 TB för 8010 <br></br> 64 TB för 8020 |8010 och 8020 är virtuella enheter i Azure som använder Standard Storage respektive Premium Storage. |
| Maximal storlek på en lokalt fäst volym på fysiska enheter |8,5 TB för 8100 <br></br> 22,5 TB för 8600 |8100 och 8600 är fysiska enheter. |
| Maximalt antal iSCSI-anslutningar |512 | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maximalt antal åtkomstkontrollposter per enhet |64 | |
| Maximalt antal volymer per säkerhetskopieringsprincip |20 | |
| Maximalt antal säkerhetskopieringar som behålls per schema (i en säkerhetskopieringsprincip) |64 | |
| Maximalt antal scheman per säkerhetskopieringsprincip |10 | |
| Maximalt antal ögonblicksbilder av alla typer som kan behållas per volym |256 |Det här numret innehåller lokala ögonblicksbilder och ögonblicksbilder av molnet. |
| Maximalt antal ögonblicksbilder som kan finnas i en enhet |10 000 | |
| Maximalt antal volymer som kan bearbetas parallellt för säkerhetskopiering, återställning eller klon |16 |<ul><li>Om det finns fler än 16 volymer bearbetas de sekventiellt när bearbetningsplatser blir tillgängliga.</li><li>Nya säkerhetskopior av en klonad eller en återställd nivåindelade volym kan inte inträffa förrän åtgärden är klar. För en lokal volym tillåts dock säkerhetskopieringar när volymen är online.</li></ul> |
| Återställa och klona återställningstid för nivåindelad volym |< 2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter efter återställning eller kloning, oavsett volymstorlek.</li><li>Volymprestandan kan inledningsvis vara långsammare än normalt eftersom de flesta data och metadata fortfarande finns i molnet. Prestanda kan öka när data flödar från molnet till StorSimple-enheten.</li><li>Den totala tiden för hämtning av metadata beror på den allokerade volymstorleken. Metadata förs automatiskt in i enheten i bakgrunden med en hastighet av 5 minuter per TB allokerade volymdata. Den här hastigheten kan påverkas av Internet-bandbredd till molnet.</li><li>Återställnings- eller klonningsåtgärden är klar när alla metadata finns på enheten.</li><li>Säkerhetskopieringsåtgärder kan inte utföras förrän återställnings- eller klonningsåtgärden är helt klar. |
| Återställa återställningstid för lokalt fästa volymer |< 2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter efter återställningen, oavsett volymstorlek.</li><li>Volymprestandan kan inledningsvis vara långsammare än normalt eftersom de flesta data och metadata fortfarande finns i molnet. Prestanda kan öka när data flödar från molnet till StorSimple-enheten.</li><li>Den totala tiden för hämtning av metadata beror på den allokerade volymstorleken. Metadata förs automatiskt in i enheten i bakgrunden med en hastighet av 5 minuter per TB allokerade volymdata. Den här hastigheten kan påverkas av Internet-bandbredd till molnet.</li><li>Till skillnad från nivåindelade volymer hämtas volymdata även lokalt på enheten för lokalt fästa volymer. Återställningen är klar när alla volymdata har förts till enheten.</li><li>Återställningsåtgärderna kan vara långa. Den totala tiden för att slutföra återställningen beror på storleken på den etablerade lokala volymen, internetbandbredden och befintliga data på enheten. Säkerhetskopieringsåtgärder på den lokalt fästa volymen tillåts medan återställningen pågår. |
| Bearbetningshastighet för ögonblicksbilder av molnet |15 minuter/TB |<ul><li>Minsta tid för att göra ögonblicksbilden av molnet klar för överföring, per TB allokerade volymdata i säkerhetskopian. </li><li> Den totala ögonblicksbildtiden för molnet beräknas genom att den här tiden läggs till i uppladdningstiden för ögonblicksbilder, som påverkas av Internet-bandbredden till molnet. |
| Maximalt klientläsnings-/skrivflöde (när det visas från SSD-nivån)* |920/720 MB/s med ett enda 10 GbE-nätverksgränssnitt |Upp till 2x med MPIO och två nätverksgränssnitt. |
| Maximalt dataflöde för klientläsning/skriv (när det visas från hårddisknivån)* |120/250 MB/s | |
| Maximalt klientläsnings-/skrivflöde (när det visas från molnnivån)* för uppdatering 3 och senare** |40/60 MB/s för nivåindelade volymer<br><br>60/80 MB/s för nivåindelade volymer med arkiveringsalternativ markerat när volymen skapas |Läsdataflöde beror på klienter som genererar och underhåller tillräckligt I/O-ködjup. <br><br>Uppnådd hastighet beror på hastigheten på det underliggande lagringskonto som används. |

&#42; Maximalt dataflöde per I/O-typ mättes med 100 procent läsning och 100 procent skrivscenarier. Det faktiska dataflödet kan vara lägre och beror på I/O-mix och nätverksförhållanden.

&#42;&#42; prestandanummer före uppdatering 3 kan vara lägre.

## <a name="next-steps"></a>Nästa steg
Granska [StorSimple-systemkraven](storsimple-8000-system-requirements.md).

