---
title: ta med fil
description: ta med fil
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 6a3e2034792fdc0a4a8fed7885c7d5ad78ea24d9
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501282"
---
B-serien VM-familj kan du välja vilka VM-storlek har du nödvändiga basnivå prestanda för din arbetsbelastning med möjlighet att utöka CPU-prestanda upp till 100% av en Intel® Broadwell E5-2673 v4 2,3 GHz eller en Intel® Haswell 2,4 GHz E5-2673 v3-processor virtuell processor.

Virtuella datorer i B-serien är idealiska för arbetsbelastningar som inte behöver fullständig prestanda för Processorn kontinuerligt, som webbservrar, bevis på koncept, små databaser och skapa utvecklingsmiljöer. Dessa arbetsbelastningar har normalt anpassningsbara prestandakrav. B-serien ger dig möjligheten att köpa en VM-storlek med baslinje-prestanda och den Virtuella datorinstansen byggs upp krediter när den använder mindre än dess baslinje. När den virtuella datorn har ackumulerats kredit, utöka den virtuella datorn ovanför baslinjen med upp till 100% av den virtuella processorn när ditt program kräver mer processorkraft.

B-serien levereras i storlekarna som följande:

| Size             | Virtuell processor  | Minne: GiB | Temporär lagring (SSD) GiB | CPU-basprestanda för VM | CPU-maxprestanda för VM | Initial kredit | Kredit till bank/timme | Max kredit till bank | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202 %                  | 1 200 %                   | 360                   | 121                 | 2 909           | 16                                     | 6480 / 75                                 | 4320 / 50                                  | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270 %                  | 1 600 %                   | 480                   | 162                 | 3 888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337 %                  | 2 000 %                   | 600                   | 203                 | 4 860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls stöds endast på Linux

## <a name="workload-example"></a>Exempel på arbetsbelastning

Tänk dig ett i/utcheckning office-program. Programmet måste CPU-belastning under kontorstid, men inte mycket datorkraft under belastning. I det här exemplet kräver arbetsbelastningen i en virtuell dator i 16vCPU med 64GiB ram att arbeta effektivt.

Tabellen visar per timme trafikdata och diagrammet är en visuell representation av denna trafik.

B16 egenskaper:

Max CPU-prestanda: 16vCPU * 100% = 1 600%

Baslinje: 270 %

![Diagram över per timme trafikdata](./media/virtual-machines-common-b-series-burstable/office-workload.png)

