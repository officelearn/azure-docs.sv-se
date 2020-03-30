---
title: Information om dataformat som stöds | Microsoft Azure Maps
description: Lär dig hur avgränsade rumsliga data tolkas i den rumsliga IO-modulen.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334088"
---
# <a name="supported-data-format-details"></a>Information om dataformat som stöds

Den här artikeln innehåller detaljer om läs- och skrivstöd för alla XML-taggar och geometrityper för välkänd text. Den beskriver också hur avgränsade rumsliga data tolkas i den rumsliga IO-modulen.

## <a name="supported-xml-namespaces"></a>XML-namnområden som stöds

Den rumsliga IO-modulen stöder XML-taggar från följande namnområden.

| Namnområdesprefix | Namnområde URI   | Anteckningar                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Skrivskyddat stöd i GeoRSS-filer.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Skrivskyddat stöd i GeoRSS-filer.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Skrivskyddat stöd i GPX-filer. Tolkar och använder DisplayColor. Alla andra egenskaper som läggs till i formmetadata. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Stöds i GPX-filer. Använder linjefärg. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Skrivskyddad. GeoRSS skriver med Atom-format.              |

## <a name="supported-xml-elements"></a>XML-element som stöds

Den rumsliga IO-modulen stöder följande XML-element. Alla XML-taggar som inte stöds konverteras till ett JSON-objekt. Sedan läggs varje tagg till som `properties` en egenskap i fältet för den överordnade formen eller lagret.

### <a name="kml-elements"></a>KML-element

Den rumsliga IO-modulen stöder följande KML-element.

