---
title: Övervaka din StorSimple 8000-serieenhet
description: Beskriver hur du använder Tjänsten StorSimple Device Manager för att övervaka användning, I/O-prestanda och kapacitetsutnyttjande.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: b3b77024606c5cdb02ff7bdd357c2d14a2415efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277045"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Använda Tjänsten StorSimple Device Manager för att övervaka din StorSimple-enhet

## <a name="overview"></a>Översikt
Du kan använda Tjänsten StorSimple Device Manager för att övervaka specifika enheter i din StorSimple-lösning. Du kan skapa anpassade diagram baserat på I/O-prestanda, kapacitetsutnyttjande, nätverksdataflöde och enhetsprestandamått och fästa dem på instrumentpanelen. Mer information finns i [anpassa portalinstrumentpanelen](../azure-portal/azure-portal-dashboards.md).

Om du vill visa övervakningsinformation för en viss enhet väljer du tjänsten StorSimple Device Manager i Azure-portalen. Välj enheten i listan över enheter och gå sedan till **Övervaka**. Du kan sedan se diagrammen **Kapacitet,** **Användning**och **Prestanda** för den valda enheten.

## <a name="capacity"></a>Kapacitet
**Kapaciteten** spårar det etablerade utrymmet och det utrymme som finns kvar på enheten. Den återstående kapaciteten visas sedan som lokalt fäst eller nivåindelad.

Den etablerade och återstående kapaciteten delas upp ytterligare efter nivåindelade och lokalt fästa volymer. För varje volym visas den etablerade kapaciteten och den återstående kapaciteten på enheten.

