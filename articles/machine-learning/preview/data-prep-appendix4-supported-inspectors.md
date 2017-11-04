---
title: "Kontrollanter tillgängliga med Azure Machine Learning förberedelse av Data som stöds | Microsoft Docs"
description: "Det här dokumentet innehåller en fullständig lista över kontrollanter som är tillgängliga för Azure Machine Learning förberedelse av data"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 35d7c04f245e93d8cc795dca7c01c2bab5a14eb8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Stöds kontrollanter för förberedelse av förhandsversionen av Azure Machine Learning-data
Det här dokumentet beskriver uppsättningen kontrollanter som är tillgängliga i den här förhandsgranskningen.

## <a name="the-halo-effect"></a>Halo effekt 
Vissa kontrollanter stöder halo effekt. Den här effekten använder två olika färger att omedelbart visar ändringen visuellt från en transformering. Grå representerar värde innan den senaste transformeringen och blå visas det aktuella värdet. Detta kan vara aktiverad och inaktiverad i alternativ.

## <a name="graphical-filtering"></a>Grafisk filtrering 
Några av kontrollanterna stöd för filtrering av data genom att använda inspector som en redigerare. Med inspector som en redigerare innebär att välja grafiska element och sedan använda verktygsfältet i den övre högra delen av fönstret inspector för att filtrera in eller ut de valda värdena. 

## <a name="column-statistics"></a>Kolumnstatistik
Den här inspector innehåller en mängd olika statistik om kolumnen för numeriska kolumner. Statistiken omfattar följande mått: 
- Minimum
- Lägre KVARTIL
- Median
- Övre KVARTIL
- Maximalt
- Genomsnittlig
- Standardavvikelse


### <a name="options"></a>Alternativ 
- Ingen

## <a name="histogram"></a>Histogram 
Beräknar och visar ett histogram för en numerisk kolumn. Antalet buckets som standard beräknas med mappen regeln. Regeln kan dock åsidosättas via alternativen.

Den här Inspector stöder halo effekt.


### <a name="options"></a>Alternativ
- Minsta antalet buckets (gäller även när standard bucketing kontrolleras)
- Standardantalet buckets (mappen regel) 
- Visa halo
- Kernel densitet ritytans överlägget (Gaussisk kernel) 


### <a name="actions"></a>Åtgärder
Den här inspector stöder filtrering via buckets som kan omfatta enkel eller välja flera buckets. Använda filter enligt beskrivningen ovan.

## <a name="value-counts"></a>Värdet antal
Den här inspector visar en frekvenstabell med värden för den kolumnen är markerad. Standardinställningen är för antal sex värden. Du kan dock ändra gränsen till ett tal. Du kan också ange skärmen för att räkna längst ned i stället upp. Den här inspector stöder halo effekt.

### <a name="options"></a>Alternativ 
- Antal antal värden
- Fallande
- Är null/felvärdena
- Visa halo


### <a name="actions"></a>Åtgärder 
Den här inspector stöder filtrering via staplar, som kan omfatta enkel eller välja flera fält. Använda filter enligt beskrivningen ovan.

## <a name="box-plot"></a>Låddiagram 
En whisker Låddiagram för en numerisk kolumn.

### <a name="options"></a>Alternativ 
- Gruppera efter kolumn

## <a name="scatter-plot"></a>Punktdiagram ritytans
Punktdiagram ritning för två numeriska kolumner. Data är ned prov av prestandaskäl. Exempelstorleken kan åsidosättas i alternativen.

### <a name="options"></a>Alternativ  
- X-axeln kolumn
- Y-axeln kolumn
- Provtagning
- Gruppera efter kolumn


## <a name="time-series"></a>Tidsserier
Ett linjediagram med tiden medvetenhet på x-axeln.

### <a name="options"></a>Alternativ
- Datumkolumnen
- Numerisk kolumn
- Provtagning


### <a name="actions"></a>Åtgärder
Den här inspector stöder filtrering via en Klicka och dra väljer metoden att välja ett område i diagrammet. När du har slutfört markeringen filter enligt beskrivningen ovan.


## <a name="map"></a>Karta 
En karta med punkter som ritas, förutsatt att latitud och longitud har angetts. Latituden måste väljas först.

### <a name="options"></a>Alternativ
- Latitud kolumn
- Longitud kolumn
- Kluster på
- Gruppera efter kolumn


### <a name="actions"></a>Åtgärder
Den här inspector stöder filtrering via punkt markeringen på kartan. Tryck på den **Ctrl** nyckel, och klicka och dra med musen för att bilda en fyrkant runt punkterna. Tillämpa filter enligt beskrivningen ovan.

Du kan snabbt ändra storlek på kartan för att visa endast möjliga punkter genom att trycka på **E** vänster på kartan.