| Elementnamn         | Läsa    | Skriva   | Anteckningar                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | Partiell | ja     | Objektet tolkas men används inte för positioneringsform.                                                                    |
| `AddressDetails`     | Partiell | nej      | Objektet tolkas men används inte för positioneringsform.                                                                    |
| `atom:author`        | ja     | ja     |                                                                                                                            |
| `atom:link`          | ja     | ja     |                                                                                                                            |
| `atom:name`          | ja     | ja     |                                                                                                                            |
| `BalloonStyle`       | Partiell | Partiell | `displayMode`stöds inte. Konverteras till `PopupTemplate`en . Om du vill `popupTemplate` skriva lägger du till en egenskap som en egenskap för den funktion som du vill skriva den för. |
| `begin`              | ja     | ja     |                                                                                                                            |
| `color`              | ja     | ja     | Inkluderar `#AABBGGRR` `#BBGGRR`och . Tolkas i en CSS-färgsträng                                                           |
| `colorMode`          | ja     | nej      |                                                                                                                            |
| `coordinates`        | ja     | ja     |                                                                                                                            |
| `Data`               | ja     | ja     |                                                                                                                            |
| `description`        | ja     | ja     |                                                                                                                            |
| `displayName`        | ja     | ja     |                                                                                                                            |
| `Document`           | ja     | ja     |                                                                                                                            |
| `drawOrder`          | Partiell | nej      | Läs för marköverlägg och används för att sortera dem. 
| `east`               | ja     | ja     |                                                                                                                            |
| `end`                | ja     | ja     |                                                                                                                            |
| `ExtendedData`       | ja     | ja     | Stöder `Data`otypade `SimpleData` `Schema`eller , och entitetsersättning av formuläret `$[dataName]`.                      |
| `extrude`            | Partiell | Partiell | Stöds endast för polygoner. MultiGeometry som har polygoner av olika höjder kommer att delas ut i enskilda funktioner. Linjeformat stöds inte. Polygoner med en höjd av 0 kommer att återges som en platt polygon. Vid läsning kommer höjden på den första koordinaten i den yttre ringen att läggas till som en höjdegenskap för polygonen. Sedan används höjden på den första koordinaten för att återge polygonen på kartan. |
| `fill`               | ja     | ja     |                                                                                                                            |
| `Folder`             | ja     | ja     |                                                                                                                            |
| `GroundOverlay`      | ja     | ja     | `color`stöds inte                                                                                                   |
| `heading`            | Partiell | nej      | Tolkas men inte återges av `SimpleDataLayer`. Skriv endast om data lagras i formens egenskap.                 |
| `hotSpot`            | ja     | Partiell | Skriv endast om data lagras i formens egenskap. Enheter matas endast ut som "pixlar".                         |
| `href`               | ja     | ja     |                                                                                                                            |
| `Icon`               | Partiell | Partiell | Tolkas men inte återges av `SimpleDataLayer`. Skriv bara ikonengenskapen för formen om den innehåller en URI-data. Endast `href` stöds. |
| `IconStyle`          | Partiell | Partiell | `icon`, `heading` `colorMode`, `hotspots` och värden tolkas, men de återges inte av`SimpleDataLayer`         |
| `innerBoundaryIs`    | ja     | ja     |                                                                                                                            |
| `kml`                | ja     | ja     |                                                                                                                            |
| `LabelStyle`         | nej      | nej      |                                                                                                                            |
| `LatLonBox`          | ja     | ja     |                                                                                                                            |
| `gx:LatLonQuad`      | ja     | ja     |                                                                                                                            |
| `LinearRing`         | ja     | ja     |                                                                                                                            |
| `LineString`         | ja     | ja     |                                                                                                                            |
| `LineStyle`          | ja     | ja     | `colorMode`stöds inte.                                                                                         |
| `Link`               | ja     | nej      | Endast `href` egenskapen stöds för nätverkslänkar.                                                                   |
| `MultiGeometry`      | Partiell | Partiell | Kan delas ut i enskilda funktioner när de läss.                                                                     |
| `name`               | ja     | ja     |                                                                                                                            |
| `NetworkLink`        | ja     | nej      | Länkar måste finnas på samma domän som dokumentet.                                                                  |
| `NetworkLinkControl` | nej      | nej      |                                                                                                                            |
| `north`              | ja     | ja     |                                                                                                                            |
| `open`               | ja     | ja     |                                                                                                                            |
| `outerBoundaryIs`    | ja     | ja     |                                                                                                                            |
| `outline`            | ja     | ja     |                                                                                                                            |
| `overlayXY`          | nej      | nej      |                                                                                                                            |
| `Pair`               | Partiell | nej      | Endast `normal` formatet i `StyleMap` en stöds. `highlight`stöds inte.                                   |
| `phoneNumber`        | ja     | ja     |                                                                                                                            |
| `PhotoOverlay`       | nej      | nej      |                                                                                                                            |
| `Placemark`          | ja     | ja     |                                                                                                                            |
| `Point`              | ja     | ja     |                                                                                                                            |
| `Polygon`            | ja     | ja     |                                                                                                                            |
| `PolyStyle`          | ja     | ja     |                                                                                                                            |
| `Region`             | Partiell | Partiell | `LatLongBox`stöds på dokumentnivå.                                                                      |
| `rotation`           | nej      | nej      |                                                                                                                            |
| `rotationXY`         | nej      | nej      |                                                                                                                            |
| `scale`              | nej      | nej      |                                                                                                                            |
| `Schema`             | ja     | ja     |                                                                                                                            |
| `SchemaData`         | ja     | ja     |                                                                                                                            |
| `schemaUrl`          | Partiell | ja     | Stöder inte inläsning av format från externa dokument som inte ingår i en KMZ.                             |
| `ScreenOverlay`      | nej      | nej      |                                                                                                                            |
| `screenXY`           | nej      | nej      |                                                                                                                            |
| `SimpleData`         | ja     | ja     |                                                                                                                            |
| `SimpleField`        | ja     | ja     |                                                                                                                            |
| `size`               | nej      | nej      |                                                                                                                            |
| `Snippet`            | Partiell | Partiell | `maxLines`attributet ignoreras.                                                                                  |
| `south`              | ja     | ja     |                                                                                                                            |
| `Style`              | ja     | ja     |                                                                                                                            |
| `StyleMap`           | Partiell | nej      | Endast den normala `StyleMap` stilen i en stöds.                                                                        |
| `styleUrl`           | Partiell | ja     | Webbadresser med externt format stöds inte.                                                                         |
| `text`               | ja     | ja     | Byte `$[geDirections]` av stöds inte                                                                          |
| `textColor`          | ja     | ja     |                                                                                                                            |
| `TimeSpan`           | ja     | ja     |                                                                                                                            |
| `TimeStamp`          | ja     | ja     |                                                                                                                            |
| `value`              | ja     | ja     |                                                                                                                            |
| `viewRefreshMode`    | Partiell | nej      |  Om du pekar på en WMS-tjänst stöds endast `onStop` marköverlägg. Kommer `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` att lägga till webbadressen och uppdatera när kartan flyttas.  |
| `visibility`         | ja     | ja     |                                                                                                                            |
| `west`               | ja     | ja     |                                                                                                                            |
| `when`               | ja     | ja     |                                                                                                                            |
| `width`              | ja     | ja     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS-element

