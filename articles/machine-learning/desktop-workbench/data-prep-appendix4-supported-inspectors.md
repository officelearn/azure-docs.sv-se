---
title: Kontroller som är tillgängliga med Azure Machine Learning förberedelse av Data som stöds | Microsoft Docs
description: Det här dokumentet innehåller en fullständig lista över kontroller som är tillgängliga för förberedelse av data i Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: ef5f6f3dc7ae0c555b2afe000b54c443313800f1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649350"
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Kontroller som stöds för förberedelse av förhandsversionen av Azure Machine Learning data
Det här dokumentet innehåller en uppsättning kontroller som är tillgängliga i den här förhandsversionen.

## <a name="the-halo-effect"></a>Halo-effekt 
Vissa kontroller har stöd för halo-effekten. Den här effekten använder två olika färger för att omedelbart visar ändrade visuellt från en transformering. Grå representerar värdet innan den senaste transformeringen och det blå fältet visar det aktuella värdet. Detta kan aktiveras och inaktiveras i alternativ.

## <a name="graphical-filtering"></a>Grafiska filtrering 
Några av kontrollerna stöder filtrering av data genom att använda inspector som en redigerare. Med inspector som en redigerare innebär att välja grafiska element och använder sedan i verktygsfältet i den övre högra delen av fönstret inspector för att filtrera in eller ut de valda värdena. 

## <a name="column-statistics"></a>Kolumnstatistik
Den här inspector innehåller en mängd olika statistik om kolumnen för numeriska kolumner. Statistiken omfattar följande mått: 
- Minimum
- Lägre kvartilen
- Median
- Övre kvartilen
- Maximal
- Medel
- Standardavvikelse


### <a name="options"></a>Alternativ 
- Ingen

## <a name="histogram"></a>Histogram 
Beräknar och visar ett histogram för en numerisk kolumn. Standardvärdet för antal buckets beräknas med hjälp av Scotts regel. Regeln kan dock åsidosättas via alternativen.

Den här Inspector stöder halo-effekten.


### <a name="options"></a>Alternativ
- Minsta antal buckets (gäller även när standard bucket har markerats)
- Standardvärdet för antal buckets (Scotts regel) 
- Visa halo
- Kernel densitet ritytans överlägget (Gaussisk kernel) 
- Använda logaritmisk skala


### <a name="actions"></a>Åtgärder
Den här inspector stöder filtrering via buckets, vilket kan innefatta en eller flera väljer buckets. Använda filter enligt beskrivningen ovan.

## <a name="value-counts"></a>Antal värden
Den här inspector anger en frekvenstabell med värden för den kolumn som för närvarande är markerad. Standardvisningen avser de antal sex värdena. Du kan dock ändra gränsen till ett tal. Du kan också ange skärmen för att räkna längst ned i stället för längst upp. Den här inspector stöder halo-effekten.

### <a name="options"></a>Alternativ 
- Antal antal värden
- Fallande
- Är null/felvärden
- Visa halo
- Använda logaritmisk skala


### <a name="actions"></a>Åtgärder 
Den här inspector stöder filtrering via staplar, vilket kan innefatta en eller flera väljer staplar. Använda filter enligt beskrivningen ovan.

## <a name="box-plot"></a>Låddiagram 
En whisker-diagram för box för en numerisk kolumn.

### <a name="options"></a>Alternativ 
- Gruppera efter kolumn

## <a name="scatter-plot"></a>Punktdiagram
Spridningsdiagrammet för två numeriska kolumner. Data är ned samplas av prestandaskäl. Urvalsstorlek kan åsidosättas i alternativen.

### <a name="options"></a>Alternativ  
- X-axeln kolumn
- Y-axeln kolumn
- Exempelstorlek
- Gruppera efter kolumn


## <a name="time-series"></a>Tidsserier
Ett linjediagram med tiden medvetenhet på x-axeln.

### <a name="options"></a>Alternativ
- Datumkolumnen
- Numerisk kolumn
- Exempelstorlek


### <a name="actions"></a>Åtgärder
Den här inspector stöder filtrering via en select Klicka och dra-metoden att välja ett område i diagrammet. När du har slutfört val av filter enligt beskrivningen ovan.


## <a name="map"></a>Karta 
En karta med punkter som ritas om vi antar att latitud och longitud har angetts. Du måste välja latitud först.

### <a name="options"></a>Alternativ
- Latitud kolumn
- Longitud kolumn
- Klustring på
- Gruppera efter kolumn


### <a name="actions"></a>Åtgärder
Den här inspector stöder filtrering via punkt val på kartan. Tryck på den **Ctrl** nyckel, och sedan klicka och dra med musen för att bilda en ruta runt poäng. Därefter tillämpa filter enligt beskrivningen ovan.

Du kan snabbt ändra storlek på kartan för att visa endast möjliga poäng genom att trycka på den **E** på vänster sida av kartan.


## <a name="pattern-frequency"></a>Mönstret frekvens 

Den här inspector visar en lista över mönster i den markerade kolumnen i strängen. Mönstren representeras med hjälp av ett reguljärt uttryck som syntax. Placera markören på mönstret visar exempel på värden som representeras av mönstret. Tillsammans med mönster visas också de ungefärliga coverages som ett procenttal.

![Bild av mönstret inspector](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Alternativ
- Antal antal värden
- Fallande
- Visa halo

### <a name="actions"></a>Åtgärder
Den här inspector stöder filtrering baserat på visas användningsmönster. Tryck på den **Ctrl** nyckel och välj sedan de fyllda staplarna i mönstret inspector. Därefter tillämpa filter enligt beskrivningen ovan. Till följd av användaren-acion läggs ett avancerat filter steg. Du kan se och ändra den genererade Python-koden genom att aktivera alternativet Redigera i Avancerat Filter-steget.
