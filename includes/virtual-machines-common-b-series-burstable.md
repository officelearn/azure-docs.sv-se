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
ms.openlocfilehash: 533fa1a8491a701571011f407b338e04fb6a7e8b
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "40182679"
---
B-serien VM-familj kan du välja vilka VM-storlek har du nödvändiga basnivå prestanda för din arbetsbelastning med möjlighet att utöka CPU-prestanda upp till 100% av en Intel® Broadwell E5-2673 v4 2,3 GHz eller en Intel® Haswell 2,4 GHz E5-2673 v3-processor virtuell processor.

Virtuella datorer i B-serien är idealiska för arbetsbelastningar som inte behöver fullständig prestanda för Processorn kontinuerligt, som webbservrar, små databaser och utveckling och testmiljöer. Dessa arbetsbelastningar har normalt anpassningsbara prestandakrav. B-serien ger dig möjligheten att köpa en VM-storlek med baslinje-prestanda och den Virtuella datorinstansen byggs upp krediter när den använder mindre än dess baslinje. När den virtuella datorn har ackumulerats kredit, utöka den virtuella datorn ovanför baslinjen med upp till 100% av den virtuella processorn när ditt program kräver mer processorkraft.

B-serien levereras i de sex storlekarna som följande:

| Storlek          | virtuella processorer | Minne: GiB | Temporär lagring (SSD) GiB | Grundläggande CPU-prestanda för virtuell dator | Max CPU-prestanda för virtuell dator | Krediter till bank / timme | Max till bank krediter |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10 %                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20 %                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Frågor och svar 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>F: hur får du 135% baslinjeprestanda från en virtuell dator?
**En**: 135% delas mellan den 8 virtuella processorer som utgör virtuella datorstorlek. Till exempel om programmet använder 4 av 8 kärnor som arbetar med batch-bearbetning, och var och en av de 4 vCPU körs på 30% utnyttjande den totala mängden Virtuella CPU-prestanda skulle vara lika med 120%.  Vilket innebär att den virtuella datorn skulle skapa kredit tid baserat på 15% deltadata från din baslinjeprestanda.  Men det innebär också att om du har samma virtuella dator kan användas för 100% av alla 8 virtuella processorer krediter ger den virtuella datorn en högsta CPU-prestanda för 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>F: hur övervakar jag mitt saldo och förbrukning
**En**: Vi kommer att lägga till 2 nya mått under de kommande veckorna den **kredit** mått gör att du kan visa hur många krediter som den virtuella datorn har till bank och **ConsumedCredit** mått visar hur många CPU krediter som den virtuella datorn har förbrukat från banken.    Du kommer att kunna visa dessa mått från fönstret mått i portalen eller via Azure Monitor-API: er.

Mer information om hur du kommer åt mätvärden för Azure finns i [översikt över mått i Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>F: hur ackumuleras krediter?
**En**: den virtuella datorns anhopning och förbrukning är inställda så att en virtuell dator som kör på exakt dess grundläggande prestandanivå har varken en net anhopning eller förbrukning av bursting krediter.  En virtuell dator har en net ökning i kredit varje gång den körs under dess grundläggande prestandanivå och har en net minskning i kredit när Virtuellt datorn använder CPU som är mer än grundläggande prestanda.

**Exempel**: jag distribuera en virtuell dator med B1ms storleken för min små tid och närvaro databasprogram. Den här storleken kan mitt program att använda upp till 20% av en virtuell processor som min baslinje, vilket är.2 krediter per minut som jag kan använda eller bank. 

Mitt program är upptagen i början och slutet av min anställda arbetsdag mellan 7:00-9:00 och 4:00 – 18:00:00. Under de andra 20 timmarna på dagen, mitt program är vanligtvis vid inaktivitet, endast med 10% av den virtuella processorn. För de låg belastning jag betalar, desto 0,2 krediter per minut men bara använda 0.l krediter per minut, så att den virtuella datorn kommer bank.1 x 60 = 6 krediter per timme.  För de 20 timmarna som jag är låg, kommer jag bankens 120 krediter.  

Under belastning mitt program anger 60% vCPU utnyttjande, jag fortfarande erhålla 0,2 krediter per minut, men jag använda 0,6 krediter per minut, en net kostnaden för.4 krediter en minut eller.4 x 60 = 24 kredit per timme. Jag har 4 timmar per dag för högsta användning, så att det kostar 4 x 24 = 96 krediter för min högsta användning.

Om jag tar 120 krediter jag tjänade med låg belastning och subtrahera 96 krediter som jag har använt för min Högbelastningstider bankens jag en ytterligare 24 krediter per dag som jag kan använda för andra ökningar av aktivitet.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>F: B-serien stöder datadiskar i Premium Storage?
**En**: Ja, alla B-seriestorlekar som stöd för datadiskar i Premium Storage.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>F: Varför har min återstående kredit värdet 0 efter en omdistribution eller stoppa/starta?
**En** : när den virtuella datorn är ”REDPLOYED” och den virtuella datorn flyttas till en annan nod, den ackumulerade krediten går förlorad. Om den virtuella datorn är stoppad/igång, men finns kvar på samma nod, behåller den virtuella datorn den ackumulerade krediten. När den virtuella datorn startar nytt på en nod, blir det en initial kredit, för Standard_B8ms är det 240 minuter.

    

    
