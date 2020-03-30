---
title: Data fabriksfunktioner och systemvariabler
description: Innehåller en lista över Azure Data Factory-funktioner och systemvariabler
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73667656"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - Funktioner och systemvariabler
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Systemvariabler i Data Factory](../control-flow-system-variables.md).

Den här artikeln innehåller information om funktioner och variabler som stöds av Azure Data Factory.

## <a name="data-factory-system-variables"></a>Data Factory systemvariabler

| Variabelnamn | Beskrivning | Objektets omfattning | JSON Omfattning och användningsfall |
| --- | --- | --- | --- |
| Fönsterstart |Tidsintervallstart för fönstret aktuell aktivitetskörning |aktivitet |<ol><li>Ange datavalsfrågor. Se anslutningsartiklar som refereras i artikeln [Dataflyttningsaktiviteter.](data-factory-data-movement-activities.md)</li> |
| FönsterEnd |Slutintervall för aktuella fönstret för aktivitetskörning |aktivitet |samma som WindowStart. |
| Segmentstart |Start av tidsintervall för datasegment som produceras |aktivitet<br/>Datamängd |<ol><li>Ange dynamiska mappsökvägar och filnamn när du arbetar med [Azure Blob-](data-factory-azure-blob-connector.md) och [File System-datauppsättningar](data-factory-onprem-file-system-connector.md).</li><li>Ange indataberoenden med datafabriksfunktioner i samlingen för indata i aktivitetsindata.</li></ol> |
| Skivad |på tidsintervallet för aktuellt datasegment. |aktivitet<br/>Datamängd |samma som SliceStart. |

> [!NOTE]
> För närvarande kräver datafabriken att schemat som anges i aktiviteten exakt matchar det schema som anges i tillgängligheten för utdatauppsättningen. Därför mappas alltid WindowStart, WindowEnd och SliceStart och SliceEnd till samma tidsperiod och ett enda utdatasegment.
> 

### <a name="example-for-using-a-system-variable"></a>Exempel för att använda en systemvariabel
I följande exempel extraheras år, månad, dag och tid för **SegmentStart** i separata variabler som används av **egenskaperna folderPath** och **fileName.**

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory-funktioner
Du kan använda funktioner i datafabriken tillsammans med systemvariabler för följande ändamål:

1. Ange datavalsfrågor (se kopplingsartiklar som refereras av artikeln [Dataflyttningsaktiviteter.](data-factory-data-movement-activities.md)
   
   Syntaxen för att anropa en ** $$ \<** datafabriksfunktion är: funktion>för datavalsfrågor och andra egenskaper i aktiviteten och datauppsättningarna.  
2. Ange indataberoenden med datafabriksfunktioner i samlingen för indata i aktivitetsindata.
   
    $$ behövs inte för att ange indataberoendeuttryck.     

I följande exempel tilldelas **sqlReaderQuery-egenskapen** i en JSON-fil `Text.Format` ett värde som returneras av funktionen. Det här exemplet använder också en systemvariabel med namnet **WindowStart**, som representerar starttiden för fönstret för aktivitetskörning.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Se Avsnittet [Anpassade datum- och tidsformatsträngar](https://msdn.microsoft.com/library/8kb3ddd4.aspx) som beskriver olika formateringsalternativ som du kan använda (till exempel ay vs. yyyy). 

### <a name="functions"></a>Funktioner
I följande tabeller visas alla funktioner i Azure Data Factory:

| Kategori | Funktion | Parametrar | Beskrivning |
| --- | --- | --- | --- |
| Tid |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |Lägger till Y-timmar till den angivna tiden X. <br/><br/>Exempel: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Tid |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |Lägger till Y minuter till X.<br/><br/>Exempel: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Tid |Startofhour(X) |X: Datumtid |Hämtar starttiden för den timme som representeras av timkomponenten i X. <br/><br/>Exempel: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Datum |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |Lägger till Y-dagar i X. <br/><br/>Exempel: 2013-09-15 12:00:00 + 2 dagar = 2013-09-17 12:00:00.<br/><br/>Du kan subtrahera dagar också genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Datum |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |Lägger till Y månader till X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Du kan subtrahera månader också genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Datum |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |Lägger till Y * 3 månader till X.<br/><br/>Exempel: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Datum |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |Lägger till Y * 7 dagar till X<br/><br/>Exempel: 2013-09-15 12:00:00 + 1 vecka = 2013-09-22 12:00:00<br/><br/>Du kan subtrahera veckor också genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Datum |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |Lägger Y år till X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Du kan subtrahera år också genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Datum |Dag(X) |X: DateTime |Hämtar dagkomponenten i X.<br/><br/>Exempel: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Datum |Dagiveve(X) |X: DateTime |Hämtar veckodagskomponenten i X.<br/><br/>Exempel: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Datum |Dagiår(X) |X: DateTime |Får den dag i året som representeras av årskomponenten i X.<br/><br/>Exempel:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Datum |DaysInMonth(X) |X: DateTime |Hämtar dagarna i månaden som representeras av månadskomponenten i parameter X.<br/><br/>Exempel: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Datum |EndOfDay(X) |X: DateTime |Hämtar den datumtid som representerar slutet på dagen (dagkomponenten) för X.<br/><br/>Exempel: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Datum |EndOfMonth(X) |X: DateTime |Får slutet av månaden representeras av månad komponent av parameter X. <br/><br/>Exempel: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datumtid som representerar slutet av september månad) |
| Datum |Startofday(X) |X: DateTime |Hämtar början av dagen representeras av dagkomponenten i parameter X.<br/><br/>Exempel: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Från(X) |X: Sträng |Tolka sträng X till en datumtid. |
| DateTime |Fästingar(X) |X: DateTime |Hämtar egenskapen fästingar för parametern X. En fästing motsvarar 100 nanoseconds. Värdet för den här egenskapen representerar antalet fästingar som har förflutit sedan 12:00:00 midnatt, 1 januari 0001. |
| Text |Format(X) |X: Strängvariabel |Formaterar texten (använd `\\'` kombination `'` för att escape-tecken).|

> [!IMPORTANT]
> När du använder en funktion i en **$$** annan funktion behöver du inte använda prefixet för den inre funktionen. Till exempel: $$Text.Format('PartitionKey \\eq\\'my_pkey_filter_value' och RowKey \\ge '{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). I det här **$$** exemplet märker du att prefixet inte används för funktionen **Time.AddHours.** 

#### <a name="example"></a>Exempel
I följande exempel bestäms in- och utdataparametrar `Text.Format` för Hive-aktiviteten med hjälp av funktions- och SliceStart-systemvariabeln. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Exempel 2

I följande exempel bestäms parametern DateTime för den lagrade proceduraktiviteten med hjälp av texten. Formatfunktion och segmentstartvariabeln. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Exempel 3
Om du vill läsa data från föregående dag i stället för dag som representeras av SegmentStart använder du funktionen AddDays som visas i följande exempel: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Se Avsnittet [Anpassade datum- och tidsformatsträngar](https://msdn.microsoft.com/library/8kb3ddd4.aspx) som beskriver olika formateringsalternativ som du kan använda (till exempel: yy vs. yyyy). 

