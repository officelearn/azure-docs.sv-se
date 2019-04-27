---
title: Övervaka din enhet i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du använder StorSimple Device Manager-tjänsten för att övervaka användningen, i/o-prestanda och kapacitetsanvändning.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 602514df69977891167f341db0ab20913bcacc9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634634"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Använda StorSimple Device Manager-tjänsten för att övervaka din StorSimple-enhet

## <a name="overview"></a>Översikt
Du kan använda StorSimple Device Manager-tjänsten för att övervaka specifika enheter i din StorSimple-lösning. Du kan skapa anpassade diagram baserat på i/o-prestanda, kapacitetsanvändning, dataflöde i nätverket och prestandamått för enheten och fästa dem på instrumentpanelen. Mer information går du till [anpassa instrumentpanelen i portalen](../azure-portal/azure-portal-dashboards.md).

Om du vill visa övervakningsinformation för en specifik enhet i Azure portal, Välj StorSimple Device Manager-tjänsten. Från listan över enheter, Välj din enhet och gå sedan till **övervakaren**. Du kan sedan se den **kapacitet**, **användning**, och **prestanda** diagram för den valda enheten.

## <a name="capacity"></a>Kapacitet
**Kapacitet** spårar etablerade utrymme och det utrymme som finns kvar på enheten. Återstående kapacitet visas sedan som lokalt fixerade eller nivåer.

Etablerade och återstående kapacitet är ytterligare fördelat på nivåindelade och lokalt fixerade volymer. För varje volym visas etablerad kapacitet och återstående kapacitet på enheten.