| Scenario | Tid | CPU-användning (%) | Krediter ackumuleras<sup>1</sup> | Krediter tillgängliga |
| --- | --- | --- | --- | --- |
| B16ms distribution | Distribution | Distribution  | 480 (inledande krediter) | 480 |
| Ingen trafik | 0:00 | 0 | 162 | 642 |
| Ingen trafik | 1:00 | 0 | 162 | 804 |
| Ingen trafik | 2:00 | 0 | 162 | 966 |
| Ingen trafik | 3:00 | 0 | 162 | 1128 |
| Ingen trafik | 4:00 | 0 | 162 | 1290 |
| Ingen trafik | 5:00 | 0 | 162 | 1452 |
| Med låg trafik | 6:00 | 270 | 0 | 1452 |
| Anställda gå till office (app behöver 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Anställda fortsätta kommer till office (app behöver 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Med låg trafik | 9:00 | 270 | 0 | 240 |
| Med låg trafik | 10:00 | 100 | 102 | 342 |
| Med låg trafik | 11:00 | 50 | 132 | 474 |
| Med låg trafik | 12:00 | 100 | 102 | 576 |
| Med låg trafik | 13:00 | 100 | 102 | 678 |
| Med låg trafik | 14:00 | 50 | 132 | 810 |
| Med låg trafik | 15:00 | 100 | 102 | 912 |
| Med låg trafik | 16:00 | 100 | 102 | 1014 |
| Anställda checka ut (app behov 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Med låg trafik | 18:00 | 270 | 0 | 216 |
| Med låg trafik | 19:00 | 270 | 0 | 216 |
| Med låg trafik | 20:00 | 50 | 132 | 348 |
| Med låg trafik | 21:00 | 50 | 132 | 480 |
| Ingen trafik | 22:00 | 0 | 162 | 642 |
| Ingen trafik | 23:00 | 0 | 162 | 804 |

<sup>1</sup> krediter ackumuleras/krediter används i en timme motsvarar: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Timpriset är $0.936 per timme (månatliga $673.92) och för B16ms med 16 vcpu: er och 64 GiB minne hastigheten är $0.794 per timme (månatliga $547.86) för en D16s_v3 som har 16 virtuella processorer och 64 GiB minne. <b> Detta resulterar i en besparing på 15%!</b>

## <a name="q--a"></a>Frågor och svar

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>F: Hur får du 135% baslinjeprestanda från en virtuell dator?
**S**: 135% delas mellan den 8 virtuella processorer som utgör virtuella datorstorlek. Till exempel om programmet använder 4 av 8 kärnor som arbetar med batch-bearbetning, och var och en av de 4 vCPU körs på 30% utnyttjande den totala mängden Virtuella CPU-prestanda skulle vara lika med 120%.  Vilket innebär att den virtuella datorn skulle skapa kredit tid baserat på 15% deltadata från din baslinjeprestanda.  Men det innebär också att om du har samma virtuella dator kan användas för 100% av alla 8 virtuella processorer krediter ger den virtuella datorn en högsta CPU-prestanda för 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>F: Hur övervakar jag mitt saldo och förbrukning
**S**: Vi kommer att lägga till 2 nya mått under de kommande veckorna den **kredit** mått gör att du kan visa hur många krediter som den virtuella datorn har till bank och **ConsumedCredit** mått visar hur många CPU-krediter som den virtuella datorn har förbrukad från din budget.    Du kommer att kunna visa dessa mått från fönstret mått i portalen eller via Azure Monitor-API: er.

Mer information om hur du kommer åt mätvärden för Azure finns i [översikt över mått i Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>F: Hur ackumuleras krediter?
**S**: Priser för virtuella datorer anhopning och förbrukning är inställda så att en virtuell dator som kör på exakt dess grundläggande prestandanivå har varken en net anhopning eller förbrukning av bursting krediter.  En virtuell dator har en net ökning i kredit varje gång den körs under dess grundläggande prestandanivå och har en net minskning i kredit när Virtuellt datorn använder CPU som är mer än grundläggande prestanda.

**Exempel**:  Jag distribuera en virtuell dator med B1ms storleken för små tid och närvaro databasprogram. Den här storleken kan mitt program att använda upp till 20% av en virtuell processor som min baslinje, vilket är 0,2 krediter per minut som jag kan använda eller bank. 

Mitt program är upptagen i början och slutet av min anställda arbetsdag mellan 7:00-9:00 och 4:00 – 18:00:00. Under de andra 20 timmarna på dagen, mitt program är vanligtvis vid inaktivitet, endast med 10% av den virtuella processorn. För de låg belastning, jag betalar, desto 0,2 krediter per minut men bara använda 0.l krediter per minut, så att den virtuella datorn kommer bank 0.1 x 60 = 6 krediter per timme.  För de 20 timmarna som jag är låg, kommer jag bankens 120 krediter.  

Under belastning mitt program anger 60% vCPU utnyttjande, jag fortfarande erhålla 0,2 krediter per minut, men jag använda 0,6 krediter per minut, en net kostnaden för 0,4 krediter en minut eller 0.4 x 60 = 24 kredit per timme. Jag har 4 timmar per dag för högsta användning, så att det kostar 4 x 24 = 96 krediter för min högsta användning.

Om jag tar 120 krediter jag tjänade med låg belastning och subtrahera 96 krediter som jag har använt för min Högbelastningstider bankens jag en ytterligare 24 krediter per dag som jag kan använda för andra ökningar av aktivitet.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>F: Hur kan jag för att beräkna krediter som ackumuleras används?
**S**: Du kan använda följande formel: 

(Basera CPU-prestanda i VM - CPU-användning) / 100 = krediter bank eller använda per minut

t.ex i ovan instans baslinjen är 20% och om du använder 10% av CPU som du kan sparas (20% - 10%) / 100 = 0,1 kredit per minut.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>F: B-serien har stöd för datadiskar i Premium Storage?
**S**: Ja, alla B-serien storlekar som stöder datadiskar i Premium Storage.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>F: Varför har min återstående kredit värdet 0 när du har en omdistribution eller stoppa/starta?
**EN** : När en virtuell dator är ”REDPLOYED” och den virtuella datorn flyttas till en annan nod, den ackumulerade krediten går förlorade. Om den virtuella datorn är stoppad/igång, men finns kvar på samma nod, behåller den virtuella datorn den ackumulerade krediten. När den virtuella datorn startar nytt på en nod, blir det en initial kredit, för Standard_B8ms är det 240 minuter.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>F: Vad händer om jag distribuerar en stöds inte OS-avbildning på B1ls?
**EN** : B1ls stöder endast Linux-avbildningar och om du distribuerar en annan OS-avbildning kan du inte får bästa möjliga kundupplevelse.
