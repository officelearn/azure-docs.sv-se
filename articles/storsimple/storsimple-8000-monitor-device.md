---
title: Övervaka enheten StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du använder tjänsten StorSimple Enhetshanteraren för att övervaka användningen, i/o-prestanda och kapacitetsutnyttjande.
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
ms.openlocfilehash: 679c1fc8775ad4481bc99c9aea79fe16e9bcac8f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
ms.locfileid: "23933146"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att övervaka din StorSimple-enhet

## <a name="overview"></a>Översikt
Du kan använda Enhetshanteraren för StorSimple-tjänsten för att övervaka specifika enheter i din StorSimple-lösning. Du kan skapa anpassade diagram baserat på i/o-prestanda, kapacitetsutnyttjande, dataflödet i nätverket och prestandamått för enheten och fästa dem på instrumentpanelen. Mer information finns på [anpassa instrumentpanelen portal](../azure-portal/azure-portal-dashboards.md).

Välj tjänsten StorSimple Device Manager om du vill visa övervakningsinformation för en specifik enhet i Azure-portalen. Välj din enhet i listan över enheter och gå sedan till **övervakaren**. Du kan se den **kapacitet**, **användning**, och **prestanda** diagram för den valda enheten.

## <a name="capacity"></a>Kapacitet
**Kapacitet** spårar allokerade utrymmet och kvar på enheten. Återstående kapacitet visas som lokalt Fäst eller nivåer.

Etablerad och återstående kapacitet är ytterligare fördelade på nivåindelade och lokalt fästa volymer. För varje volym visas etablerad kapacitet och återstående kapacitet på enheten.

