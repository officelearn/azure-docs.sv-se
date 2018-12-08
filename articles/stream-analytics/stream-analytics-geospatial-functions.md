---
title: Introduktion till Azure Stream Analytics geospatiala funktioner
description: Den här artikeln beskriver geospatiala funktioner som används i Azure Stream Analytics-jobb.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: ad789a597da759b9a2d58138c7ed441389a12adb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103140"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introduktion till Stream Analytics geospatiala funktioner

Geospatiala funktioner i Azure Stream Analytics aktivera analys i realtid på strömmande geospatiala data. Med bara några rader med kod kan du utveckla en lösning för produktion i företagsklass för avancerade scenarier. 

Exempel på scenarier som kan dra nytta av geospatiala funktioner:

* Bilpooler
* Hantering av vagnpark
* Tillgångar
* Geografiska avgränsningar
* Phone spårning på cell platser

Stream Analytics-frågespråket har sju inbyggda geospatiala funktioner: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE** , **ST_OVERLAPS**, **ST_INTERSECTS**, och **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

Den `CreateLineString` funktionen accepterar punkter och returnerar en GeoJSON LineString som kan ritas som en linje på en karta. Du måste ha minst två punkter att skapa en LineString. LineString-punkter ansluts i ordning.

Följande fråga använder `CreateLineString` att skapa en LineString med tre punkter. Den första punkten skapas från strömmande inkommande data medan de andra två skapas manuellt.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Inkommande exempel  
  
|Latitud|Longitud|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exempel på utdata  

 {”type”: ”LineString”, ”coordinates”: [[-10.2, 3.0], [10.0, 10.0], [10,5, 10,5]]}

 {”type”: ”LineString”, ”coordinates”: [[20.2321,-87.33], [10.0, 10.0], [10,5, 10,5]]}

Mer information finns i [CreateLineString](https://msdn.microsoft.com/azure/stream-analytics/reference/createlinestring) referens.

## <a name="createpoint"></a>CreatePoint

Den `CreatePoint` funktionen accepterar en latitud och longitud och returnerar en GeoJSON-plats som kan skrivas ut på en karta. Din Latitude och longitudes måste vara en **flyttal** datatype.

Följande exempelfråga använder `CreatePoint` att skapa en återställningspunkt med hjälp av Latitude och longitudes från strömmande indata.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Inkommande exempel  
  
|Latitud|Longitud|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exempel på utdata
  
 {”type”: ”Point”, ”coordinates”: [-10.2, 3.0]}  
  
 {”type”: ”Point”, ”coordinates”: [20.2321,-87.33]}  

Mer information finns i [CreatePoint](https://msdn.microsoft.com/azure/stream-analytics/reference/createpoint) referens.

## <a name="createpolygon"></a>CreatePolygon

Den `CreatePolygon` funktionen accepterar punkter och returnerar en post för GeoJSON-polygon. Ordningen på punkter måste följa högra ring orientering eller motsols. Tänk dig att gå från en plats till en annan i den ordning som de deklarerades. Polygonens mittpunkt är till vänster hela tiden.

Följande exempelfråga använder `CreatePolygon` att skapa en polygon från tre punkter. De första två punkterna skapas manuellt och den sista punkten skapas från indata.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Inkommande exempel  
  
|Latitud|Longitud|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exempel på utdata  

 {”type”: ”Polygon”, ”coordinates”: [[[-10.2, 3.0], [10.0, 10.0], [10,5, 10,5], [-10.2, 3.0]]]}
 
 {”type”: ”Polygon”, ”coordinates”: [[[20.2321,-87.33], [10.0, 10.0], [10,5, 10,5], [20.2321,-87.33]]]}

Mer information finns i [CreatePolygon](https://msdn.microsoft.com/azure/stream-analytics/reference/createpolygon) referens.


## <a name="stdistance"></a>ST_DISTANCE
Den `ST_DISTANCE` funktionen returnerar avståndet mellan två platser i mätare. 

Följande fråga använder `ST_DISTANCE` att generera en händelse när en Bensinstation är mindre än 10 km från bilen.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Mer information finns i [ST_DISTANCE](https://msdn.microsoft.com/azure/stream-analytics/reference/st-distance) referens.

## <a name="stoverlaps"></a>ST_OVERLAPS
Den `ST_OVERLAPS` funktionen Jämför två polygoner. Om polygonerna överlappar returnerar funktionen en 1. Funktionen returnerar 0 om polygonerna inte överlappar varandra. 

Följande fråga använder `ST_OVERLAPS` att generera en händelse när en byggnad ligger inom ett möjligt överbelasta zon.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Följande exempelfråga genererar en händelse när en storm är på väg för en bil.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Mer information finns i [ST_OVERLAPS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-overlaps) referens.

## <a name="stintersects"></a>ST_INTERSECTS
Den `ST_INTERSECTS` funktionen Jämför två LineString. Om LineString intersect, returnerar funktionen 1. Funktionen returnerar 0 om LineString inte överlappar varandra.

Följande exempelfråga använder `ST_INTERSECTS` att avgöra om asfalterad väg korsar en smuts väg.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Inkommande exempel  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{”type”: ”LineString”, ”coordinates”: [[-10.0, 0.0], [0.0, 0.0], [10.0, 0.0]]}|{”type”: ”LineString”, ”coordinates”: [[0.0, 10.0], [0.0, 0.0], [0.0,-10.0]]}|  
|{”type”: ”LineString”, ”coordinates”: [[-10.0, 0.0], [0.0, 0.0], [10.0, 0.0]]}|{”type”: ”LineString”, ”coordinates”: [[-10.0, 10.0], [0.0, 10.0], [10.0, 10.0]]}|  
  
### <a name="output-example"></a>Exempel på utdata  

 1  
  
 0  

Mer information finns i [ST_INTERSECTS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-intersects) referens.

## <a name="stwithin"></a>ST_WITHIN
Den `ST_WITHIN` funktionen avgör om en polygon eller är i en polygon. Om polygonen innehåller där eller polygon, returnerar funktionen 1. Funktionen returnerar 0 om inte är belägen inom deklarerade polygon där eller polygon.

Följande exempelfråga använder `ST_WITHIN` om målplats leverans ligger inom viss warehouse polygonen.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Inkommande exempel  
  
|deliveryDestination|datalager|  
|-------------------------|---------------|  
|{”type”: ”Point”, ”coordinates”: [76.6, 10.1]}|{”type”: ”Polygon”, ”coordinates”: [[0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0]]}|  
|{”type”: ”Point”, ”coordinates”: [15.0, 15.0]}|{”type”: ”Polygon”, ”coordinates”: [[10.0, 10.0], [20,0, 10.0], [20.0, 20.0], [10.0, 20,0], [10.0, 10.0]]}|  
  
### <a name="output-example"></a>Exempel på utdata  

 0  
  
 1  

Mer information finns i [ST_WITHIN](https://msdn.microsoft.com/azure/stream-analytics/reference/st-within) referens.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)