---
title: Machine learning-funktionen i Azure Data Explorer
description: Använd maskininlärning klustring för analys av rotorsaker i Datautforskaren i Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: bc72cc21ab525ec82d9ce4b24e80ce82d92a5d21
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233509"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Machine learning-funktionen i Azure Data Explorer

Azure Data Explorer, en Big Data-analysplattform som används för att övervaka tjänstens hälsotillstånd, QoS eller felaktig enheter för avvikande beteende med hjälp av inbyggda [avvikelseidentifiering och prognostisering](/azure/data-explorer/anomaly-detection) funktioner. När en avvikande mönster har identifierats, utförs en rot orsak Analysis (RCA) för att undvika eller lösa avvikelsen.

Du kan undersöka processen är komplexa och långvariga och görs av områdesexperter. Processen omfattar hämtar och koppla ytterligare data från olika källor till samma tidsram, letar du efter ändringar i distributionen av värden för flera dimensioner, diagram ytterligare variabler och andra tekniker baserat på kunskap och intuition. Eftersom dessa diagnos scenarier är vanliga i Datautforskaren i Azure är machine learning-plugin-program tillgängliga att underlätta fasen diagnos och minska varaktigheten av RCA.

Azure Data Explorer har tre Machine Learning-plugin-program: [ `autocluster` ](/azure/kusto/query/autoclusterplugin), [ `basket` ](/azure/kusto/query/basketplugin), och [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin). Alla plugin-program implementera klustring algoritmer. Den `autocluster` och `basket` plugin-program klustret en enda uppsättning av poster och `diffpatterns` plugin-programmet kluster skillnaderna mellan två uppsättningar av poster.

## <a name="clustering-a-single-record-set"></a>Klustring en enda postuppsättning

Ett vanligt scenario innehåller en datauppsättning som valts av ett visst villkor, till exempel tidsfönster som uppvisar avvikande beteende, hög temperatur enheten avläsningar, långsamma kommandon och top utgifter användare. Vi vill ha ett snabbt och enkelt sätt att hitta vanliga mönster (segment) i data. Mönster är en del av datauppsättningen vars poster har samma värden över flera dimensioner (kategoriska kolumner). Följande fråga skapar och visar en tidsserie med undantag för service under en vecka i tio minuter lagerplatser:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Tjänsten undantag tidsdiagram](media/machine-learning-clustering/service-exceptions-timechart.png)

Tjänsten antalet undantag som är kopplat till övergripande tjänsten trafik. Du tydligt kan se dagliga mönstret för arbetsdagar från måndag till fredag, med en ökning av antalet service undantag räknar mitten dag och sjunker i antalet under natten. Fast lågt antal visas under helgen. Undantag toppar som kan identifieras med [time series-avvikelseidentifiering](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) i Datautforskaren i Azure.

Andra topp i data sker på eftermiddagen tisdag. Följande fråga för ytterligare diagnos den här insamling. Använd fråga till ritas om runt topp i högre upplösning (åtta timmar på en minut lagerplatser) för att kontrollera om det är en sharp topp och visa dess gränser.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Fokusera på topp tidsdiagram](media/machine-learning-clustering/focus-spike-timechart.png)

Vi kan se en smal två minuter topp från 15:00 till 15:02. Antal undantag i den här tvåminutersperiod i följande fråga:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

Exempel 20 undantag utanför 972 i följande fråga:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Region | ScaleUnit | DeploymentId                     | Spårningspunkten | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Använd autocluster() för enkel postuppsättning klustring

Även om det finns mindre än tusen undantag, är det fortfarande svårt att hitta vanliga segment, eftersom det finns flera värden i varje kolumn. Du kan använda [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) plugin-program för att extrahera en lista över vanliga segment och hitta den intressanta direkt kluster inom två minuter för i insamling som visas i följande fråga:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Count | Procent | Region | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Du kan se resultaten ovan att segmentet mest dominerande innehåller 65.74% av totalt antal undantag posterna och delar fyra dimensioner. Nästa segment är mycket mindre vanligt, innehåller endast 9.67% av posterna och delar tre dimensioner. De andra segmenten är även mindre vanliga. 

