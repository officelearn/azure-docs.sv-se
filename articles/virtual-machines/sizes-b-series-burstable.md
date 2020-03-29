---
title: B-serien sprängbar - Virtuella Azure-datorer
description: Beskriver B-serien med burstable Azure VM-storlekar.
services: virtual-machines
author: ayshakeen
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: ayshak
ms.openlocfilehash: 5121ef8eb3123d830233e91968c69b331d255bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161088"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>B-serien sprängbara storlekar för virtuella datorer

Virtuella datorer i B-serien är idealiska för arbetsbelastningar som inte behöver processorns fulla prestanda kontinuerligt, som webbservrar, konceptbevis, små databaser och utvecklingsversionsmiljöer. Dessa arbetsbelastningar har vanligtvis burstable prestandakrav. B-serien ger dig möjlighet att köpa en VM-storlek med baslinjeprestanda och VM-instansen bygger upp krediter när den använder mindre än baslinjen. När den virtuella datorn har ackumulerat kredit kan den virtuella datorn brista ovanför baslinjen med upp till 100 % av vCPU när ditt program kräver högre CPU-prestanda.

B-serien finns i följande VM-storlekar:

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Bas-CPU Perf för VM | Max CPU Perf av VM | Inledande krediter | Krediter bankade/timme | Max bankade krediter | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps | Max oankopplat diskdataflöde: IOPS/MBps | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5 %   | 100 %  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10 %  | 100 %  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20 %  | 100 %  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40 %  | 200 %  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60 %  | 200 %  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90 %  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000 % | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls stöds endast på Linux

## <a name="workload-example"></a>Exempel på arbetsbelastning

Överväg ett office-inchecknings-/ut-program. Ansökan behöver CPU skurar under kontorstid, men inte mycket datorkraft under off timmar. I det här exemplet kräver arbetsbelastningen en virtuell dator med 16vCPU med 64GiB RAM för att fungera effektivt.

Tabellen visar trafikdata per timme och diagrammet är en visuell representation av den trafiken.

B16 Egenskaper:

Max CPU perf: 16vCPU * 100% = 1600%

Baslinje: 270%

![Diagram över trafikdata per timme](./media/b-series-burstable/office-workload.png)