![I/o-kapacitet](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Användning
**Användning** spårar prestandamått mängden lagringsutrymme för data som används av volymer, volymbehållare och enheten. Du kan skapa rapporter som baseras på kapacitetsanvändning av din primär lagring, molnlagringen eller din enhetslagring. Kapacitetsutnyttjande kan mätas på en specifik volym eller en specifik volymbehållare alla volymbehållare.
Som standard rapporteras användning senaste 24 timmarna. Du kan redigera diagrammet om du vill ändra den varaktighet som rapporteras användningen genom att välja från:
* Senaste 24 timmarna
* Senaste 7 dagarna
* Senaste 30 dagarna
* Senaste 90 dagarna
* Senaste året

Två viktiga metrices, tillväxt och intervallet rapporteras för diagram för användning. Intervallet avser det maximala värdet och lägsta värdena för användning som har rapporterat under den valda perioden (fo instans, senaste 7 dagarna).

Tillväxt refererar till ökade användningen från den första dagen till den sista dagen över den valda perioden. 

Ökning och intervallet kan också representeras av följande formler:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Det primära, molnet och lokal lagring som används kan beskrivas på följande sätt:

### <a name="primary-storage-usage"></a>Primär lagringsanvändning
De här diagrammen visar mängden data som skrivs till StorSimple-volymer innan data dedupliceras och komprimeras. Du kan visa den primära lagringen som används av alla volymer i en volymbehållare eller för en enskild volym. Primär lagring som används är ytterligare fördelat på primär nivåindelad lagring som används och primär lokalt fixerad lagring som används.

Följande diagram visar den primära lagringen som används för en StorSimple-enheten innan och efter att en ögonblicksbild i molnet har utfört. Eftersom det är bara volymdata bör en ögonblicksbild i molnet inte ändra den primära lagringen. Som du ser visas i diagrammet ingen skillnad i den primär nivåindelad eller lokalt fixerad lagring som används på grund av att ta en ögonblicksbild i molnet. Ögonblicksbilden av molndata startade cirka 23:50:00 på den enheten.

![Primär kapacitetsanvändning efter ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Om du nu köra i/o på värden är ansluten till din StorSimple-enhet, visas en ökning av primär nivåindelad lagring eller primär lokalt fixerad lagring som används beroende på vilka volymer (nivåer eller lokalt fixerade) skriver du data till. Här är primär lagring användning diagram för en StorSimple-enhet. På den här enheten skriver StorSimple värden igång betjänar på cirka 2:30 pm på en nivåindelad volym på enheten. Du kan se högsta i skrivna byte/s för i/o som körs på värden.

![Prestanda när i/o som körs på nivåindelade volymer](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Om du tittar på primär nivåindelad lagring som används som har gått medan primär lokalt fixerad användningen förblir oförändrade som det finns inga skrivningar som hanteras av lokalt fixerade volymer på enheten.

![Primär kapacitetsanvändning när i/o körs på nivåindelade volymer](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Om du kör uppdatering 3 eller högre, du kan bryta ned den primära lagringskapacitetsutnyttjandet med en enskild volym, alla volymer, alla nivåindelade volymer och alla lokalt fixerade volymer som visas nedan. Bryta ned efter alla lokalt fixerade volymer kan du snabbt fastställa hur mycket av den lokala nivån har förbrukats.

![Primär kapacitetsanvändning för nivåindelade volymer](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Primär kapacitetsanvändning för lokalt fixerade volymer](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Ytterligare kan du klicka på varje volym i listan och visa den motsvarande användningen.

![Primär kapacitetsanvändning för lokalt fixerade volymer](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Lagringsanvändning i molnet
De här diagrammen visar mängden molnlagring som används. Dessa data är deduplicerad och komprimeras. Den här mängden innehåller ögonblicksbilder av molnet som kan innehålla data som inte visas i alla primära volymer och sparas för kvarhållning av äldre eller krävs. Du kan jämföra primära och användning av lagringsutrymmet för att få en uppfattning om minskning datahastighet i molnet även om talet är inte exakt.

Följande diagram visar molnanvändning för lagring av en StorSimple-enhet när en ögonblicksbild i molnet togs.

* Ögonblicksbilden som startade ungefär 11:50:00 på den enheten och du kan se att innan ögonblicksbild av molndata det fanns inga molnlagring som används. 
* När ögonblicksbilden av molndata har slutförts, lagringsanvändningen molnet som upp 0,89 GB. 
* När ögonblicksbilden av molndata pågick, finns det också en motsvarande topp i i/o från enhet till molnet.

    ![Molnet lagringsanvändningen innan ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Lagringsanvändningen efter ögonblicksbild av molndata i molnet](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![I/o från enhet till molnet under en ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Användning av lokal lagring
Dessa diagram visar den totala belastningen för enheten, vilket kommer att mer än primära lagringsanvändning eftersom den innehåller linjär SSD-nivån. Den här nivån innehåller en datamängd som även finns på enheten är andra nivåer. Kapacitet på SSD-nivån för linjär kopplas så att när nya data kommer in, gamla data flyttas till hårddisknivån (då den är deduplicerad och komprimeras) och därefter till molnet.

Över tid, primär lagring ökar används och lokal lagring som används mest sannolikt tillsammans tills data börjar nivåindelas till molnet. I det här läget lokal lagring används börjar förmodligen plana ut, men den primära lagringen som används ökar i takt med mer detaljerade data lagras.

Följande diagram visar den primära lagringen som används för en StorSimple-enhet när en ögonblicksbild i molnet togs. Ögonblicksbilden som startade kl. 11:50 och den lokala lagringen igång minskar vid den tidpunkten. Lokal lagring som används har gått från 1.445 GB till 1,09 GB. Detta anger att troligen okomprimerade data i den linjära SSD-nivån har dedupliceras, komprimeras och flyttas till hårddisknivån. Observera att om enheten redan har en stor mängd data nivåerna både SSD och HDD, du inte kanske ser den här minskningen. Enheten har en liten mängd data i det här exemplet.

![Lokal lagringsanvändning efter ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Prestanda
**Prestanda** spårar prestandamått antalet Läs- och skrivåtgärder mellan antingen-gränssnitt för iSCSI-initieraren på värdservern och enheten eller enheten och molnet. Prestanda kan beräknas för en specifik volym eller en specifik volymbehållare alla volymbehållare. Prestanda innehåller också CPU-användning och dataflöde i nätverket för olika nätverksgränssnitten på din enhet.

### <a name="io-performance-for-initiator-to-device"></a>I/o-prestanda för initierare till enhet
Diagrammet nedan visar i/o för initierare för enheten för volymerna som för en enhet för produktion. De mått som ritas är läser och skriver byte per sekund. Du kan också skapa diagram över Läs-, Skriv- och utestående i/o eller läsa och skriva svarstider.

![I/o-prestanda för initierare till enhet](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>I/o-prestanda för enhet till molnet
På samma enhet ritas i/o-åtgärder för data från enheten till molnet för alla volymbehållare. Dina data finns endast i den linjära nivån på enheten, och ingenting har hamnat i molnet. Det finns inga skrivskyddade åtgärder som pågår från enhet till molnet. Toppar i diagrammet är därför ett intervall på 5 minuter som motsvarar den frekvens med vilken pulsslag kontrolleras mellan enheten och tjänsten.

För samma enhet, har en ögonblicksbild i molnet vidtagits för volymdata börjar kl. 11:50. Detta resulterade i data som flödar från enheten till molnet. Skrivningar har sett till molnet under den tiden. I/o-diagrammet visar en topp i skrivna byte/s för tiden när ögonblicksbilden togs.

![I/o från enhet till molnet under en ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Nätverksdataflöde för enheten nätverksgränssnitt
**Nätverkets genomflöde** spårar prestandamått för mängden data som överförs från nätverksgränssnitt för iSCSI-initieraren på värdservern och enheten och mellan enheten och molnet. Du kan övervaka det här måttet för var och en av de iSCSI-nätverksgränssnitten på din enhet.

Följande diagram visar nätverkets genomflöde för Data 0, 1-1 GbE-nätverket på din enhet som har både moln-aktiverat (standard) och iSCSI-aktiverat. På den här enheten den 14 juni kl cirka 9 har nivåindelade data i molnet (inga molnögonblicksbilder togs vid den tidpunkten som pekar mot lagringsnivåer som mekanism för att flytta data till molnet) vilket resulterade i i/o som hanteras i molnet. Det finns en motsvarande topp i nätverket dataflöde graph för samtidigt och de flesta av nätverkstrafiken är utgående till molnet.

![Nätverksdataflöde för Data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Om vi tittar på Data 1 gränssnittet dataflöde diagrammet en annan 1 GbE nätverksgränssnittet som tidigare endast iSCSI-aktiverat och det fanns praktiskt taget ingen nätverkstrafik i varaktigheten.

![Nätverksdataflöde för Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>CPU-belastningen för enhet
**CPU-belastning** spårar prestandamått Processorn som används på enheten. Följande diagram visar statistik för CPU-användning för en enhet i produktion.

![CPU-belastningen för enhet](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Device Manager-instrumentpanelen för enheten](storsimple-device-dashboard.md).
* Lär dig hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

