---
title: Parsa JSON och AVRO i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder på komplexa datatyper, till exempel matriser, JSON, CSV-formaterad-data.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329363"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Parsa JSON och Avro-data i Azure Stream Analytics

Azure Stream Analytics stöder bearbeta händelser i CSV, JSON och Avro dataformat. JSON-och Avro kan struktureras och innehålla vissa komplexa typer, till exempel kapslade objekt (poster) och matriser. 




## <a name="record-data-types"></a>Post-datatyper
Typer av poster data används för att representera JSON och Avro-matriser när motsvarande format används i de inkommande dataströmmarna. De här exemplen visar en exempel-sensor som läser in händelser i JSON-format. Här är exempel på en enskild händelse:

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


### <a name="access-nested-fields-in-known-schema"></a>Åtkomst till kapslade fält i känt schema
Använd punktnotation (.) för att enkelt komma åt kapslade fält direkt från din fråga. Till exempel väljer den här frågan koordinater för latitud och longitud under egenskapen Location i föregående JSON-data. Punktnotationen kan användas för att navigera på flera nivåer som visas nedan.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Välj alla egenskaper
Du kan välja alla egenskaper för en kapslad post med hjälp av ' *' med jokertecken. Ta följande som exempel:

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


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Få åtkomst till kapslade fält när egenskapsnamnet är en variabel
Använd den [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) fungera om egenskapsnamnet är en variabel. 

Anta exempelvis att en exempel-dataström måste kopplas till referens för data som innehåller tröskelvärden för varje enhet sensor. Ett kodfragment till dessa referensdata visas nedan.

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

### <a name="convert-record-fields-into-separate-events"></a>Konvertera postfält i separata händelser
Konvertera postfält i separata händelser med den [tillämpa](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operatör tillsammans med den [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) funktion. Om det tidigare exemplet har haft flera poster för SensorReading, kan till exempel följande fråga användas för att extrahera dem till olika händelser:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Matris-datatyper

Matris datatyper är en ordnad uppsättning värden. Vissa vanliga åtgärder på matrisvärden beskrivs nedan. De här exemplen antar inkommande händelser har en egenskap med namnet ”arrayField” som är en matris-datatyp.

De här exemplen använder funktionerna [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics), och [tillämpa](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operator.

### <a name="working-with-a-specific-array-element"></a>Arbeta med en specifik matriselement
Välj matriselement vid ett specificerat index (och markera det första matriselementet):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Välj matrislängd

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Konvertera matriselement till separata händelser
Välj alla matriselement som enskilda händelser. Den [tillämpa](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operatör tillsammans med den [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) inbyggd funktion extraherar alla matriselement som enskilda händelser:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Se även
[Datatyper i Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
