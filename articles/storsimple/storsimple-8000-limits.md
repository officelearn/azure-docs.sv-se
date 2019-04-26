---
title: StorSimple 8000-serien system gränser | Microsoft Docs
description: Beskriver system gränser och rekommenderade storlekar för StorSimple 8000-serien komponenter och anslutningar.
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
ms.openlocfilehash: a0053f950b36351b06d08630cbf9977f53f2ed47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319572"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Vad är StorSimple 8000-serien system begränsningar?

## <a name="overview"></a>Översikt

StorSimple tillhandahåller skalbara och flexibla lagring för ditt datacenter. Det finns dock vissa begränsningar som du bör tänka på när du planerar, distribuerar och driva din StorSimple-lösning. I följande tabell beskrivs dessa gränser och innehåller ett antal rekommendationer så att du kan få ut det mesta av din StorSimple-lösning.

| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal autentiseringsuppgifter för lagringskonto |64 | |
| Maximalt antal volymbehållare |64 | |
| Maxantalet volymer |255 | |
| Maximalt antal lokalt fixerade volymer |32 | |
| Maximalt antal scheman per bandbreddsmall |168 |Ett schema för varje timme, varje dag i veckan (7 * 24). |
| Maximal storlek på en nivåindelad volym på fysiska enheter |64 TB för 8100 och 8600 |8100 och 8600 är fysiska enheter. |
| Maximal storlek på en nivåindelad volym på virtuella enheter i Azure |30 TB för 8010 <br></br> 64 TB för 8020 |8010 och 8020 är virtuella enheter i Azure som använder Standard Storage respektive Premium Storage. |
| Maximal storlek på en lokalt Fäst volym på fysiska enheter |8,5 TB för 8100 <br></br> 22,5 TB för 8600 |8100 och 8600 är fysiska enheter. |
| Maximalt antal iSCSI-anslutningar |512 | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maxantalet åtkomstkontrollposter per enhet |64 | |
| Maxantalet volymer per princip för säkerhetskopiering |20 | |
| Hur många säkerhetskopior som finns kvar per schema (i en princip för säkerhetskopiering) |64 | |
| Maximalt antal scheman per princip för säkerhetskopiering |10 | |
| Högsta antalet ögonblicksbilder av valfri typ som kan bevaras per volym |256 |Det här värdet innefattar lokala ögonblicksbilder och molnbaserade ögonblicksbilder. |
| Maximalt antal ögonblicksbilder som kan finnas i valfri enhet |10 000 | |
| Högsta antalet volymer som kan bearbetas parallellt för säkerhetskopiering, återställning eller klona |16 |<ul><li>Om det finns fler än 16 volymer, de utförs i ordningsföljd när bearbetning fack blir tillgängliga.</li><li>Nya säkerhetskopior av en klonade eller en återställd nivåindelad volym kan inte ske förrän åtgärden är klar. Men för en lokal volym är säkerhetskopior tillåtna när volymen är online.</li></ul> |
| Återställa och klona Återställ tiden för nivåindelade volymer |< 2 minuter |<ul><li>Volymen är tillgänglig inom två minuter för återställning eller klona åtgärden oavsett volymens storlek.</li><li>Den volymen först gå långsammare än normalt eftersom de flesta data och metadata som fortfarande finns i molnet. Prestanda kan öka som dataflöden från molnet till StorSimple-enheten.</li><li>Total tid för att hämta metadata beror på allokerade volymens storlek. Metadata hämtas automatiskt till enheten i bakgrunden med en hastighet av 5 minuter per TB allokerade volymdata. Priset påverkas av internetbandbredd till molnet.</li><li>Restore- eller kopieringen är klar när alla metadata som finns på enheten.</li><li>Säkerhetskopieringsåtgärder kan inte utföras förrän återställningen eller Kloningen är helt klar. |
| Återställ återställningstid för lokalt fästa volymer |< 2 minuter |<ul><li>Volymen är tillgänglig inom två minuter för återställningsåtgärden, oavsett volymens storlek.</li><li>Den volymen först gå långsammare än normalt eftersom de flesta data och metadata som fortfarande finns i molnet. Prestanda kan öka som dataflöden från molnet till StorSimple-enheten.</li><li>Total tid för att hämta metadata beror på allokerade volymens storlek. Metadata hämtas automatiskt till enheten i bakgrunden med en hastighet av 5 minuter per TB allokerade volymdata. Priset påverkas av internetbandbredd till molnet.</li><li>Till skillnad från nivåindelade volymer för lokalt fästa volymer, hämtas även volymdata lokalt på enheten. Återställningen är klar när alla volymdata har trätt till enheten.</li><li>Återställningsåtgärden kan vara långt. Total tid för att slutföra återställningen beror på storleken på den etablerade lokal volymen, internetbandbredden och befintliga data på enheten. Säkerhetskopieringsåtgärder på lokalt fixerad volym tillåts medan återställningen pågår. |
| Behandlingstakt för ögonblicksbilder av molnet |15 minuter/TB |<ul><li>Den minsta tiden att göra molnet ögonblicksbilder är klara för uppladdning per TB allokerade volymdata i säkerhetskopian. </li><li> Totalt antal molnet ögonblicksbild tid beräknas genom att lägga till den här gången överföringstid ögonblicksbild som påverkas av internetbandbredd till molnet. |
| Maximalt antal klienter skrivning/dataflöde (när hanteras från SSD-nivån) * |920/720 MB/s med ett enda 10 GbE-nätverksgränssnitt |Upp till 2 x med MPIO och två nätverksgränssnitt. |
| Maximalt antal klienter skrivning/dataflöde (när hanteras från hårddisknivån) * |120/250 MB/s | |
| Maximalt antal klienter skrivning/dataflöde (när hanteras från molnnivån) * för uppdatering 3 och senare ** |40/60 MB/s för den nivåindelade volymer<br><br>60/80 MB/s för den nivåindelade volymer med arkivering alternativet när volymen skapas |Läs dataflödet beror på klienter genererar och bevara tillräcklig i/o-ködjup. <br><br>Hastigheten uppnås beror på hastigheten på det underliggande lagringskontot som används. |

&#42;Maximalt dataflöde per i/o-typ har mätt med 100 procent Läs- och 100 procent skrivåtgärder scenarier. Det verkliga dataflödet kan vara lägre och beror på i/o blanda och nätverks-villkor.

&#42;&#42;Prestandavärden före uppdatering 3 kan vara lägre.

## <a name="next-steps"></a>Nästa steg
Granska den [systemkrav för StorSimple](storsimple-8000-system-requirements.md).

