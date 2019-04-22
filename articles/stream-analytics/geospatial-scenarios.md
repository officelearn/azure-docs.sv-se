---
title: Geofencing och geospatiala aggregering scenarier med Azure Stream Analytics
description: Den här artikeln beskriver hur du använder Azure Stream Analytics för geofencing och geospatiala aggregering.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: cc301855e4cdcb8eb687e753835577399cfe72b0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58807285"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Geofencing och geospatiala aggregering scenarier med Azure Stream Analytics

Du kan använda inbyggda geospatiala funktioner, för att använda Azure Stream Analytics om du vill bygga program för scenarier, t.ex hantering av vagnpark trampa delning, anslutna bilar och spårning av tillgångar.

## <a name="geofencing"></a>Geofencing

Azure Stream Analytics stöder svarstider i realtid geofencing beräkningar i molnet och på IoT Edge-körningen.

### <a name="geofencing-scenario"></a>Geofencing scenario

Ett tillverkningsföretag behöver för att spåra tillgångar på sina byggnader. De utrustade alla enheter med en GPS och vill få meddelanden om en enhet lämnar ett visst område.

Referensdata som används i det här exemplet finns informationen om byggnader och de enheter som tillåts i var och en av byggnaderna att geofence-området. Kom ihåg att referensdata kan antingen vara statiska eller långsamma ändra. Statiska referensdata används för det här scenariot. En dataström genererar kontinuerligt enhets-ID och dess aktuella position.

### <a name="define-geofences-in-reference-data"></a>Definiera geofence-områdena i referensdata

En geofence-området kan definieras med ett GeoJSON-objekt. För jobb med kompatibilitet version 1.2 och högre geofence-områden kan också definieras med hjälp av välkända Text (WELL-KNOWN) som `NVARCHAR(MAX)`. WELL-KNOWN är en öppen geospatiala Consortium (OGC) standard som används för att representera rumsliga data i ett textformat.

Inbyggda geospatiala funktioner kan använda definierade geofence-områdena för att ta reda på om ett element är i eller utanför en specifik geofence-området polygon.

I följande tabell är ett exempel på geofence-området referensdata som kan lagras i Azure blob storage eller Azure SQL-tabell. Varje plats representeras av en polygon geospatiala och alla enheter som är associerad med en tillåten webbplats-ID.

|SiteID|Webbplatsnamn|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Building 41"|”POLYGON ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432)) ”|"B"|
|2|"Redmond Building 40"|”POLYGON ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009)) ”|”A”|
|3|"Redmond Building 22"|”POLYGON ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554)) ”|"C"|

### <a name="generate-alerts-with-geofence"></a>Generera meddelanden med geofence-området

Enheter kan generera deras ID och plats varje minut via en dataström som kallas `DeviceStreamInput`. I följande tabell är en ström av indata.

|DeviceID|GeoPosition|
|--------|-----------|
|”A”|"POINT(-122.13292341559497 47.636318374032726)"|
|"B"|"POINT(-122.13338475554553 47.63743531308874)"|
|"C"|"POINT(-122.13354001095752 47.63627622505007)"|

Du kan skriva en fråga som kopplar ihop enheten dataströmmen med referensdata geofence-området och genererar en avisering varje gång som en enhet ligger utanför en tillåtna byggnad.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Följande bild visar den geofence-områden. Du kan se där enheterna är i enlighet med dataströmmar som indata.

![Skapa geofence-områden](./media/geospatial-scenarios/building-geofences.png)

Enheten ”C” finns i byggnad 2-ID, vilket inte är tillåtet enligt referensdata. Den här enheten måste finnas i att skapa ID 3. Kör detta jobb genererar en avisering för den här specifika överträdelsen.

### <a name="site-with-multiple-allowed-devices"></a>Plats med flera tillåtna enheter

Om en plats kan flera enheter, en matris med enhets-ID definieras i `AllowedDeviceID` och en användardefinierade funktionen kan användas på den `WHERE` -satsen för att kontrollera om stream enhets-ID matchar alla enhets-ID i listan. Mer information finns i [Javascript UDF](stream-analytics-javascript-user-defined-functions.md) självstudierna molnjobb och [ C# UDF](stream-analytics-edge-csharp-udf.md) självstudierna edge-jobb.

## <a name="geospatial-aggregation"></a>Geospatial aggregering

Azure Stream Analytics stöder svarstider i realtid geospatiala sammanställning i molnet och på IoT Edge-körningen.

### <a name="geospatial-aggregation-scenario"></a>Geospatial aggregering scenario

En cab företaget att skapa ett realtidsprogram att leda sina cab-drivrutiner letar du efter resa mot områden i städer högre begäran för närvarande.

Företaget lagrar logiska regioner på ort som referensdata. Varje region definieras av en RegionID och RegionName geofence-området.

### <a name="define-the-geofences"></a>Definiera den geofence-områden

I följande tabell är ett exempel på geofence-området referensdata som kan lagras i Azure blob storage eller Azure SQL-tabell. Varje region representeras av en polygon geospatiala, som används för att jämföra med begäranden som kommer från strömmande data.

Dessa polygoner är endast för referens och representerar inte faktiska stad logisk eller fysisk uppsägningar.

|RegionID|RegionName|Geofence|
|--------|----------|--------|
|1|"SoHo"|”POLYGON ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|”Chinatown”|”POLYGON ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133)) ”|
|3|"Tribeca"|”POLYGON ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787)) ”|

### <a name="aggregate-data-over-a-window-of-time"></a>Sammanställda data över en tidsperiod

Följande tabell innehåller data som skickats av ”bilar”.

|Användar-ID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|”A”|"POINT(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.716439898624024)"|"POINT(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|”D”|"POINT(-74.00741090068288 40.71615626755086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Följande fråga ansluter enheten dataströmmen med referensdata geofence-området och beräknar antalet begäranden per region på ett tidsfönster på 15 minuter varje minut.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Den här frågan tar antalet begäranden varje minut under de senaste 15 minuterna efter varje region i en stad. Den här informationen kan visas enkelt i Power BI-instrumentpanel eller sändas till alla drivrutiner som SMS-meddelanden via integrering med tjänster som Azure functions.

Bilden nedan visar resultatet av frågan till Power BI-instrumentpanel. 

![Resultatet utdata på Power BI-instrumentpanel](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics geospatiala funktioner](stream-analytics-geospatial-functions.md)
* [Geospatiala funktioner (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
