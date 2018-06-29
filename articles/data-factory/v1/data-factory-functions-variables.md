---
title: Data Factory-funktioner och systemvariabler | Microsoft Docs
description: Visar en lista över Azure Data Factory-funktioner och systemvariabler
documentationcenter: ''
author: sharonlo101
manager: craigg
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b4d9a684c2c21ed9ec00b04963432f9ebcff7493
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048462"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - funktioner och systemvariabler
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [systemvariabler i Data Factory](../control-flow-system-variables.md).

Den här artikeln innehåller information om funktioner och variabler som stöds av Azure Data Factory.

## <a name="data-factory-system-variables"></a>Data Factory systemvariabler
| Variabelnamn | Beskrivning | Objektets Scope | JSON-Scope och användningsområden |
| --- | --- | --- | --- |
| WindowStart |Början av tidsintervall för aktuell aktivitet som kör Windows |aktivitet |<ol><li>Ange datafrågor val. Se connector artiklar som refereras i den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel.</li> |
| WindowEnd |Slutet av tidsintervall för aktuell aktivitet som kör Windows |aktivitet |samma som WindowStart. |
| SliceStart |Början av tidsintervall för datasektor som skapas |aktivitet<br/>DataSet |<ol><li>Ange dynamiska sökvägar och filnamn när du arbetar med [Azure Blob](data-factory-azure-blob-connector.md) och [filsystemet datauppsättningar](data-factory-onprem-file-system-connector.md).</li><li>Ange indata beroenden med data factory-funktioner i aktiviteten indata samling.</li></ol> |
| SliceEnd |Slut på tidsintervall för aktuella datasektorn. |aktivitet<br/>DataSet |samma som SliceStart. |

> [!NOTE]
> För närvarande kräver datafabriken att schemat som anges i aktiviteten exakt matchar det schema som angetts i tillgängligheten för datamängd för utdata. WindowStart, WindowEnd, och SliceStart och SliceEnd mappas därför alltid till samma tidsperiod och ett enda utflöde segment.
> 

### <a name="example-for-using-a-system-variable"></a>Exempel för att använda en systemvariabel
I följande exempel, år, månad, dag och tid för **SliceStart** extraheras till olika variabler som används av **folderPath** och **fileName** egenskaper.

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
Du kan använda funktionerna i data factory tillsammans med systemvariabler för följande ändamål:

1. Anger att markeringen datafrågor (finns connector artiklar som refererar till den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel.
   
   Syntax för att anropa en funktion som data factory är: **$$ <function>** för val av datafrågor och andra egenskaper i aktiviteten och datauppsättningar.  
2. Ange indata beroenden med data factory-funktioner i aktivitetssamling indata.
   
    $$ behövs inte för att ange indata beroende uttryck.     

I följande exempel **sqlReaderQuery** egenskap i en JSON-fil som har tilldelats ett värde som returneras av den `Text.Format` funktion. Det här exemplet använder också en systemvariabeln med namnet **WindowStart**, som representerar starttiden för kör-Aktivitetsfönstret.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Se [anpassade datum och tid formatsträngar](https://msdn.microsoft.com/library/8kb3ddd4.aspx) avsnitt som beskriver olika formateringsalternativ som du kan använda (till exempel: Visa kontra åååå). 

### <a name="functions"></a>Functions
I tabellerna nedan listas funktionerna i Azure Data Factory:

| Kategori | Funktion | Parametrar | Beskrivning |
| --- | --- | --- | --- |
| Tid |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |Lägger till Y timmar angiven tid X. <br/><br/>Exempel: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Tid |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |Lägger till Y minuter X.<br/><br/>Exempel: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Tid |StartOfHour(X) |X: Datetime |Hämtar starttiden för timme som representeras av timkomponenten för X. <br/><br/>Exempel: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |Lägger till Y dagar X. <br/><br/>Exempel: 9/15/2013 12:00:00 PM + 2 dagar = 9/17/2013 12:00:00 PM.<br/><br/>Du kan ta bort dagar för genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |Lägger till Y månader X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Du kan ta bort månader för genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |Lägger till Y * tre månader x.<br/><br/>Exempel: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |Lägger till Y * 7 dagar x<br/><br/>Exempel: 9/15/2013 12:00:00 PM + 1 vecka = 9/22/2013 12:00:00 PM<br/><br/>Du kan ta bort veckor för genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |Lägger till Y år X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Du kan ta bort år för genom att ange Y som ett negativt tal.<br/><br/>Exempel: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Day(X) |X: DateTime |Hämtar dagkomponenten för X.<br/><br/>Exempel: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(X) |X: DateTime |Hämtar dagen i veckodagskomponenten för X.<br/><br/>Exempel: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: DateTime |Hämtar dagen i representeras av årskomponenten för X.<br/><br/>Exempel:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X: DateTime |Hämtar dagar i månaden som månadskomponenten för parametern X.<br/><br/>Exempel: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay(X) |X: DateTime |Hämtar den tid som representerar dagen (dagkomponenten) för X.<br/><br/>Exempel: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth(X) |X: DateTime |Hämtar slutet på månaden som representeras av månadskomponenten för parametern X. <br/><br/>Exempel: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (datum tid som motsvarar slutet av September månad) |
| Date |StartOfDay(X) |X: DateTime |Hämtar början på dagen som representeras av dagkomponenten i parametern X.<br/><br/>Exempel: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |FROM(X) |X: sträng |Parsa strängen X till en datum-tid. |
| DateTime |Ticks(X) |X: DateTime |Hämtar skalstrecken-egenskapen för parametern X. En skalstreck är lika med 100 nanosekunder. Värdet för den här egenskapen representerar antalet intervall som har förflutit sedan 12:00:00 midnatt den 1 januari 0001. |
| Text |Format(X) |X: string-variabel |Formaterar texten (Använd `\\'` kombination för att undvika `'` tecken).|

> [!IMPORTANT]
> När du använder en funktion i en annan funktion behöver du inte använda **$$** prefix för inre funktionen. Till exempel: $$Text.Format ('PartitionKey eq \\' my_pkey_filter_value\\' och ge RowKey \\' {0: ÅÅÅÅ-MM-dd: mm: ss}\\'', Time.AddHours (SliceStart -6)). Observera att i det här exemplet **$$** prefix används inte för den **Time.AddHours** funktion. 

#### <a name="example"></a>Exempel
I följande exempel inkommande och utgående parametrar för aktiviteten Hive bestäms med hjälp av den `Text.Format` funktionen och SliceStart systemvariabeln. 

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

I följande exempel bestäms DateTime-parametern för aktiviteten lagrad procedur med hjälp av texten. Format-funktionen och SliceStart-variabeln. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
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
Om du vill läsa data från föregående dag i stället för dag som representeras av SliceStart funktionen AddDays som visas i följande exempel: 

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

Se [anpassade datum och tid formatsträngar](https://msdn.microsoft.com/library/8kb3ddd4.aspx) avsnitt som beskriver olika formateringsalternativ som du kan använda (till exempel: åå kontra åååå). 

