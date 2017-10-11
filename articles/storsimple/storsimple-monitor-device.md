---
title: "Övervaka din StorSimple-enhet | Microsoft Docs"
description: "Beskriver hur du använder StorSimple Manager-tjänsten för att övervaka i/o-prestanda, kapacitetsutnyttjande, dataflödet i nätverket och Enhetsprestanda."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: bd4f7704-4f6f-47d0-927a-b1c91eabc453
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/16/2016
ms.author: alkohli
ms.openlocfilehash: d45bb37c8417785db0ea38be4375a998b6d9f109
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Använda StorSimple Manager-tjänsten för att övervaka din StorSimple-enhet
## <a name="overview"></a>Översikt
Du kan använda StorSimple Manager-tjänsten för att övervaka specifika enheter i din StorSimple-lösning. Du kan skapa anpassade diagram baserat på i/o-prestanda, kapacitetsutnyttjande, dataflödet i nätverket och prestandamått för enheten. 

Välj StorSimple Manager-tjänsten om du vill visa övervakningsinformation för en specifik enhet i den klassiska Azure-portalen. Klicka på den **övervakaren** och välj sedan från listan över enheter. Den **övervakaren** innehåller följande information.

## <a name="io-performance"></a>I/o-prestanda
**I/o-prestanda** spårar mätvärden som är relaterade till antalet och skrivåtgärder mellan antingen-gränssnitt för iSCSI-initieraren på värdservern och enheten eller enheten och molnet. Prestanda kan beräknas för en viss volym, en specifik volymbehållare eller alla volymbehållare.

Diagrammet nedan visar i/o för initieraren till din enhet för volymerna som för en enhet för produktion. Mätvärdena ritas läses och skriva byte per sekund, läsa och skriva i/o-åtgärder per sekund, och läsa och skriva svarstider.

