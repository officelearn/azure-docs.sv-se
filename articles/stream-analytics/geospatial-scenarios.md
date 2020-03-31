---
title: Geofencing och geospatial aggregering med Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder Azure Stream Analytics för geofencing och geospatial aggregering.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 5a3aa3786469c3df37b53cb82bdd396871689297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443646"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Geofencing och geospatial aggregering scenarier med Azure Stream Analytics

Med inbyggda geospatiala funktioner kan du använda Azure Stream Analytics för att skapa program för scenarier som vagnparkshantering, samåkning, anslutna bilar och tillgångsspårning.

## <a name="geofencing"></a>Geofencing (geofencing)

Azure Stream Analytics stöder geofencing-beräkningar i realtid i realtid i molnet och i IoT Edge-körningen.

### <a name="geofencing-scenario"></a>Geofencing-scenario

Ett tillverkningsföretag måste spåra tillgångar på sina byggnader. De utrustade varje enhet med en GPS och vill ta emot meddelanden om en enhet lämnar ett visst område.

Referensdata som används i det här exemplet har geofence-informationen för de byggnader och de enheter som är tillåtna i var och en av byggnaderna. Kom ihåg att referensdata antingen kan vara statiska eller långsamma. Statiska referensdata används för det här scenariot. En dataström avger kontinuerligt enhets-ID:t och dess aktuella position.

### <a name="define-geofences-in-reference-data"></a>Definiera geofenser i referensdata

En geofence kan definieras med hjälp av ett GeoJSON-objekt. För jobb med kompatibilitet version 1.2 och högre, geofences kan också `NVARCHAR(MAX)`definieras med välkänd text (WKT) som . WKT är en OGC-standard (Open Geospatial Consortium) som används för att representera rumsliga data i ett textformat.

De inbyggda geospatiala funktionerna kan använda definierade geofences för att ta reda på om ett element är i eller ut ur en specifik geofence polygon.

Följande tabell är ett exempel på geofence referensdata som kan lagras i Azure blob storage eller en Azure SQL-tabell. Varje plats representeras av en geospatial polygon och varje enhet är associerad med ett tillåtet plats-ID.

|SiteID (På plats)|SiteName|Geofence (olika)|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Byggnad 41"|"POLYGON(-122.13373579222017 47.63782998329432,-122.13373042778369 47,637634793257305,-122,13346757130023 47,63764 2022530954,-122.13348902897235 47,637508280806806,-122.13361777500506 47,637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Redmond Byggnad 40"|"POLYGON((-122.1336154507967 47.6366745947009,-122.13361008637867 47,636483015064535,-122,13349206918201 47,636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47,63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Redmond Byggnad 22"|"POLYGON((-122.13611660248233 47,63758544698554,-122.13635263687564 47,6374083293018,-122,13622389084293 47,63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47,63692757827657,-122,13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|"C"|

### <a name="generate-alerts-with-geofence"></a>Generera aviseringar med geofence

Enheter kan avge sitt ID och sin `DeviceStreamInput`plats varje minut via en ström som kallas . Följande tabell är en indataström.

|Deviceid|GeoPosition (geoposition)|
|--------|-----------|
|"A"|"POINT(-122.13292341559497 47.636318374032726)"|
|"B"|"POINT(-122.13338475554553 47.63743531308874)"|
|"C"|"POINT(-122.13354001095752 47.63627622505007)"|

Du kan skriva en fråga som ansluter enhetsströmmen med geofence-referensdata och genererar en avisering varje gång en enhet ligger utanför en tillåten byggnad.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Följande bild representerar geofences. Du kan se var enheterna är i enlighet med datainmatningen för dataström.

![Bygga geofences](./media/geospatial-scenarios/building-geofences.png)

Enhet "C" finns inuti bygg-ID 2, vilket inte är tillåtet enligt referensdata. Den här enheten bör placeras inuti bygg-ID 3. Om du kör det här jobbet skapas en avisering för den här specifika överträdelsen.

### <a name="site-with-multiple-allowed-devices"></a>Webbplats med flera tillåtna enheter

Om en webbplats tillåter flera enheter kan en matris `AllowedDeviceID` med enhets-ID definieras i `WHERE` och en användardefinierad funktion kan användas på satsen för att kontrollera om dataenhetens ID matchar något enhets-ID i den listan. Mer information finns i [Javascript UDF-självstudien](stream-analytics-javascript-user-defined-functions.md) för molnjobb och [C# UDF-självstudien](stream-analytics-edge-csharp-udf.md) för kantjobb.

## <a name="geospatial-aggregation"></a>Geospatial aggregering

Azure Stream Analytics stöder geospatial aggregering i realtid i realtid i molnet och i IoT Edge-körningen.

### <a name="geospatial-aggregation-scenario"></a>Geospatial aggregering scenario

Ett taxiföretag vill bygga en realtidsapplikation för att guida sina taxichaufförer som söker skjuts mot de områden i städerna som för närvarande har högre efterfrågan.

Företaget lagrar logiska regioner av staden som hänvisar till data. Varje region definieras av ett RegionID, RegionName och Geofence.

### <a name="define-the-geofences"></a>Definiera geofences

Följande tabell är ett exempel på geofence referensdata som kan lagras i Azure blob storage eller en Azure SQL-tabell. Varje region representeras av en geospatial polygon, som används för att korrelera med begäranden som kommer från strömmande data.

Dessa polygoner är för hänvisar till endast och föreställer inte faktiska logiska eller läkarundersökningavskiljande.

|RegionID|RegionNamn|Geofence (olika)|
|--------|----------|--------|
|1|"SoHo"|"POLYGON((-74,00279525078275 40,72833625216264,-74,00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|"Chinatown"|"POLYGON((-73.99712367114876 40.71281582267133,-73.990107 40,71336881907936,-73,99023575839851 40,71452359088633,-73.98976368961189 40.71554823078944,-73.0199551434573982 40,717337246783735,-73,99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"POLYGON((-74.01091641815208 40.72583120006787,-74.01338405044578 40,71436586362705,-74,01370591552757 40,713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Aggregera data över ett tidsfönster

Följande tabell innehåller strömmande data för "rides".

|UserID|Frånlokalisering|ToLocation (ToLocation)|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"POINT(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.716439898624024)"|"POINT(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"POINT(-74.00741090068288 40.71615626755086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Följande fråga sammanfogar enhetsströmmen med geofence-referensdata och beräknar antalet begäranden per region i ett tidsfönster på 15 minuter varje minut.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Den här frågan matar ut ett antal begäranden varje minut under de senaste 15 minuterna av varje region i staden. Den här informationen kan enkelt visas av Power BI-instrumentpanelen eller kan sändas till alla drivrutiner som SMS via integrering med tjänster som Azure-funktioner.

Bilden nedan illustrerar utdata från frågan till Power BI-instrumentpanelen. 

![Resultatutgång på Power BI-instrumentpanelen](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Nästa steg

* [Introduktion till geospatiala stream analytics-funktioner](stream-analytics-geospatial-functions.md)
* [Geospatiala funktioner (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
