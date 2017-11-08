---
title: "StorSimple 8000-serien system gränser | Microsoft Docs"
description: "Beskriver system gränser och rekommenderade storlekar för StorSimple 8000-serien komponenter och anslutningar."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2adefbe89108bc5a807b7413e821f8fcd0f3f325
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Vad är StorSimple 8000-serien system gränser?
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [vad är StorSimple 8000-serien system gränser?](storsimple-8000-limits.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
StorSimple innehåller skalbara och flexibla lagring för ditt datacenter. Det finns dock vissa begränsningar som du bör tänka på när du planerar, distribuerar och fungerar din StorSimple-lösning. Följande tabell beskriver dessa begränsningar och innehåller några rekommendationer så att du kan få ut mesta möjliga av din StorSimple-lösning.

| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal lagringskontouppgifter |64 | |
| Maximalt antal volymbehållare |64 | |
| Maximalt antal volymer |255 | |
| Maximalt antal lokalt fästa volymer |32 | |
| Maximalt antal scheman per bandbreddsmall |168 |Ett schema för varje timme, varje dag i veckan (24 * 7). |
| Maximal storlek för en nivåindelad volym på fysiska enheter |64 TB för 8100 och 8600 |8100 och 8600 är fysiska enheter. |
| Maximal storlek för en nivåindelad volym på virtuella enheter i Azure |30 TB för 8010 <br></br> 64 TB för 8020 |8010 och 8020 är virtuella enheter i Azure som använder standardlagring respektive Premium-lagring. |
| Maximal storlek för en lokalt Fäst volym på fysiska enheter |8.5 TB för 8100 <br></br> 22,5 TB för 8600 |8100 och 8600 är fysiska enheter. |
| Maximalt antal iSCSI-anslutningar |512 | |
| Maximalt antal iSCSI klientanslutningar från initierarna |512 | |
| Högsta antal access control poster per enhet |64 | |
| Maximalt antal volymer per princip för säkerhetskopiering |20 | |
| Maximalt antal säkerhetskopior behålls per schema (i en princip för säkerhetskopiering) |64 | |
| Maximalt antal scheman per princip för säkerhetskopiering |10 | |
| Maximalt antal ögonblicksbilder av typer som kan behållas per volym |256 |Antalet inkluderar lokala ögonblicksbilder och molnbaserade ögonblicksbilder. |
| Maximalt antal ögonblicksbilder som kan finnas i valfri enhet |10 000 | |
| Maximalt antal volymer som kan bearbetas parallellt för säkerhetskopiering, återställning eller klona |16 |<ul><li>Om det finns fler än 16 volymer, tur de och ordning när bearbetningen fack blir tillgängliga.</li><li>Nya säkerhetskopior av en klonade eller en återställd nivåindelad volym kan inte ske förrän åtgärden har slutförts. Men för en lokal volym är säkerhetskopior tillåtna när volymen är online.</li></ul> |
| Återställa och klona Återställ tid för nivåindelade volymer |< 2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter för återställning eller klona igen, oavsett volymens storlek.</li><li>Den volymen först gå långsammare än normalt som de flesta data och metadata finns fortfarande i molnet. Prestanda kan öka som data flödar från molnet till StorSimple-enhet.</li><li>Total tid för att hämta metadata är beroende av allokerade volymens storlek. Metadata hämtas automatiskt till enheten i bakgrunden i frekvens på 5 minuter per TB allokerade volymdata. Detta värde kan påverkas av Internet-bandbredd till molnet.</li><li>Restore- eller kopieringen är klar när alla metadata finns på enheten.</li><li>Säkerhetskopieringsåtgärder kan inte utföras förrän återställningen eller kopieringen är helt klar. |
| Återställning återställa tid för lokalt fästa volymer |< 2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter efter återställningen, oavsett volymens storlek.</li><li>Den volymen först gå långsammare än normalt som de flesta data och metadata finns fortfarande i molnet. Prestanda kan öka som data flödar från molnet till StorSimple-enhet.</li><li>Total tid för att hämta metadata är beroende av allokerade volymens storlek. Metadata hämtas automatiskt till enheten i bakgrunden i frekvens på 5 minuter per TB allokerade volymdata. Detta värde kan påverkas av Internet-bandbredd till molnet.</li><li>Till skillnad från nivåindelade volymer för lokalt fästa volymer hämtas även volymens data lokalt på enheten. Återställningen är klar när alla volymdata har trätt till enheten.</li><li>Återställningsåtgärderna kan bli långa. Total tid för att slutföra återställningen beror på storleken på den etablerade volymen för lokala och Internet-bandbredd och befintliga data på enheten. Säkerhetskopieringsåtgärder på lokalt Fäst volym tillåts medan återställningen pågår. |
| Behandlingstakt för molnögonblicksbilder |15 minuter/TB |<ul><li>Minsta tid att skapa molnet snapshot klar för överföring per TB allokerad volymdata i säkerhetskopian. </li><li> Totalt antal molnet ögonblicksbild tid beräknas genom att lägga till den här gången överföringstid ögonblicksbild som påverkas av Internet-bandbredd till molnet. |
| Maximalt antal klienter läsning och skrivning genomströmning (när hanteras från SSD-nivån) * |920/720 MB/s med ett enda 10 GbE-nätverksgränssnitt |Upp till 2 x med MPIO och två nätverksgränssnitt. |
| Maximalt antal klienter läsning och skrivning genomströmning (när hanteras från hårddisknivån) * |120/250 MB/s | |
| Maximalt antal klienter läsning och skrivning genomströmning (när hanteras från molnnivån) * för uppdatering 3 och senare ** |40/60 MB/s för nivåindelade volymer<br><br>60/80 MB/s för nivåindelade volymer med arkivering alternativet under skapande av volym |Läs genomströmning beroende klienter skapa och upprätthålla tillräcklig i/o-ködjup. <br><br>Hastigheten uppnås beror på hastigheten på det underliggande storage-konto som används. |

&#42; Maximalt dataflöde per i/o-typ har mätt med 100 procent Läs- och 100 procent skrivåtgärder scenarier. Faktiska genomflöde kan vara lägre och beror på i/o blanda och nätverk villkor.

&#42; &#42; Prestanda siffror före uppdatering 3 kan vara lägre.

## <a name="next-steps"></a>Nästa steg
Granska de [StorSimple systemkrav](storsimple-system-requirements.md). 