![I/o-kapacitet](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Användning
**Användning** spårar mätvärden som är relaterade till mängden lagringsutrymme för data som används av volymer, volymbehållare eller enhet. Du kan skapa rapporter utifrån kapacitetsutnyttjande din primära lagring, molnlagringen eller enhetens utrymme. Kapacitetsutnyttjande kan mätas på en viss volym, en specifik volymbehållare eller alla volymbehållare.
Som standard rapporteras användning för senaste 24 timmarna. Du kan redigera diagram om du vill ändra den varaktighet som rapporteras användningen genom att välja:
* Senaste 24 timmarna
* De senaste 7 dagarna
* De senaste 30 dagarna
* De senaste 90 dagarna
* Senaste året

Två viktiga metrices, tillväxt och intervallet rapporteras för diagram för användning. Refererar till det högsta värdet och lägsta värdena för användning som rapporterat under den valda varaktigheten (för instansen, senaste 7 dagarna).

Tillväxt refererar till ökad användning från den första dagen efter den sista dagen under den valda varaktigheten. 

Öknings- och kan också vara representerad av nedanstående:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Det primära molnet och lokal lagring används kan beskrivas på följande sätt:

### <a name="primary-storage-usage"></a>Lagringskvoten på primär
Följande diagram visar mängden data som skrivs till StorSimple-volymer innan data är deduplicerad och komprimeras. Du kan visa den primära lagringsutrymme som används av alla volymer i en volymbehållare eller för en enskild volym. Den primära lagringsutrymme som används är ytterligare fördelade på primära nivåindelade lagringsutrymme som används och primära lokalt Fäst lagringsutrymme som används.

Följande diagram visar den primära lagringsutrymme som används för en StorSimple-enheten innan och efter att en ögonblicksbild i molnet har tagits. Eftersom det är bara volymdata bör en ögonblicksbild i molnet inte ändra den primära lagringsplatsen. Som du ser visas i diagrammet någon skillnad i den primära nivåindelade eller lokalt Fäst lagringsutrymme som används på grund av en ögonblicksbild i molnet. Ögonblicksbild i molnet startade ungefär 11:50 pm på enheten.

![Primär kapacitetsutnyttjande efter ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Om du nu köra i/o på värden som är ansluten till din StorSimple-enhet, ska du se en ökning i primära skiktad lagring eller primär lokalt Fäst lagringsutrymme som används beroende på vilka volymer (nivåer eller lokalt Fäst) skriver du data till. Här är primär lagring användning diagram för en StorSimple-enhet. På den här enheten skriver StorSimple värden igång betjänar på cirka 2:30 pm på en nivåindelad volym på enheten. Du kan se belastning i skriva byte/s för i/o som körs på värden.

![Prestanda när den körs på i/o nivåindelade volymer](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Om du tittar på den primära nivåindelade lagringsutrymme som används som har gått medan primärt lokalt Fäst användning förblir oförändrad eftersom det inte finns några skrivningar till lokalt fästa volymer på enheten.

![Primär kapacitetsutnyttjande när i/o körs på nivåindelade volymer](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Om du kör uppdatering 3 eller senare, du kan dela ned den primära lagringskapacitetsutnyttjandet genom en enskild volym, alla volymer, alla nivåindelade volymer och alla lokalt fästa volymer som visas nedan. Bryta ned efter alla lokalt fästa volymer kan du snabbt fastställa hur mycket av den lokala nivån används.

![Primär kapacitetsutnyttjande för alla nivåindelade volymer](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Primär kapacitetsutnyttjande för alla lokalt fästa volymer](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Du kan ytterligare klickar du på varje volym i listan och se motsvarande användning.

![Primär kapacitetsutnyttjande för alla lokalt fästa volymer](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Lagringskvoten på molnet
Följande diagram visar mängden molnlagring som används. Dessa data är deduplicerad och komprimeras. Den här mängden innehåller molnögonblicksbilder som kan innehålla data som inte visas i alla primära volymer och sparas i äldre eller obligatoriskt kvarhållning syfte. Du kan jämföra primärt och molnet förbrukning av lagringssiffrorna för att få en uppfattning om minskning datahastighet, även om talet är inte exakt.

Följande diagram visar molnanvändning för lagring av StorSimple-enhet när en ögonblicksbild i molnet togs.

* Ögonblicksbild i molnet som startades vid ungefär 11:50:00 på enheten och du kan se att innan molnet ögonblicksbild, det fanns inga molnlagring som används. 
* En gång molnet ögonblicksbilden slutförts lagringsanvändningen moln tog upp 0,89 GB. 
* När ögonblicksbilden i molnet pågick finns även en motsvarande belastning i IO från enheten till molnet.

    ![Molnet lagringsanvändningen innan ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Lagring av molnanvändning efter ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![I/o från enhet till moln under en ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Användning av lokal lagring
Följande diagram visar den totala belastningen för enheten, vilket kommer att lagringskvoten på flera primära eftersom den innehåller linjär SSD-nivån. Det här skiktet innehåller en datamängd som även finns på enheten har andra nivåer. Kapaciteten i den linjära SSD-nivån kopplas så att när nya data kommer in, gamla data flyttas till hårddisknivån (då den är deduplicerad och komprimerade) och därefter till molnet.

Över tiden, primär lagring ökar används och lokal lagring som används mest sannolikt tillsammans tills data börjar nivåer till molnet. Vid den punkten lokal lagring används börjar förmodligen plana ut, men ökar den primära lagringsutrymme som används när mer data skrivs.

Följande diagram visar den primära lagringsutrymme som används för en StorSimple-enhet när en ögonblicksbild i molnet togs. Ögonblicksbild i molnet som startade kl 11:50 och lokal lagring igång minskar vid den tiden. Lokal lagring används minskade från 1.445 GB till 1,09 GB. Detta anger att troligen okomprimerad data i linjär SSD-nivån har dedupliceras, komprimeras och flyttas till hårddisknivån. Observera att om enheten redan har en stor mängd data i både SSD och HDD-nivåer, du inte kan se denna minskning. Enheten har en liten del av informationen i det här exemplet.

![Lokal lagring användning efter ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Prestanda
**Prestanda** spårar mätvärden som är relaterade till antalet och skrivåtgärder mellan antingen-gränssnitt för iSCSI-initieraren på värdservern och enheten eller enheten och molnet. Prestanda kan beräknas för en viss volym, en specifik volymbehållare eller alla volymbehållare. Prestanda innehåller CPU-användning och dataflödet i nätverket för olika nätverkskort på enheten.

### <a name="io-performance-for-initiator-to-device"></a>I/o-prestanda för initieraren till enhet
Diagrammet nedan visar i/o för initieraren till din enhet för volymerna som för en enhet för produktion. Mätvärdena ritas är läsa och skriva byte per sekund. Du kan också skapa diagram över Läs-, Skriv- och utestående I/O eller läsa och skriva svarstider.

![I/o-prestanda för initieraren till enhet](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>I/o-prestanda för enheten till molnet
På samma enhet ritas i/o-åtgärder för data från enheten till molnet för alla volymbehållare. Data är endast i den linjära nivån på den här enheten och inget har hamnat i molnet. Det finns ingen läs-och skrivåtgärder som sker från enhet till molnet. Det är därför toppar i diagrammet med ett intervall på 5 minuter som motsvarar den frekvens som kontrolleras av pulsslag mellan enheten och tjänsten.

För samma enhet togs en ögonblicksbild i molnet för volymdata Starta kl 11:50. Detta resulterade i data som flödar från enheten till molnet. Skrivning har sett till molnet i varaktigheten. I/o-diagrammet visar en topp i skriva byte/s som motsvarar den tid när ögonblicksbilden togs.

![I/o från enhet till moln under en ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Dataflödet i nätverket för enheten nätverksgränssnitt
**Dataflödet i nätverket** spårar mätvärden som är relaterade till mängden data som överförs från nätverksgränssnitt för iSCSI-initieraren på värdservern och enheten och mellan enheten och molnet. Du kan övervaka det här måttet för varje nätverksgränssnitt iSCSI på enheten.

Följande diagram visar genomströmningen i nätverket för Data 0, 1 1 GbE nätverket på din enhet som har båda moln-aktiverat (standard) och iSCSI-aktiverade. På den här enheten på 14 juni cirka 21: 00 data var nivåer i molnet (ingen molnögonblicksbilder togs vid den tiden som pekar på skiktning som en mekanism för att flytta data i molnet) vilket resulterade i i/o hanteras till molnet. Det finns en motsvarande belastning i nätverket genomströmning diagrammet samtidigt och de flesta av nätverkstrafiken är utgående till molnet.

![Dataflödet i nätverket för Data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Om vi tittar på Data 1 gränssnittet genomströmning diagrammet en annan 1 GbE nätverksgränssnittet som var bara iSCSI-aktiverade sedan uppstod praktiskt taget ingen nätverkstrafik i varaktigheten.

![Dataflödet i nätverket för Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>CPU-belastningen för enhet
**Processoranvändningen** spårar mätvärden som är relaterade till Processorn som används på enheten. Följande diagram visar statistik för CPU-användning för en enhet i produktion.

![CPU-belastningen för enhet](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [StorSimple Enhetshanteraren service enheten instrumentpanel](storsimple-device-dashboard.md).
* Lär dig hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

