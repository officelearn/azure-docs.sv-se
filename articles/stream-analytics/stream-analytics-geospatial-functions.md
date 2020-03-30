---
title: Introduktion till geospatiala Azure Stream Analytics-funktioner
description: I den här artikeln beskrivs geospatiala funktioner som används i Azure Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f47f34b60c858bb9a0feafd25176e4a811046630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426227"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introduktion till geospatiala stream analytics-funktioner

Geospatiala funktioner i Azure Stream Analytics möjliggör realtidsanalys på strömmande geospatiala data. Med bara några få rader kod kan du utveckla en lösning för produktionskvalitet för komplexa scenarier. 

Exempel på scenarier som kan dra nytta av geospatiala funktioner är:

* Samåkning
* Förvaltning av flottan
* Tillgångar
* Geo-stängsel
* Telefonspårning över cellwebbplatser

Stream Analytics frågespråk har sju inbyggda geospatiala funktioner: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS**och **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

Funktionen `CreateLineString` accepterar punkter och returnerar en GeoJSON LineString, som kan ritas som en linje på en karta. Du måste ha minst två punkter för att skapa en LineString. LineString-punkterna ansluts i ordning.

Följande fråga `CreateLineString` används för att skapa en LineString med tre punkter. Den första punkten skapas från strömmande indata, medan de andra två skapas manuellt.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Exempel på indata  
  
|Latitude|Longitud|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exempel på utdata  

 {"typ": "LineString", "koordinater": [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"typ": "LineString", "koordinater": [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

Mer information finns i [createlinestring-referensen.](https://docs.microsoft.com/stream-analytics-query/createlinestring)

## <a name="createpoint"></a>CreatePoint

Funktionen `CreatePoint` accepterar en latitud och longitud och returnerar en GeoJSON-punkt, som kan ritas på en karta. Dina breddgrader och longituder måste vara en **flytande** datatyp.

Följande exempelfråga `CreatePoint` använder för att skapa en punkt med hjälp av latitud och longituder från strömmande indata.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Exempel på indata  
  
|Latitude|Longitud|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exempel på utdata
  
 {"typ" : "Point", "koordinater": [-10.2, 3.0]}  
  
 {"typ" : "Point", "koordinater": [20.2321, -87.33]}  

Mer information finns i [CreatePoint-referensen.](https://docs.microsoft.com/stream-analytics-query/createpoint)

## <a name="createpolygon"></a>CreatePolygon

Funktionen `CreatePolygon` accepterar punkter och returnerar en GeoJSON-polygonpost. Ordningen på punkterna måste följa höger ring orientering, eller moturs. Tänk dig att gå från en punkt till en annan i den ordning de förklarades. Mitten av polygonen skulle vara till vänster hela tiden.

Följande exempelfråga `CreatePolygon` används för att skapa en polygon från tre punkter. De två första punkterna skapas manuellt och den sista punkten skapas från indata.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Exempel på indata  
  
|Latitude|Longitud|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exempel på utdata  

 {"typ": "Polygon", "koordinater": [[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"typ": "Polygon", "koordinater": [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

Mer information finns i [CreatePolygon-referensen.](https://docs.microsoft.com/stream-analytics-query/createpolygon)


## <a name="st_distance"></a>ST_DISTANCE
Funktionen `ST_DISTANCE` returnerar avståndet mellan två punkter i meter. 

Följande fråga `ST_DISTANCE` används för att generera en händelse när en bensinstation är mindre än 10 km från bilen.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Mer information finns i [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) referens.

## <a name="st_overlaps"></a>ST_OVERLAPS
Funktionen `ST_OVERLAPS` jämför två polygoner. Om polygonerna överlappar varandra returnerar funktionen en 1. Funktionen returnerar 0 om polygonerna inte överlappar varandra. 

Följande fråga `ST_OVERLAPS` används för att generera en händelse när en byggnad ligger inom en möjlig översvämningszon.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Följande exempelfråga genererar en händelse när en storm är på väg mot en bil.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Mer information finns i [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) referens.

## <a name="st_intersects"></a>ST_INTERSECTS
Funktionen `ST_INTERSECTS` jämför två LineString. Om LineString skär varandra returnerar funktionen 1. Funktionen returnerar 0 om LineString inte skär varandra.

Följande exempelfråga `ST_INTERSECTS` används för att avgöra om en asfalterad väg korsar en grusväg.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Exempel på indata  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type":"LineString", "koordinater": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "koordinater": [ [0,0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "koordinater": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "koordinater": [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Exempel på utdata  

 1  
  
 0  

Mer information finns i [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) referens.

## <a name="st_within"></a>ST_WITHIN
Funktionen `ST_WITHIN` avgör om en punkt eller polygon ligger inom en polygon. Om polygonen innehåller punkten eller polygonen returneras funktionen 1. Funktionen returneras 0 om punkten eller polygonen inte finns i den deklarerade polygonen.

Följande exempelfråga `ST_WITHIN` används för att avgöra om leveransmålpunkten ligger inom det angivna distributionsstället polygon.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Exempel på indata  
  
|leveransDestination|Lager|  
|-------------------------|---------------|  
|{"type":"Point", "koordinater": [76.6, 10.1]}|{"type":"Polygon", "koordinater": [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "koordinater": [15.0, 15.0]}|{"type":"Polygon", "koordinater": [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Exempel på utdata  

 0  
  
 1  

Mer information finns i [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) referens.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