Autocluster använder en upphovsrättsskyddade algoritm för utvinningsmodellen flera dimensioner och extrahering av intressanta segment. ”Intressanta” innebär att varje segment har betydande täckning av både uppsättningen poster och dess funktioner. Segmenten är också delats, vilket innebär att var och en skiljer sig avsevärt från de andra. En eller flera av dessa segment kan vara användbart för RCA-processen. För att minimera segment granskning och utvärdering, extraherar autocluster endast en utvalt segment-lista.

### <a name="use-basket-for-single-record-set-clustering"></a>Använd basket() för enkel postuppsättning klustring

Du kan också använda den [ `basket()` ](/azure/kusto/query/basketplugin) plugin-programmet som visas i följande fråga:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Count | Procent | Region | ScaleUnit | DeploymentId | Spårningspunkten | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Korgen implementerar Apriori-algoritm för objektet ange datautvinning och extraherar alla segment vars täckning postuppsättningens är över tröskeln (standardvärdet är 5%). Du kan se att fler segment extraherades med liknande ansikten (till exempel, segment 0,1 eller 2,3).

Båda plugin-program är kraftfull och enkel att använda, men deras viktig begränsning beror på att de klustret en enda post på ett sätt som ej kontrollerade (med omärkta). Därför är det oklart om de extrahera mönster beskriva den valda postuppsättningen (avvikande poster) eller den globala postuppsättningen.

## <a name="clustering-the-difference-between-two-records-sets"></a>Klustring skillnaden mellan två postuppsättningar

Den [ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) plugin-program utan begränsning av `autocluster` och `basket`. `Diffpatterns` tar två uppsättningar av poster och extraherar de huvudsakliga segment som skiljer sig mellan dem. En uppsättning innehåller vanligtvis avvikande postuppsättningen på att undersökas (en analyseras av `autocluster` och `basket`). Den andra uppsättningen innehåller referens postuppsättning (baslinje). 

I frågan nedan använder vi `diffpatterns` att hitta intressant kluster inom två minuter för i insamling, vilket skiljer sig från kluster i baslinjen. Vi definierar fönstret baslinje som åtta minuter innan 15:00 (när insamling startades). Vi måste också utöka genom en binär kolumn (AB) som anger om en viss post tillhör baslinjen eller till uppsättningen med avvikande. `Diffpatterns` implementerar en algoritm för övervakad inlärning, där två klass-etiketter har genererats av avvikande jämfört med baslinje-flaggan (AB).

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | Region | ScaleUnit | DeploymentId | Spårningspunkten |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Det mest dominerande segmentet är samma segmentet som har extraherats av `autocluster`, dess täckning på den avvikande tvåminutersperiod är också 65.74%. Men dess täckning i åtta minuter baslinje-fönstret är endast 1.7%. Skillnaden är 64.04%. Den här skillnaden verkar vara relaterade till avvikande insamling. Du kan kontrollera detta antagande genom att dela det ursprungliga diagrammet i de poster som hör till det här problematiska segmentet jämfört med de andra segmenten som visas i frågan nedan:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Verifiera 'diffpattern' segment tidsdiagram](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Det här diagrammet gör att vi kan se att det var topp på tisdag eftermiddagen på grund av undantag från den här specifika segmentet som identifierades med hjälp av den `diffpatterns` plugin-programmet.

## <a name="summary"></a>Sammanfattning

Azure Data Explorer Machine Learning-plugin-program är till hjälp i många scenarier. Den `autocluster` och `basket` implementerar oövervakade Inlärningsalgoritmen och är lätt att använda. `Diffpatterns` implementerar övervakat Inlärningsalgoritmen även mer komplexa, är det mer kraftfulla vid extrahering differentiering segment för RCA.

Dessa plugin-program används interaktivt i ad hoc-scenarier och automatisk nästan i realtid övervakningstjänster. I Datautforskaren i Azure följs time series-avvikelseidentifiering av en diagnos process som är optimerade för att uppfylla nödvändiga prestanda standarder.