![I/o-kapacitet](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Användning
**Användningen** spårar mått som är relaterade till mängden datalagringsutrymme som används av volymerna, volymbehållarna eller enheten. Du kan skapa rapporter baserat på kapacitetsutnyttjandet av din primära lagring, din molnlagring eller din enhetslagring. Kapacitetsutnyttjande kan mätas på en viss volym, en specifik volymbehållare eller alla volymbehållare.
Som standard rapporteras användningen för de senaste 24 timmarna. Du kan redigera diagrammet om du vill ändra hur länge användningen rapporteras genom att välja mellan:
* Senaste 24 timmarna
* Senaste 7 dagarna
* Senaste 30 dagarna
* Senaste 90 dagarna
* Förra året

Två viktiga mått, tillväxt och intervall rapporteras för användningsdiagrammen. Intervall refererar till det maximala värdet och minimivärdena för den rapporterade användningen under den valda varaktigheten (för instans, senaste 7 dagarna).

Tillväxt avser ökningen av användningen från den första dagen till den sista dagen under den valda varaktigheten. 

Tillväxt och räckvidd kan också representeras av följande ekvationer:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Den primära, molnet och lokala lagring som används kan beskrivas på följande sätt:

### <a name="primary-storage-usage"></a>Primär lagringsanvändning
Dessa diagram visar mängden data som skrivs till StorSimple-volymer innan data dedupliceras och komprimeras. Du kan visa den primära lagring som används av alla volymer i en volymbehållare eller för en enskild volym. Den primära lagring som används bryts ned ytterligare efter primär nivåindelad lagring som används och primär lokalt nålad lagring som används.

Följande diagram visar den primära lagring som används för en StorSimple-enhet före och efter att en ögonblicksbild i molnet togs. Eftersom det bara är volymdata bör en ögonblicksbild i molnet inte ändra den primära lagringen. Som du kan se visar diagrammet ingen skillnad i den primära nivåindelade eller lokalt fästa lagringen som används som ett resultat av att ta en ögonblicksbild av molnet. Ögonblicksbilden av molnet började vid 23.50-tiden på den enheten.

![Primär kapacitetsutnyttjande efter ögonblicksbild av molnet](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Om du nu kör I/O på värden som är ansluten till StorSimple-enheten ser du en ökning av primär nivåindelad lagring eller primär lokalt fäst lagring som används beroende på vilka volymer (nivåindelade eller lokalt fästa) du skriver data till. Här är de primära lagringsanvändningsdiagrammen för en StorSimple-enhet. På den här enheten började StorSimple-värden att betjäna skrivningar runt 14:30 på en nivåindelad volym på enheten. Du kan se toppen i skrivbyten/enheter som motsvarar IO som körs på värden.

![Prestanda när IO körs på nivåindelad volym](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Om du tittar på den primära nivåindelade lagring som används, som har gått upp medan den primära lokalt fästa användningen förblir oförändrad eftersom det inte finns några skrivningar som serveras till lokalt fästa volymer på enheten.

![Primärt kapacitetsutnyttjande när IO körs på nivåindelad volym](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Om du kör uppdatering 3 eller högre kan du dela upp det primära lagringskapacitetsutnyttjandet med en enskild volym, alla volymer, alla nivåindelade volymer och alla lokalt fästa volymer som visas nedan. Om du bryter ned efter alla lokalt fästa volymer kan du snabbt ta reda på hur mycket av den lokala nivån som förbrukas.

![Primärt kapacitetsutnyttjande för alla nivåindelade volymer](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Primärt kapacitetsutnyttjande för alla lokalt fästa volymer](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Du kan klicka vidare på var och en av volymerna i listan och se motsvarande användning.

![Primärt kapacitetsutnyttjande för alla lokalt fästa volymer](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Användning av molnlagring
Dessa diagram visar mängden molnlagring som används. Dessa data dedupliceras och komprimeras. Det här beloppet innehåller ögonblicksbilder av molnet som kan innehålla data som inte återspeglas i någon primär volym och som sparas för äldre eller nödvändiga kvarhållningsändamål. Du kan jämföra de primära och molnlagringsförbrukningssiffrorna för att få en uppfattning om dataminskningshastigheten, även om antalet inte kommer att vara exakt.

Följande diagram visar molnlagringsanvändningen för en StorSimple-enhet när en ögonblicksbild av molnet togs.

* Ögonblicksbilden av molnet startade runt 11:50 på den enheten och du kan se att det före ögonblicksbilden av molnet inte användes. 
* När ögonblicksbilden av molnet har slutförts sköt molnlagringsanvändningen upp 0,89 GB. 
* Medan ögonblicksbilden av molnet pågick finns det också en motsvarande topp i IO från enhet till moln.

    ![Användning av molnlagring före ögonblicksbild av molnet](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Användning av molnlagring efter ögonblicksbild av molnet](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![I/o från enhet till moln under en ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Lokal lagringsanvändning
Dessa diagram visar den totala användningen för enheten, vilket kommer att vara mer än primär lagringsanvändning eftersom den innehåller den linjära SSD-nivån. Den här nivån innehåller en mängd data som också finns på enhetens andra nivåer. Kapaciteten i den linjära SSD-nivån är växlad så att när nya data kommer in flyttas de gamla data till hårddisknivån (då den dedupliceras och komprimeras) och därefter till molnet.

Med tiden ökar primärlagring som används och lokal lagring som används troligen tillsammans tills data börjar nivåindelas till molnet. Vid den tidpunkten kommer den lokala lagring som används förmodligen att börja platå, men den primära lagring som används kommer att öka när mer data skrivs.

Följande diagram visar den primära lagring som används för en StorSimple-enhet när en ögonblicksbild av molnet togs. Ögonblicksbilden av molnet startade klockan 11:50 och den lokala lagringen började minska vid den tidpunkten. Den lokala lagring som används minskade från 1,445 GB till 1,09 GB. Detta indikerar att de troligen okomprimerade data i den linjära SSD-nivån deduplicerades, komprimerades och flyttades till hårddisknivån. Observera att om enheten redan har en stor mängd data i både SSD- och HDD-nivåerna kanske du inte ser den här minskningen. I det här exemplet har enheten en liten mängd data.

![Lokal lagringsanvändning efter ögonblicksbild av molnet](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Prestanda
**Prestanda** spårar mått som är relaterade till antalet läs- och skrivåtgärder mellan antingen iSCSI-initierargränssnitten på värdservern och enheten eller enheten och molnet. Den här prestandan kan mätas för en viss volym, en specifik volymbehållare eller alla volymbehållare. Prestanda inkluderar även CPU-användning och nätverksdataflöde för de olika nätverksgränssnitten på enheten.

### <a name="io-performance-for-initiator-to-device"></a>I/O-prestanda för initierare till enhet
Diagrammet nedan visar I/O för initieraren till enheten för alla volymer för en produktionsenhet. De mätvärden som ritas läs- och skrivbyte per sekund. Du kan också kartlägga läsa, skriva och enastående IO, eller läsa och skriva latenser.

![I/O-prestanda för initierare till enhet](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>I/O-prestanda för enheten till molnet
För samma enhet ritas I/O-åtgärderna för data från enheten till molnet för alla volymbehållare. På den här enheten är data bara i den linjära nivån och ingenting har spillts till molnet. Det finns inga läs-skriv-åtgärder som inträffar från enhet till molnet. Topparna i diagrammet är därför med ett intervall på 5 minuter som motsvarar den frekvens med vilken pulsslag kontrolleras mellan enheten och tjänsten.

För samma enhet togs en ögonblicksbild i molnet för volymdata från 11:50. Detta resulterade i data som flödade från enheten till molnet. Skrivningar serverades till molnet under den här tiden. IO-diagrammet visar en topp i skrivbyten/s som motsvarar den tidpunkt då ögonblicksbilden togs.

![I/o från enhet till moln under en ögonblicksbild i molnet](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Nätverksdataflöde för enhetsnätverksgränssnitt
**Nätverksdataflöde** spårar mått som är relaterade till mängden data som överförs från nätverksgränssnitten för iSCSI-initieraren på värdservern och enheten och mellan enheten och molnet. Du kan övervaka det här måttet för var och en av iSCSI-nätverksgränssnitten på enheten.

Följande diagram visar nätverksdataflödet för Data 0, 1 1 GbE-nätverket på enheten, som var både molnaktiverat (standard) och iSCSI-aktiverat. På den här enheten den 14 juni runt 21:00, data var nivåindelade i molnet (inga ögonblicksbilder i molnet togs vid den tidpunkten som pekar på nivåindelning är mekanismen för att flytta data till molnet) vilket resulterade i IO som serveras till molnet. Det finns en motsvarande topp i diagrammet för nätverksdataflöde för samma tid och större delen av nätverkstrafiken är utgående till molnet.

![Nätverksdataflöde för data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Om vi tittar på data 1-gränssnittets dataflödesdiagram, ett annat 1 GbE-nätverksgränssnitt som bara var iSCSI-aktiverat, så var det praktiskt taget ingen nätverkstrafik under denna varaktighet.

![Nätverksdataflöde för data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>CPU-användning för enhet
**CPU-användning** spårar mått relaterade till processorn som används på enheten. Följande diagram visar statistik för CPU-utnyttjande för en enhet i produktion.

![CPU-användning för enhet](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder storsimple-enhetsenhetens instrumentpanel för Enhetshanteraren](storsimple-device-dashboard.md).
* Lär dig hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

