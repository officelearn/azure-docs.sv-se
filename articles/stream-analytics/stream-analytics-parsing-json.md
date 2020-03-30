---
title: Tolka JSON och AVRO i Azure Stream Analytics
description: I den här artikeln beskrivs hur du arbetar med komplexa datatyper som matriser, JSON, CSV-formaterade data.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484595"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Tolka JSON- och Avro-data i Azure Stream Analytics

Azure Stream Analytics stöder bearbetning av händelser i CSV-, JSON- och Avro-dataformat. Både JSON- och Avro-data kan struktureras och innehålla vissa komplexa typer, till exempel kapslade objekt (poster) och matriser. 

>[!NOTE]
>AVRO-filer som skapas av Event Hub Capture använder ett visst format som kräver att du använder funktionen *anpassad deserializer.* Mer information finns [i Läs indata i valfritt format med .NET-anpassade deserializers](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples).



## <a name="record-data-types"></a>Registrera datatyper
Postdatatyper används för att representera JSON- och Avro-matriser när motsvarande format används i indataströmmarna. Dessa exempel visar en provsensor som läser indatahändelser i JSON-format. Här är ett exempel på en enda händelse:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>Komma åt kapslade fält i känt schema
Använd punktatering (.) för att enkelt komma åt kapslade fält direkt från frågan. Den här frågan väljer till exempel Latitude- och Longitudkoordinaterna under egenskapen Plats i föregående JSON-data. Punkt notationen kan användas för att navigera flera nivåer som visas nedan.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Resultatet är:

|Deviceid|Lat|Lång|Temperatur|Version|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Markera alla egenskaper
Du kan markera alla egenskaper för en kapslad post med jokertecknet "*". Ta följande som exempel:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Resultatet är:

|Deviceid|Lat|Lång|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Ansluta kapslade fält när egenskapsnamn är en variabel

Använd funktionen [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) om egenskapsnamnet är en variabel. Detta gör det möjligt att skapa dynamiska frågor utan hårdkodningsegenskapsnamn.

Tänk dig till exempel att exempeldataströmmen **måste sammanfogas med referensdata** som innehåller tröskelvärden för varje enhetssensor. Nedan visas ett utdrag av sådana referensdata.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Målet här är att ansluta till vårt exempel datauppsättning från toppen av artikeln till att referensdata, och mata ut en händelse för varje sensor åtgärd över sin tröskel. Det innebär att vår enda händelse ovan kan generera flera utdatahändelser om flera sensorer är över sina respektive tröskelvärden, tack vare kopplingen. För att uppnå liknande resultat utan en koppling, se avsnittet nedan.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** väljer egenskapen i *SensorReadings*, vilket namn matchar egenskapsnamnet som kommer från referensdata. Därefter extraheras det associerade värdet från *SensorReadings.*

Resultatet är:

|Deviceid|SensorName (SensorName)|AlertMessage|
|-|-|-|
|12345|Fuktighet|Varning : Sensor över tröskelvärdet|

### <a name="convert-record-fields-into-separate-events"></a>Konvertera postfält till separata händelser

Om du vill konvertera postfält till separata händelser använder du operatorn [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) tillsammans med funktionen [GetRecordProperties.](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics)

Med de ursprungliga exempeldata kan följande fråga användas för att extrahera egenskaper till olika händelser.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Resultatet är:

|Deviceid|SensorName (SensorName)|AlertMessage|
|-|-|-|
|12345|Temperatur|80|
|12345|Fuktighet|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[objektobjekt]|

Med [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)är det då möjligt att dirigera dessa händelser till olika destinationer:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>Tolka JSON-post i SQL-referensdata
När du använder Azure SQL Database som referensdata i jobbet är det möjligt att ha en kolumn som har data i JSON-format. Ett exempel på detta visas nedan.

|Deviceid|Data|
|-|-|
|12345|{"nyckel" : "värde1"}|
|54321|{"nyckel" : "value2"}|

Du kan tolka JSON-posten i kolumnen *Data* genom att skriva en enkel JavaScript-användardefinierad funktion.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Du kan sedan skapa ett steg i din Stream Analytics-fråga enligt nedan för att komma åt fälten i dina JSON-poster.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Matrisdatatyper

Matrisdatatyper är en ordnad samling värden. Vissa typiska åtgärder på matrisvärden beskrivs nedan. I de här exemplen används funktionerna [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)och [OPERATOR APPLY.](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics)

Här är ett exempel på en enda händelse. Båda `CustomSensor03` `SensorMetadata` och är av typen **array:**

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Arbeta med ett visst matriselement

Välj matriselement vid ett angivet index (välja det första matriselementet):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Resultatet är:

|firstElement förstaElement|
|-|
|12|

### <a name="select-array-length"></a>Välj matrislängd

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Resultatet är:

|arrayLängd|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Konvertera matriselement till separata händelser

Markera alla matriselement som enskilda händelser. [Operatorn APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) tillsammans med den inbyggda [funktionen GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) extraherar alla matriselement som enskilda händelser:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Resultatet är:

|DeviceId|ArrayIndex (olika betydelser)|Matrisvärde|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Resultatet är:

|DeviceId|smKey (snAng)|smVärde|
|-|-|-|
|12345|Tillverkare|ABC|
|12345|Version|1.2.45|

Om de extraherade fälten måste visas i kolumner är det möjligt att pivotera datauppsättningen med hjälp av syntaxen [MED](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) förutom [JOIN-åtgärden.](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) Kopplingen kräver ett [tidsgränstillstånd](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) som förhindrar dubblering:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Resultatet är:

|DeviceId|Lat|Lång|smVersion (sn)|smTillverkare|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Se även
[Datatyper i Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