| Scenario | Tid | CPU-användning (%) | Krediter ackumulerade<sup>1</sup> | Krediter tillgängliga |
| --- | --- | --- | --- | --- |
| B16ms-distribution | Distribution | Distribution  | 480 (Inledande krediter) | 480 |
| Ingen trafik | 0:00 | 0 | 162 | 642 |
| Ingen trafik | 1:00 | 0 | 162 | 804 |
| Ingen trafik | 2:00 | 0 | 162 | 966 |
| Ingen trafik | 3:00 | 0 | 162 | 1128 |
| Ingen trafik | 4:00 | 0 | 162 | 1290 |
| Ingen trafik | 5:00 | 0 | 162 | 1452 |
| Låg trafik | 6:00 | 270 | 0 | 1452 |
| Anställda kommer till kontoret (appen behöver 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Anställda fortsätter att komma till kontoret (appen behöver 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Låg trafik | 9:00 | 270 | 0 | 240 |
| Låg trafik | 10:00 | 100 | 102 | 342 |
| Låg trafik | 11:00 | 50 | 132 | 474 |
| Låg trafik | 12:00 | 100 | 102 | 576 |
| Låg trafik | 13:00 | 100 | 102 | 678 |
| Låg trafik | 14:00 | 50 | 132 | 810 |
| Låg trafik | 15:00 | 100 | 102 | 912 |
| Låg trafik | 16:00 | 100 | 102 | 1014 |
| Anställda checkar ut (appen behöver 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Låg trafik | 18:00 | 270 | 0 | 216 |
| Låg trafik | 19:00 | 270 | 0 | 216 |
| Låg trafik | 20:00 | 50 | 132 | 348 |
| Låg trafik | 21:00 | 50 | 132 | 480 |
| Ingen trafik | 22:00 | 0 | 162 | 642 |
| Ingen trafik | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Ackumulerade krediter/krediter som används under `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`en timme motsvarar: .  

För en D16s_v3 som har 16 vCPUs och 64 GiB minne timtaxan är $ 0,936 per timme (månatliga $ 673,92) och för B16ms med 16 vCPUs och 64 GiB minne är hastigheten $ 0,794 per timme (månatliga $ 547,86). <b>Detta resulterar i 15% besparingar!</b>

## <a name="q--a"></a>Vanliga frågor och svar

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>F: Hur får du 135% baslinjeprestanda från en virtuell dator?

**A:** Den 135% delas mellan 8 vCPU är som utgör vm storlek. Till exempel, om ditt program använder 4 av de 8 kärnor som arbetar med batchbearbetning och var och en av dessa 4 vCPU:er körs på 30 % utnyttjande skulle den totala mängden VM-processorprestanda vara lika med 120 %.  Vilket innebär att din virtuella dator skulle bygga kredittid baserat på 15% delta från din baslinje prestanda.  Men det innebär också att när du har krediter tillgängliga samma virtuella dator kan använda 100% av alla 8 vCPU: s ger den virtuella datorn en Max CPU-prestanda på 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>F: Hur kan jag övervaka mitt kreditsaldo och min förbrukning

**A:** Vi kommer att införa 2 nya mått under de kommande veckorna, **kredit** måttet gör att du kan visa hur många krediter din vm har bankat och **ConsumedCredit** måttet visar hur många CPU-krediter din virtuella dator har förbrukat från banken.    Du kan visa dessa mått från statistikfönstret i portalen eller programmässigt via Azure Monitor API:er.

Mer information om hur du kommer åt mätdata för Azure finns [i Översikt över mått i Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated"></a>F: Hur ackumuleras krediter?

**S:** Vm-ackumulering och förbrukningshastigheter ställs in så att en virtuell dator som körs på exakt sin basprestandanivå varken har någon nettoackumulering eller förbrukning av bursting-krediter.  En virtuell dator kommer att ha en nettoökning av krediter när den körs under sin basprestandanivå och kommer att ha en nettominskning av krediter när den virtuella datorn använder processorn mer än sin basprestandanivå.

**Exempel:** Jag distribuerar en virtuell dator med B1ms storlek för min lilla tid och närvaro databasprogram. Denna storlek gör att mitt program att använda upp till 20% av en vCPU som min baslinje, vilket är 0,2 krediter per minut jag kan använda eller bank.

Min ansökan är upptagen i början och slutet av mina anställda arbetsdag, mellan 7:00-9:00 och 4:00 - 6:00. Under de andra 20 timmarna av dagen, är min ansökan vanligtvis på tomgång, bara med 10% av vCPU. För icke-rusningstid, tjänar jag 0,2 krediter per minut men bara konsumerar 0.l krediter per minut, så min VM kommer bank 0,1 x 60 = 6 krediter per timme.  För de 20 timmar som jag är off-peak, kommer jag bank 120 krediter.  

Under rusningstid min ansökan genomsnitt 60% vCPU utnyttjande, tjänar jag fortfarande 0,2 krediter per minut men jag förbrukar 0,6 krediter per minut, för en nettokostnad på 0,4 krediter per minut eller 0,4 x 60 = 24 krediter per timme. Jag har 4 timmar per dag av toppanvändning, så det kostar 4 x 24 = 96 krediter för min topp användning.

Om jag tar 120 krediter jag tjänat off-peak och subtrahera de 96 krediter jag använde för min rusningstid, jag bank ytterligare 24 krediter per dag som jag kan använda för andra skurar av aktivitet.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>F: Hur kan jag beräkna krediter ackumulerade och använda?

**S:** Du kan använda följande formel:

(Bas CPU perf av VM - CPU-användning) / 100 = Krediter bank eller användning per minut

t.ex. i ovanstående fall är din baslinje 20% och om du använder 10% av processorn du ackumulerar (20%-10%)/100 = 0,1 kredit per minut.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>F: Stöder B-serien Premium Storage-datadiskar?

**S:** Ja, alla storlekar i B-serien stöder Premium Storage-datadiskar.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>F: Varför är min återstående kredit inställd på 0 efter en omdistribution eller ett stopp/start?

**S:** När en virtuell dator är "REDPLOYED" och den virtuella datorn flyttas till en annan nod, går den ackumulerade krediten förlorad. Om den virtuella datorn stoppas/startas, men finns kvar på samma nod, behåller den virtuella datorn den ackumulerade krediten. När den virtuella datorn startar nytt på en nod, får den en första kredit, för Standard_B8ms det är 240 minuter.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>F: Vad händer om jag distribuerar en OS-avbildning som inte stöds på B1ls?

**A:** B1ls stöder bara Linux-avbildningar och om du distribuerar någon annan OS-avbildning kanske du inte får den bästa kundupplevelsen.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.