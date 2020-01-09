---
title: Parsar JSON och AVRO i Azure Stream Analytics
description: Den här artikeln beskriver hur du arbetar med komplexa data typer som matriser, JSON, CSV-formaterade data.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1741510c7398ce74da81f006cb4109d9a33f8f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431595"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Parsa JSON-och Avro-data i Azure Stream Analytics

Azure Stream Analytics att bearbeta händelser i CSV-, JSON-och Avro data format. Både JSON-och Avro-data kan struktureras och innehålla några komplexa typer, till exempel kapslade objekt (poster) och matriser. 




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
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Välj alla egenskaper
Du kan välja alla egenskaper för en kapslad post med jokertecknet *. Ta följande som exempel:

```SQL
SELECT input.Location.*
FROM input
```

Resultatet är:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Komma åt kapslade fält när egenskaps namnet är en variabel
Använd funktionen [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) om egenskaps namnet är en variabel. 

Anta till exempel att en exempel data ström måste vara kopplad till referens data som innehåller tröskelvärden för varje enhets sensor. Ett kodfragment av dessa referens data visas nedan.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Konvertera post fält till separata händelser
Om du vill konvertera postfält till separata händelser använder [du operatorn](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) use tillsammans med funktionen [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) . Om exempelvis föregående exempel hade flera poster för SensorReading, kan följande fråga användas för att extrahera dem till olika händelser:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Mat ris data typer

Mat ris data typer är en ordnad samling av värden. Några vanliga åtgärder för mat ris värden beskrivs nedan. Dessa exempel förutsätter att indata-händelserna har en egenskap med namnet "arrayField" som är en mat ris data typ.

I de här exemplen används Functions- [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)och operatorn [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) .

### <a name="working-with-a-specific-array-element"></a>Arbeta med ett enskilt mat ris element
Välj mat ris element i ett angivet index (Välj det första mat ris elementet):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Välj mat ris längd

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Omvandla mat ris element till separata händelser
Markera alla mat ris element som enskilda händelser. Operatorn [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) tillsammans med den inbyggda funktionen [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) extraherar alla mat ris element som enskilda händelser:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Se även
[Data typer i Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
