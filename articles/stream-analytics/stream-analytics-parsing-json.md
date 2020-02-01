---
title: Parsar JSON och AVRO i Azure Stream Analytics
description: Den här artikeln beskriver hur du arbetar med komplexa data typer som matriser, JSON, CSV-formaterade data.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: ac06521df38bdc91ca717d888c73cd541576014d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905448"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Parsa JSON-och Avro-data i Azure Stream Analytics

Azure Stream Analytics att bearbeta händelser i CSV-, JSON-och Avro data format. Både JSON-och Avro-data kan struktureras och innehålla några komplexa typer, till exempel kapslade objekt (poster) och matriser. 

>[!NOTE]
>AVRO-filer som skapats av Event Hub-insamling använder ett speciellt format som kräver att du använder funktionen *anpassad deserialisering* . Mer information finns i [läsa in indata i alla format med anpassade .net-deserialiserare](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples).



## <a name="record-data-types"></a>Registrera data typer
Post data typer används för att representera JSON-och Avro-matriser när motsvarande format används i indata strömmar. Dessa exempel demonstrerar en exempel sensor som läser ingångs händelser i JSON-format. Här är ett exempel på en enskild händelse:

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
Använd punkt notation (.) för att enkelt komma åt kapslade fält direkt från frågan. Den här frågan väljer till exempel de koordinater för latitud och longitud under egenskapen location i föregående JSON-data. Punkt notationen kan användas för att navigera flera nivåer som visas nedan.

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

|DeviceID|koder|Lång|Temperatur|Version|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Välj alla egenskaper
Du kan välja alla egenskaper för en kapslad post med jokertecknet *. Ta följande som exempel:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Resultatet är:

|DeviceID|koder|Lång|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Komma åt kapslade fält när egenskaps namnet är en variabel

Använd funktionen [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) om egenskaps namnet är en variabel. Detta gör det möjligt att skapa dynamiska frågor utan hårdkoda egenskaps namn.

Anta till exempel att data strömmen i exempel måste **kopplas till referens data** som innehåller tröskelvärden för varje enhets sensor. Ett kodfragment av dessa referens data visas nedan.

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

Målet här är att koppla exempel data uppsättningen från överkanten av artikeln till dessa referens data och mata ut en händelse för varje sensor mått över tröskelvärdet. Det innebär att vår enskilda händelse ovan kan generera flera utgående händelser om flera sensorer är över respektive tröskelvärde, tack vare kopplingen. Om du vill uppnå liknande resultat utan någon koppling, se avsnittet nedan.

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

**GetRecordPropertyValue** väljer egenskapen i *SensorReadings*, vars namn matchar det egenskaps namn som kommer från referens data. Sedan extraheras det associerade värdet från *SensorReadings* .

Resultatet är:

|DeviceID|sensorName|Alertmessage som|
|-|-|-|
|12345|Fuktighet|Varning: sensor över tröskelvärdet|

### <a name="convert-record-fields-into-separate-events"></a>Konvertera post fält till separata händelser

Om du vill konvertera postfält till separata händelser använder [du operatorn](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) use tillsammans med funktionen [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) .

Med de ursprungliga exempel data kan följande fråga användas för att extrahera egenskaper till olika händelser.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Resultatet är:

|DeviceID|sensorName|Alertmessage som|
|-|-|-|
|12345|Temperatur|80|
|12345|Fuktighet|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[objekt objekt]|

Med [hjälp av](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics), kan du sedan dirigera dessa händelser till olika mål:

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

## <a name="array-data-types"></a>Mat ris data typer

Mat ris data typer är en ordnad samling av värden. Några vanliga åtgärder för mat ris värden beskrivs nedan. I de här exemplen används Functions- [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)och operatorn [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) .

Här är ett exempel på en enskild händelse. Både `CustomSensor03` och `SensorMetadata` är av typen **matris**:

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

### <a name="working-with-a-specific-array-element"></a>Arbeta med ett enskilt mat ris element

Välj mat ris element i ett angivet index (Välj det första mat ris elementet):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Resultatet är:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Välj mat ris längd

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Resultatet är:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Omvandla mat ris element till separata händelser

Markera alla mat ris element som enskilda händelser. Operatorn [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) tillsammans med den inbyggda funktionen [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) extraherar alla mat ris element som enskilda händelser:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Resultatet är:

|deviceId|ArrayIndex|ArrayValue|
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

|deviceId|smKey|smValue|
|-|-|-|
|12345|Tillverkare|Pia|
|12345|Version|1.2.45|

Om de extraherade fälten måste visas i kolumner, är det möjligt att pivotera data uppsättningen med syntaxen [with](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) , förutom [kopplings](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) åtgärden. Den här kopplingen kräver ett [tids gräns](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) villkor som förhindrar duplicering:

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

|deviceId|koder|Lång|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|Pia|

## <a name="see-also"></a>Se även
[Data typer i Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
