---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 7372a0c49f6e41ef4d336188ceb5cf4393f4c982
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
B-serien VM-familjen kan du välja vilka VM-storlek ger dig den nödvändiga basnivån prestandan för din arbetsbelastning möjligheten att burst CPU-prestanda upp till 100% av en Intel® Broadwell E5-2673 v4 2,3 GHz eller en Intel® Haswell 2,4 GHz E5-2673 v3-processor vCPU.

De virtuella datorerna B-serien är idealisk för arbetsbelastningar som inte behöver fullständig prestanda för Processorn kontinuerligt, som webbservrar, små databaser och utveckling och testmiljöer. Dessa arbetsbelastningar har vanligtvis burstable prestandakrav. B-serien ger dig möjligheten att köpa en VM-storlek med baslinje-prestanda och VM-instans som bygger upp krediter när den använder mindre än dess baslinjen. När den virtuella datorn har ackumulerats kredit, kan den virtuella datorn burst ovanför baslinjen med upp till 100% av vCPU när programmet kräver högre CPU-prestanda.

B-serien finns följande sex VM-storlekar:

| Storlek          | Vcpu's | Minne: GiB | Temporär lagring (SSD) GiB | Grundläggande CPU-prestanda för den virtuella datorn | Högsta CPU-prestanda för den virtuella datorn | Krediter till bank / timme | Max till bank krediter |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10 %                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20 %                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Frågor och svar 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>F: Hur skaffar du 135% baslinje-prestanda från en virtuell dator?
**En**: 135% delas mellan den 8 Vcpu's som utgör VM-storlek. Till exempel om programmet använder 4 8 kärnor arbetar med batch-bearbetning och var och en av de 4 vCPU körs med en 30% användning den totala mängden VM CPU-prestanda skulle vara lika med 120%.  Vilket innebär att den virtuella datorn skulle skapa kredit tid baserat på 15% delta från baslinje-prestanda.  Men det innebär också att om du har samma virtuella dator kan användas för 100% av alla 8 vCPU krediter ger den virtuella datorn en högsta CPU-prestanda för 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>F: hur övervakar jag min Kreditsaldot och förbrukning
**En**: Vi kommer att 2 nya mått under de kommande veckorna den **kredit** mått kan du se hur många krediter som den virtuella datorn har till bank och **ConsumedCredit** måttet visar hur många CPU den virtuella datorn har förbrukat från banken krediter.    Du kommer att kunna visa de här måtten från fönstret mått i portalen eller programmässigt via API: er för Azure-Monitor.

Mer information om hur du kommer åt data för mått för Azure finns [översikt över mått i Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>F: hur ackumulerade krediter?
**En**: den virtuella datorns anhopning och förbrukning är inställda så att en virtuell dator som kör på exakt dess grundläggande prestandanivå har varken en net anhopning eller förbrukning av burst-överföring krediter.  En virtuell dator har en net ökning i krediter varje gång den körs under dess grundläggande prestandanivå och har en minskning av net krediter när den virtuella datorn använder Processorn som är större än dess grundläggande prestandanivå.

**Exempel**: jag distribuera en virtuell dator med den B1ms storleken för min liten tid och närvaro databasprogram. Den här storleken kan mitt program att använda upp till 20% av en vCPU som min baslinje,.2 krediter per minut som jag kan använda eller bank. 

Mina program är upptagen i början och slutet av arbetsdagen min anställda, mellan 7:00 – 9:00 AM och 4:00 – 18:00. Under de andra 20 timmarna, mitt program är vanligtvis vid inaktivitet, endast med 10% av vCPU. För icke-användningsnivå jag tjäna 0,2 krediter per minut, men endast använda 0.l krediter per minut, så att den virtuella datorn kommer bank.1 x 60 = 6 krediter per timme.  Jag kommer bank 120 krediter för 20 timmar som jag är låg.  

Under vissa tider mitt program medelvärden 60% vCPU användning, jag fortfarande erhålla 0,2 krediter per minut, men jag använda 0,6 krediter per minut, för en net kostnaden för.4 krediter en minut eller.4 x 60 = 24 krediter per timme. Jag har fyra timmar per dag för maximal användning, så det kostar 4 x 24 = 96 krediter för min belastning användning.

Om jag ta 120 krediter som jag har fått låg och subtrahera 96 krediter som jag använder för min Högbelastningstider bank jag en ytterligare 24 krediter per dag som jag kan använda för andra belastning för aktiviteten.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>F: B-serien stöder Premium-lagring datadiskar?
**En**: Ja, alla B-serien storlekar stöd för Premium-lagring datadiskar.   
    
### <a name="q-my-remaining-credit-are-set-to-0-after-a-redepoy-or-a-stopstart"></a>F: min återstående kredit har angetts till 0 när du har en redepoy eller stoppa/starta.
**En** : när en virtuell dator är ”REDPLOYED”, det vill säga, den virtuella datorn flyttas till en annan nod och den ackumulerade krediten går förlorade. Om den virtuella datorn stoppade/startade, men finns kvar på samma nod, behåller den virtuella datorn ackumulerade kredit. När den virtuella datorn börjar ny på en nod, får den en inledande kredit, Standard_B8ms är det 240 minuter.

    

    