Den rumsliga IO-modulen stöder följande GeoRSS-element.

| Elementnamn             | Läsa    | Skriva | Anteckningar                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | ja     | ja   |                                                                                                |
| `atom:category`          | ja     | ja   |                                                                                                |
| `atom:content`           | ja     | ja   |                                                                                                |
| `atom:contributor`       | ja     | ja   |                                                                                                |
| `atom:email`             | ja     | ja   |                                                                                                |
| `atom:entry`             | ja     | ja   |                                                                                                |
| `atom:feed`              | ja     | ja   |                                                                                                |
| `atom:icon`              | ja     | ja   |                                                                                                |
| `atom:id`                | ja     | ja   |                                                                                                |
| `atom:link`              | ja     | ja   |                                                                                                |
| `atom:logo`              | ja     | ja   |                                                                                                |
| `atom:name`              | ja     | ja   |                                                                                                |
| `atom:published`         | ja     | ja   |                                                                                                |
| `atom:rights`            | ja     | ja   |                                                                                                |
| `atom:source`            | ja     | ja   |                                                                                                |
| `atom:subtitle`          | ja     | ja   |                                                                                                |
| `atom:summary`           | ja     | ja   |                                                                                                |
| `atom:title`             | ja     | ja   |                                                                                                |
| `atom:updated`           | ja     | ja   |                                                                                                |
| `atom:uri`               | ja     | ja   |                                                                                                |
| `geo:lat`                | ja     | nej    | Skrivet `georss:point`som en .                                                                   |
| `geo:lon`                | ja     | nej    | Skrivet `georss:point`som en .                                                                   |
| `geo:long`               | ja     | nej    | Skrivet `georss:point`som en .                                                                   |
| `georss:box`             | ja     | nej    | Läs som en polygon `subType` och ges en egenskap av "Rektangel"                                |
| `georss:circle`          | ja     | ja   |                                                                                                |
| `georss:elev`            | ja     | ja   |                                                                                                |
| `georss:featurename`     | ja     | ja   |                                                                                                |
| `georss:featuretypetag`  | ja     | ja   |                                                                                                |
| `georss:floor`           | ja     | ja   |                                                                                                |
| `georss:line`            | ja     | ja   |                                                                                                |
| `georss:point`           | ja     | ja   |                                                                                                |
| `georss:polygon`         | ja     | ja   |                                                                                                |
| `georss:radius`          | ja     | ja   |                                                                                                |
| `georss:relationshiptag` | ja     | ja   |                                                                                                |
| `georss:where`           | ja     | ja   |                                                                                                |
| `geourl:latitude`        | ja     | nej    | Skrivet `georss:point`som en .                                                                   |
| `geourl:longitude`       | ja     | nej    | Skrivet `georss:point`som en .                                                                   |
| `position`               | ja     | nej    | Vissa XML-flöden kommer att radbrytas GML med `georss:where` en positionstagg i stället för att slå in den med en tagg. Kommer att läsa den här `georss:where` taggen, men kommer att skriva med hjälp av en tagg. |
| `rss`                    | ja     | nej    | GeoRSS skrivet i ATOM-format.                                                                 |
| `rss:author`             | ja     | Partiell | Skrivet `atom:author`som en .                                                                 |
| `rss:category`           | ja     | Partiell | Skrivet `atom:category`som en .                                                               |
| `rss:channel`            | ja     | nej    |                                                                                                |
| `rss:cloud`              | ja     | nej    |                                                                                                |
| `rss:comments`           | ja     | nej    |                                                                                                |
| `rss:copyright`          | ja     | Partiell | Skrivet `atom:rights` som en om-form `rights` `properties` har inte redan en egenskap.       |
| `rss:description`        | ja     | Partiell | Skrivet `atom:content` som en om-form `content` `properties` har inte redan en egenskap.      |
| `rss:docs`               | ja     | nej    |                                                                                                |
| `rss:enclosure`          | ja     | nej    |                                                                                                |
| `rss:generator`          | ja     | nej    |                                                                                                |
| `rss:guid`               | ja     | Partiell | Skrivet `atom:id` som en om-form `id` `properties` har inte redan en egenskap.         |
| `rss:image`              | ja     | Partiell | Skrivet `atom:logo` som en om-form `logo` `properties` har inte redan en egenskap.      |
| `rss:item`               | ja     | Partiell | Skrivet `atom:entry`som en .                                                                  |
| `rss:language`           | ja     | nej    |                                                                                                |
| `rss:lastBuildDate`      | ja     | Partiell | Skrivet `atom:updated` som en om-form `updated` `properties` har inte redan en egenskap.     |
| `rss:link`               | ja     | Partiell | Skrivet `atom:link`som en .                                                                   |
| `rss:managingEditor`     | ja     | Partiell | Skrivet `atom:contributor`som en .                                                            |
| `rss:pubDate`            | ja     | Partiell | Skrivet `atom:published` som en om-form `published` `properties` har inte redan en egenskap.  |
| `rss:rating`             | ja     | nej    |                                                                                                |
| `rss:skipDays`           | ja     | nej    |                                                                                                |
| `rss:skipHours`          | ja     | nej    |                                                                                                |
| `rss:source`             | ja     | Partiell | Skrivet `atom:source` som `atom:link`en innehållande en .                                       |
| `rss:textInput`          | ja     | nej    |                                                                                                |
| `rss:title`              | ja     | Partiell | Skrivet `atom:title`som en .                                                                  |
| `rss:ttl`                | ja     | nej    |                                                                                                |
| `rss:webMaster`          | ja     | nej    |                                                                                                |