![I/o-prestanda från initieraren till enhet](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

På samma enhet ritas i/o-åtgärder för data från enheten till molnet för alla volymbehållare. Data är endast i den linjära nivån på den här enheten och inget har hamnat i molnet. Det finns ingen läs-och skrivåtgärder som sker från enhet till molnet. Det är därför toppar i diagrammet med ett intervall på 5 minuter som motsvarar den frekvens som kontrolleras av pulsslag mellan enheten och tjänsten. 

![I/o-prestanda från enhet till moln](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)

För samma enhet togs en ögonblicksbild i molnet för volymdata som börjar på 2:00 pm. Detta resulterade i data som flödar från enheten till molnet. Läsning / skrivning har sett till molnet i varaktigheten. I/o-diagrammet visar en topp i de olika mätvärden som motsvarar den tid när ögonblicksbilden togs. 

![I/o-prestanda för enheten till molnet efter ögonblicksbild i molnet](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)

## <a name="capacity-utilization"></a>Kapacitetsutnyttjande
**Kapacitetsutnyttjande** spårar mätvärden som är relaterade till mängden lagringsutrymme för data som används av volymer, volymbehållare eller enhet. Du kan skapa rapporter utifrån kapacitetsutnyttjande din primära lagring, molnlagringen eller enhetens utrymme. Kapacitetsutnyttjande kan mätas på en viss volym, en specifik volymbehållare eller alla volymbehållare.

Det primära molnet, och enheten lagringskapacitet kan beskrivas på följande sätt:

### <a name="primary-storage-capacity-utilization"></a>Primär lagringskapacitetsutnyttjandet
Följande diagram visar mängden data som skrivs till StorSimple-volymer innan data är deduplicerad och komprimeras. Du kan visa den primära lagringsanvändningen av alla volymer eller för en enskild volym.

När du visar primära lagringsplatsen volym kapacitet användning diagram för alla volymer jämfört med var och en av de enskilda volymerna och summera primära data i båda dessa fall kan det finnas ett matchningsfel mellan två tal. Totalt antal primära data på alla volymer kan inte lägga till totalt antalet av primära data för de enskilda volymerna. Detta kan bero på något av följande:

* **Ögonblicksbild av data som ingår för alla volymer**: det här beteendet visas bara om du använder version tidigare än uppdatering 3. Den primära data som visas för alla volymer är summan av primära data för varje volym och ögonblicksbilddata. Primär data som visas för en viss volym motsvarar endast mängden data som allokerats på volymen (och innehåller inte motsvarande volume snapshot data).
  
    Här förklaras också med följande formel:
  
    *Primära data (alla volymer) = summan av (primära data (volym i) + storleken på data från ögonblicksbilder (volume i))*
  
    *Om det primära data (volym i) = storlek av primära data som allokerats till volymen i*
  
    Om ögonblicksbilder tas bort via tjänsten, görs borttagningen asynkront i bakgrunden. Det kan ta en stund innan data volymstorleken uppdateras efter ögonblicksbild tas bort. 
  
    Om du kör uppdatering 3 eller senare, ingår inte ögonblicksbilddata i volymens data. Och den primära användningen beräknas enligt följande:
  
    * Primära data (alla volymer) = summan av (primära data (volym i)
  
    *Om det primära data (volym i) = storlek av primära data som allokerats till volymen i*
* **Volymerna som ingår i alla volymer med övervakning inaktiveras**: Om du har volymer på enheten som övervakningen inaktiverades övervakningsdata för volymerna enskilda blir inte tillgängliga i scheman. Data för alla volymer i diagrammet innehåller emellertid volymerna som övervakning har inaktiverats. 
* **Ta bort volymer med levande tillhörande säkerhetskopior inkluderade för alla volymer**: om volymer som innehåller ögonblicksbilddata för tas bort men associerade ögonblicksbilder fortfarande finns och sedan kan du se ett matchningsfel.
* **Ta bort volymerna för alla volymer**: I vissa fall gamla volymer finns även om dessa har tagits bort. Effekten av borttagning visas inte och enheten kan visa lägre tillgänglig kapacitet. Du måste kontakta Microsoft-supporten om du vill ta bort dessa volymer.

Följande diagram visar den primära lagringskapacitetsutnyttjandet av StorSimple-enhet före och efter ett moln ögonblicksbilden togs. Eftersom det är bara volymdata bör en ögonblicksbild i molnet inte ändra den primära lagringsplatsen. Som du ser visas i diagrammet någon skillnad i den primära kapacitetsutnyttjande på grund av en ögonblicksbild i molnet. Ögonblicksbild i molnet startade cirka 2:00 pm på enheten.

![Primär kapacitetsutnyttjande innan ögonblicksbild i molnet](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Primär kapacitetsutnyttjande efter ögonblicksbild i molnet](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Om du kör uppdatering 2 eller högre, du kan dela upp primära lagringskapacitetsutnyttjandet av en enskild volym, alla volymer, alla nivåindelade volymer och alla lokalt fästa volymer som visas nedan. Bryta ned efter alla lokalt fästa volymer kan du snabbt fastställa hur mycket av den lokala nivån används.

![Primär kapacitetsutnyttjande för alla lokalt fästa volymer](./media/storsimple-monitor-device/localvolumes.png)

### <a name="cloud-storage-capacity-utilization"></a>Lagringskapacitet för molnet
Följande diagram visar mängden molnlagring som används. Dessa data är deduplicerad och komprimeras. Den här mängden innehåller molnögonblicksbilder som kan innehålla data som inte visas i alla primära volymer och sparas i äldre eller obligatoriskt kvarhållning syfte. Du kan jämföra primärt och molnet förbrukning av lagringssiffrorna för att få en uppfattning om minskning datahastighet, även om talet är inte exakt. Följande diagram visar den molnet lagringskapacitetsutnyttjandet av StorSimple-enhet innan och efter att en ögonblicksbild i molnet har tagits. Ögonblicksbild i molnet som startades vid cirka 2:00 pm på enheten och du kan se kapacitetsutnyttjande molnet tog samtidigt, öka från 5.73 MB till 4.04 GB.

![Molnet kapacitetsutnyttjande innan ögonblicksbild i molnet](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Kapacitet för molnanvändning efter ögonblicksbild i molnet](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)

### <a name="device-storage-capacity-utilization"></a>Lagringskapacitet för enhet
Följande diagram visar den totala belastningen för enheten, vilket kommer att mer än primära lagringsanvändningen eftersom den innehåller linjär SSD-nivån. Det här skiktet innehåller en datamängd som även finns på enheten har andra nivåer. Kapaciteten i den linjära SSD-nivån kopplas så att när nya data kommer in, gamla data flyttas till hårddisknivån (då den är deduplicerad och komprimerade) och därefter till molnet.

Över tiden, primära kapacitetsutnyttjande och enheten kapacitetsutnyttjande kommer troligen att öka tillsammans tills data börjar nivåer till molnet. Vid den punkten kapacitetsutnyttjande enheten börjar förmodligen plana ut, men den primära kapacitetsutnyttjande ökar när mer data skrivs.

Följande diagram visar den primära lagringskapacitetsutnyttjandet av StorSimple-enhet före och efter ett moln ögonblicksbilden togs. Ögonblicksbild i molnet som startade kl. 2:00 pm och kapacitetsutnyttjande enheten startas om minskar vid den tiden. Lagringskapacitet för enheten har gått från 11.58 GB 7.48 GB. Detta anger att troligen okomprimerad data i linjär SSD-nivån har dedupliceras, komprimeras och flyttas till hårddisknivån. Observera att om enheten redan har en stor mängd data i både SSD och HDD-nivåer, du inte kan se denna minskning. Enheten har en liten del av informationen i det här exemplet.

![Enheten kapacitetsutnyttjande innan ögonblicksbild i molnet](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Enheten kapacitetsutnyttjande efter ögonblicksbild i molnet](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)

## <a name="network-throughput"></a>Dataflödet i nätverket
**Dataflödet i nätverket** spårar mätvärden som är relaterade till mängden data som överförs från nätverksgränssnitt för iSCSI-initieraren på värdservern och enheten och mellan enheten och molnet. Du kan övervaka det här måttet för varje nätverksgränssnitt iSCSI på enheten.

Följande diagram visar genomströmningen i nätverket för Data 0 och 4 för Data, både 1 GbE-nätverkskort på enheten. I den här instansen aktiverades Data 0 cloud-medan Data 4 har iSCSI-aktiverade. Du kan se både den inkommande och utgående trafik för din StorSimple-enhet. Platt linje i diagrammet från 3:24 pm på grund av att vi samla in data endast var femte minut och bör ignoreras. 

![Dataflödet i nätverket för Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Dataflödet i nätverket för Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)

## <a name="device-performance"></a>Enhetsprestanda
**Enhetsprestanda** spårar mått som rör prestandan för din enhet. Följande diagram visar statistik för CPU-användning för en enhet i produktion.

![CPU-belastningen för enhet](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [enheten instrumentpanel för StorSimple Manager-tjänsten](storsimple-device-dashboard.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

