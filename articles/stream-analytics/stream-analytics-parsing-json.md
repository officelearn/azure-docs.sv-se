---
title: Parsa JSON och AVRO i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder på komplexa datatyper, till exempel matriser, JSON, CSV-formaterad-data.
services: stream-analytics
ms.service: stream-analytics
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.topic: conceptual
origin.date: 08/03/2018
ms.date: 11/26/2018
ms.openlocfilehash: 3d4c1bfa8b376ec50efc7b3cddc8d22a40e70892
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478869"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Parsa JSON och Avro-data i Azure Stream Analytics

Azure Stream Analytics har stöd för bearbetning av händelser i CSV, JSON och Avro dataformat. JSON- och Avro-data kan innehålla komplexa typer, till exempel kapslade objekt (poster) och matriser. 

## <a name="array-data-types"></a>Matris-datatyper  
Matris datatyper är en ordnad uppsättning värden. Vissa vanliga åtgärder på matrisvärden beskrivs nedan. De här exemplen antar inkommande händelser har en egenskap med namnet ”arrayField” som är en matris-datatyp.

De här exemplen använder funktionerna [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics), och [tillämpa](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operator.

## <a name="examples"></a>Exempel  
 Välj matriselement vid ett specificerat index (och markera det första matriselementet):  

```SQL 
SELECT   
    GetArrayElement(arrayField, 0) AS firstElement  
FROM input  
```  

 Välj matrislängd:  

```SQL  
SELECT   
    GetArrayLength(arrayField) AS arrayLength  
FROM input  
```  

Välj alla matriselement som enskilda händelser. Den [tillämpa](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operatör tillsammans med den [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) inbyggd funktion extraherar alla matriselement som enskilda händelser:  

```SQL  
SELECT   
    arrayElement.ArrayIndex,  
    arrayElement.ArrayValue  
FROM input as event  
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement  
```  

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
        "CustomSensor02" : 99  
    }  
}  
```  

## <a name="examples"></a>Exempel  
Använd punktnotation (.) för att få åtkomst till kapslade fält. Den här frågan väljer Lat och länge samordnar under egenskapen Location i föregående JSON-data: 

```SQL  
SELECT  
    DeviceID,  
    Location.Lat,  
    Location.Long  
FROM input  
```  

Använd den [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) fungera om egenskapsnamnet är okänt. Anta exempelvis att en exempel-dataström måste kopplas till referens för data som innehåller tröskelvärden för varje enhet sensor:  

```json  
{  
    "DeviceId" : "12345",  
    "SensorName" :  "Temperature",
    "Value" : 75
}  
```  

```SQL  
SELECT  
    input.DeviceID,  
    thresholds.SensorName  
FROM input  
JOIN thresholds  
ON  
    input.DeviceId = thresholds.DeviceId  
WHERE  
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value  
```  

Konvertera postfält i separata händelser med den [tillämpa](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operatör tillsammans med den [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics) funktion. Exempelvis kan konvertera en exempel-dataström till en dataström med händelser med enskilda sensoravläsningar genom skulle den här frågan kunna användas:  

```SQL  
SELECT   
    event.DeviceID,  
    sensorReading.PropertyName,  
    sensorReading.PropertyValue  
FROM input as event  
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading  
```  

Du kan välja alla egenskaper för en kapslad post med hjälp av ' *' med jokertecken. Ta följande som exempel:  

```SQL  
SELECT input.SensorReadings.*  
FROM input  
```  

Resultatet är:  

```json  
{  
    "Temperature" : 80,  
    "Humidity" : 70,  
    "CustomSensor01" : 5,  
    "CustomSensor022" : 99  
}  
```  

## <a name="see-also"></a>Se även  
 [Datatyper i Azure Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)
 
<!-- Update_Description: new articles on stream analytics parsing json -->
<!--ms.date: 09/17/2018-->