### <a name="gml-elements"></a>GML-element

Den rumsliga IO-modulen stöder följande GML-element. 

| Elementnamn            | Läsa | Skriva | Anteckningar                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | ja  | nej    | Skrivet `gml:posList`som .                                                              |
| `gml:curveMember`       | ja  | nej    |                                                                                        |
| `gml:curveMembers`      | ja  | nej    |                                                                                        |
| `gml:Box`               | ja  | nej    | Skrivet `gml:Envelope`som .                                                             |
| `gml:description`       | ja  | ja   |                                                                                        |
| `gml:Envelope`          | ja  | ja   |                                                                                        |
| `gml:exterior`          | ja  | ja   |                                                                                        |
| `gml:Feature`           | ja  | nej    | Skriven som en form.                                                                    |
| `gml:FeatureCollection` | ja  | nej    | Skriven som en geometrisamling.                                                      |
| `gml:featureMember`     | ja  | nej    | Skriven som en geometrisamling.                                                      |
| `gml:geometry`          | ja  | nej    | Skriven som en form.                                                                    |
| `gml:geometryMember`    | ja  | ja   |                                                                                        |
| `gml:geometryMembers`   | ja  | ja   |                                                                                        |
| `gml:identifier`        | ja  | ja   |                                                                                        |
| `gml:innerBoundaryIs`   | ja  | nej    | Skrivet `gml.interior`med .                                                          |
| `gml:interior`          | ja  | ja   |                                                                                        |
| `gml:LinearRing`        | ja  | ja   |                                                                                        |
| `gml:LineString`        | ja  | ja   |                                                                                        |
| `gml:lineStringMember`  | ja  | ja   |                                                                                        |
| `gml:lineStringMembers` | ja  | nej    |                                                                                        |
| `gml:MultiCurve`        | ja  | nej    | Läser `gml:LineString` bara medlemmar. Skrivet som`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | Partiell  | Partiell   | Läs endast som en FeatureCollection.                                              |
| `gml:MultiLineString`   | ja  | ja   |                                                                                        |
| `gml:MultiPoint`        | ja  | ja   |                                                                                        |
| `gml:MultiPolygon`      | ja  | ja   |                                                                                        |
| `gml:MultiSurface`      | ja  | nej    | Läser `gml:Polygon` bara medlemmar. Skrivet som`gml.MultiPolygon`                        |
| `gml:name`              | ja  | ja   |                                                                                        |
| `gml:outerBoundaryIs`   | ja  | nej    | Skrivet `gml.exterior`med .                                                          |
| `gml:Point`             | ja  | ja   |                                                                                        |
| `gml:pointMember`       | ja  | ja   |                                                                                        |
| `gml:pointMembers`      | ja  | nej    |                                                                                        |
| `gml:Polygon`           | ja  | ja   |                                                                                        |
| `gml:polygonMember`     | ja  | ja   |                                                                                        |
| `gml:polygonMembers`    | ja  | nej    |                                                                                        |
| `gml:pos`               | ja  | ja   |                                                                                        |
| `gml:posList`           | ja  | ja   |                                                                                        |
| `gml:surfaceMember`     | ja  | ja   |                                                                                        |

#### <a name="additional-notes"></a>ytterligare anteckningar

- Medlemselement kommer att sökas efter en geometri som kan begravas i underordnade element. Den här sökåtgärden är nödvändig eftersom många XML-format som sträcker sig från GML kanske inte placerar en geometri som ett direkt underordnat medlemselement.
- `srsName`stöds delvis för WGS84-koordinater och följande koder:[EPSG:4326](https://epsg.io/4326)) och web Mercator ([EPSG:3857](https://epsg.io/3857) eller någon av dess alternativa koder. Alla andra koordinatsystem tolkas som WGS84 som de är.
- Om inte anges när du läser en XML-feed bestäms axelordningen baserat på tips i XML-feeden. En preferens ges för axelordningen "latitud, longitud".
- Om inte ett anpassat GML-namnområde anges för egenskaperna när du skriver till en GML-fil, läggs ytterligare egenskapsinformation inte till.

### <a name="gpx-elements"></a>GPX-element

Den rumsliga IO-modulen stöder följande GPX-element.

| Elementnamn             | Läsa    | Skriva   | Anteckningar                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | ja     | ja     |                                                                                             |
| `gpx:author`             | ja     | ja     |                                                                                             |
| `gpx:bounds`             | ja     | ja     | Konverteras till en PlatsRect när den läss.                                                    |
| `gpx:cmt`                | ja     | ja     |                                                                                             |
| `gpx:copyright`          | ja     | ja     |                                                                                             |
| `gpx:desc`               | ja     | ja     | Kopieras till en beskrivningsegenskap när den läss för att justeras med andra XML-format.               |
| `gpx:dgpsid`             | ja     | ja     |                                                                                             |
| `gpx:ele`                | ja     | ja     |                                                                                             |
| `gpx:extensions`         | Partiell | Partiell | När du läser extraheras formatinformation. Alla andra tillägg kommer att förenklas till ett enkelt JSON-objekt. Endast formformatinformation skrivs. |
| `gpx:geoidheight`        | ja     | ja     |                                                                                             |
| `gpx:gpx`                | ja     | ja     |                                                                                             |
| `gpx:hdop`               | ja     | ja     |                                                                                             |
| `gpx:link`               | ja     | ja     |                                                                                             |
| `gpx:magvar`             | ja     | ja     |                                                                                             |
| `gpx:metadata`           | ja     | ja     |                                                                                             |
| `gpx:name`               | ja     | ja     |                                                                                             |
| `gpx:pdop`               | ja     | ja     |                                                                                             |
| `gpx:rte`                | ja     | ja     |                                                                                             |
| `gpx:rtept`              | ja     | ja     |                                                                                             |
| `gpx:sat`                | ja     | ja     |                                                                                             |
| `gpx:src`                | ja     | ja     |                                                                                             |
| `gpx:sym`                | ja     | ja     | Värdet fångas in, men det används inte för att ändra pushpin-ikonen.                               |
| `gpx:text`               | ja     | ja     |                                                                                             |
| `gpx:time`               | ja     | ja     |                                                                                             |
| `gpx:trk`                | ja     | ja     |                                                                                             |
| `gpx:trkpt`              | ja     | ja     |                                                                                             |
| `gpx:trkseg`             | ja     | ja     |                                                                                             |
| `gpx:type`               | ja     | ja     |                                                                                             |
| `gpx:vdop`               | ja     | ja     |                                                                                             |
| `gpx:wpt`                | ja     | ja     |                                                                                             |
| `gpx_style:color`        | ja     | ja     |                                                                                             |
| `gpx_style:line`         | Partiell | Partiell | `color`, `opacity` `width`, `lineCap` stöds.                                           |
| `gpx_style:opacity`      | ja     | ja     |                                                                                             |
| `gpx_style:width`        | ja     | ja     |                                                                                             |
| `gpxx:DisplayColor`      | ja     | nej      | Används för att ange färgen på en form. När du `gpx_style:line` skriver används färgen i stället.  |
| `gpxx:RouteExtension`    | Partiell | nej      | Alla egenskaper läss in i `properties`. Endast `DisplayColor` används.                     |
| `gpxx:TrackExtension`    | Partiell | nej      | Alla egenskaper läss in i `properties`. Endast `DisplayColor` används.                     |
| `gpxx:WaypointExtension` | Partiell | nej      | Alla egenskaper läss in i `properties`. Endast `DisplayColor` används.                     |
| `gpx:keywords`           | ja     | ja     |                                                                                             |
| `gpx:fix`                | ja     | ja     |                                                                                             |

#### <a name="additional-notes"></a>ytterligare anteckningar

När du skriver;

- MultiPoints delas upp i enskilda waypoints.
- Polygoner och MultiPolygons kommer att skrivas som spår. 
  
## <a name="supported-well-known-text-geometry-types"></a>Geometrityper för välkänd text som stöds

| Geometrityp | Läsa | Skriva |
|--------------|:----:|:-----:|
| Punkt | x | x |
| PUNKT Z | x | x | 
| PUNKT M | x | x<sup>[2]</sup> |
| PUNKT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINJERNAS RIKTNING | x | x |
| LINJERNA Z | x | x | 
| LINJERNA M | x | x<sup>[2]</sup> |
| LINJERNA ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Polygon | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipoint | x | x |
| FLERAPUNKT Z | x | x | 
| FLERAPUNKT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| ZM med flera strecklinjer | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Endast Z-parametern fångas in och läggs till som ett tredje värde i positionsvärdet.

\[2\] M-parametern fångas inte.

## <a name="delimited-spatial-data-support"></a>Stöd för avgränsade rumsliga data

Avgränsade rumsliga data, till exempel kommaavgränsade värdefiler (CSV), har ofta kolumner som innehåller rumsliga data. Det kan till exempel finnas kolumner som innehåller latitud- och longitudinformation. I formatet Välkänd text kan det finnas en kolumn som innehåller rumsliga geometridata.

### <a name="spatial-data-column-detection"></a>Identifiering av kolumn i spatialdata

När du läser en avgränsad fil som innehåller rumsliga data analyseras huvudet för att avgöra vilka kolumner som innehåller platsfält. Om huvudet innehåller textinformation används cellvärdena till lämplig typ. Om inget huvud anges analyseras den första raden och används för att generera ett huvud. När du analyserar den första raden körs en check för att matcha kolumnnamn med följande namn på ett skiftlägesokänsligt sätt. Namnordningen är prioriteten, om det finns två eller flera namn i en fil.

#### <a name="latitude"></a>Latitud

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Longitud

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Höjd

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geografi

Den första raden med data kommer att genomsökas efter strängar som är i välkänt textformat. 

### <a name="delimited-data-column-types"></a>Avgränsade datakolumntyper

När du skannar rubrikraden extraheras all typinformation som finns i kolumnnamnet och används för att casta cellerna i den kolumnen. Här är ett exempel på ett kolumnnamn som har ett textvärde: "ColumnName (typeName)". Följande skiftlägesokänsliga typnamn stöds:

#### <a name="numbers"></a>Tal

- edm.int64
- int
- long
- edm.double (edm.double)
- float
- double
- nummer

#### <a name="booleans"></a>Booleans

- edm.boolean
- bool
- boolean

#### <a name="dates"></a>Datum

- edm.datetime
- date
- datetime

#### <a name="geography"></a>Geografi

- edm.geografi
- geography

#### <a name="strings"></a>Strängar

- edm.string (edm.string)
- varchar
- text
- sträng

Om ingen typinformation kan extraheras från huvudet och det dynamiska skrivalternativet är aktiverat vid läsning, analyseras varje cell individuellt för att avgöra vilken datatyp den är bäst lämpad att casta som.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Läsa och skriva rumsliga data](spatial-io-read-write-spatial-data.md)
