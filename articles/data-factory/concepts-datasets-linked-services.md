---
title: Datauppsättningar
description: Läs mer om datauppsättningar i Data Factory. Datamängder representerar in- och utdata.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 878ad98b118fa02a6659584ac60e3343a948cd20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246271"
---
# <a name="datasets-in-azure-data-factory"></a>Datauppsättningar i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuell version](concepts-datasets-linked-services.md)

I den här artikeln beskrivs vilka datauppsättningar som är, hur de definieras i JSON-format och hur de används i Azure Data Factory-pipelines.

Om du inte har något nytt för Data Factory läser [du Introduktion till Azure Data Factory](introduction.md) för en översikt.

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Nu är en **datauppsättning** en namngiven vy av data som helt enkelt pekar eller refererar till de data som du vill använda i dina **aktiviteter** som indata och utdata. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel blobcontainern och mappen i Blob Storage som aktiviteten ska läsa data från.

Innan du skapar en datauppsättning måste du skapa en [**länkad tjänst**](concepts-linked-services.md) för att länka datalagret till datafabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Tänk på det så här; Datauppsättningen representerar datastrukturen i de länkade datalagret och den länkade tjänsten definierar anslutningen till datakällan. En Azure Storage-länkad tjänst länkar till exempel ett lagringskonto till datafabriken. En Azure Blob-datauppsättning representerar blob-behållaren och mappen i det Azure-lagringskontot som innehåller de indatablobar som ska bearbetas.

Här är ett exempelscenario. Om du vill kopiera data från Blob-lagring till en SQL-databas skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två datauppsättningar: Azure Blob-datauppsättning (som refererar till azure storage-länkade tjänsten) och Azure SQL Table-datauppsättningen (som refererar till azure SQL Database-länkade tjänsten). Azure Storage- och Azure SQL Database-länkade tjänster innehåller anslutningssträngar som Data Factory använder vid körning för att ansluta till din Azure Storage respektive Azure SQL Database. Azure Blob-datauppsättningen anger blob-behållaren och blob-mappen som innehåller indatablobar i blob-lagringen. Datauppsättningen för Azure SQL Table anger SQL-tabellen i SQL-databasen som data ska kopieras till.

I följande diagram visas relationerna mellan pipeline, aktivitet, datauppsättning och länkad tjänst i Data Factory:

![Förhållandet mellan pipeline, aktivitet, datauppsättning, länkade tjänster](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Dataset JSON
En datauppsättning i Data Factory definieras i följande JSON-format:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
I följande tabell beskrivs egenskaper i ovanstående JSON:

Egenskap | Beskrivning | Krävs |
-------- | ----------- | -------- |
namn | Datauppsättningens namn. Se [Azure Data Factory - Namngivningsregler](naming-rules.md). |  Ja |
typ | Typ av datauppsättning. Ange en av de typer som stöds av Data Factory (till exempel AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [Datauppsättningstyper](#dataset-type). | Ja |
Struktur | Schema för datauppsättningen. Mer information finns i [Dataset-schema](#dataset-structure-or-schema). | Inga |
typeProperties | Typegenskaperna är olika för varje typ (till exempel Azure Blob, Azure SQL-tabell). Mer information om vilka typer som stöds och deras egenskaper finns i [Datauppsättningstyp](#dataset-type). | Ja |

### <a name="data-flow-compatible-dataset"></a>Dataflödeskompatibla datauppsättning



Se [datauppsättningstyper som stöds](#dataset-type) för en lista över datauppsättningstyper som är [dataflödeskompatibla.](concepts-data-flow-overview.md) Datauppsättningar som är kompatibla för Dataflöde kräver detaljerade datauppsättningsdefinitioner för omvandlingar. JSON-definitionen är alltså något annorlunda. I stället _structure_ för en strukturegenskap har datauppsättningar som är dataflödeskompatibla en schemaegenskap. _schema_

I Dataflöde används datauppsättningar i käll- och sinkomvandlingar. Datauppsättningarna definierar de grundläggande dataschemana. Om dina data inte har något schema kan du använda schemadrift för din källa och diskho. Schemat i datauppsättningen representerar den fysiska datatypen och formen.

Genom att definiera schemat från datauppsättningen får du relaterade datatyper, dataformat, filplats och anslutningsinformation från den associerade länkade tjänsten. Metadata från datauppsättningarna visas i källomvandlingen som *källprojektion*. Projektionen i källomvandlingen representerar dataflödesdata med definierade namn och typer.

När du importerar schemat för en dataflödesdatauppsättning väljer du knappen **Importera schema** och väljer att importera från källan eller från en lokal fil. I de flesta fall importerar du schemat direkt från källan. Men om du redan har en lokal schemafil (en Parkettfil eller CSV med rubriker) kan du styra Data Factory för att basera schemat på filen.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

I följande tabell beskrivs egenskaper i ovanstående JSON:

Egenskap | Beskrivning | Krävs |
-------- | ----------- | -------- |
namn | Datauppsättningens namn. Se [Azure Data Factory - Namngivningsregler](naming-rules.md). |  Ja |
typ | Typ av datauppsättning. Ange en av de typer som stöds av Data Factory (till exempel AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [Datauppsättningstyper](#dataset-type). | Ja |
Schemat | Schema för datauppsättningen. Mer information finns i [Dataflödeskompatibla datauppsättningar](#dataset-type). | Inga |
typeProperties | Typegenskaperna är olika för varje typ (till exempel Azure Blob, Azure SQL-tabell). Mer information om vilka typer som stöds och deras egenskaper finns i [Datauppsättningstyp](#dataset-type). | Ja |


## <a name="dataset-example"></a>Exempel på datauppsättning
I följande exempel representerar datauppsättningen en tabell med namnet MyTable i en SQL-databas.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Observera följande punkter:

- är inställd på AzureSqlTable.
- egenskapen tableName type (specifik för AzureSqlTable-typen) är inställd på MyTable.
- linkedServiceName refererar till en länkad tjänst av typen AzureSqlDatabase, som definieras i nästa JSON-kodavsnitt.

## <a name="dataset-type"></a>Datauppsättningstyp
Det finns många olika typer av datauppsättningar, beroende på vilket datalager du använder. Du hittar listan över data som lagras som stöds av Data Factory från [Connector översiktsartikel.](connector-overview.md) Klicka på ett datalager om du vill lära dig hur du skapar en länkad tjänst och en datauppsättning för det datalagret.

I exemplet i föregående avsnitt anges typen av datauppsättning till **AzureSqlTable**. På samma sätt, för en Azure Blob-datauppsättning, är typen av datauppsättning inställd på **AzureBlob**, som visas i följande JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Datauppsättningsstruktur eller schema
**Strukturavsnittet** eller schemat (Dataflödeskompatibla) avsnittsdatauppsättningar är valfritt. **structure** Den definierar schemat för datauppsättningen genom att innehålla en samling namn och datatyper av kolumner. Du kan använda strukturavsnittet för att ange typinformation som används för att konvertera typer och mappa kolumner från källan till målet.

Varje kolumn i strukturen innehåller följande egenskaper:

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
namn | Kolumnens namn. | Ja
typ | Datatyp för kolumnen. Data Factory stöder följande interimsdatatyper som tillåtna värden: **Int16, Int32, Int64, Enkel, Dubbel, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset och Timespan** | Inga
Kultur | . NET-baserad odling som ska användas när typen `Datetime` `Datetimeoffset`är en .NET-typ: eller . Standardvärdet är `en-us`. | Inga
format | Formatera sträng som ska användas när typen `Datetime` är `Datetimeoffset`en .NET-typ: eller . Se [anpassade datum- och tidsformatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) om hur du formaterar datetime. | Inga

### <a name="example"></a>Exempel
I följande exempel antar du att källblobbdata är i CSV-format och innehåller tre kolumner: userid, namn och lastlogindate. De är av typen Int64, String och Datetime med ett anpassat datetime-format med förkortade franska namn för veckodag.

Definiera Blob-datauppsättningsstrukturen enligt följande tillsammans med typdefinitioner för kolumnerna:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Riktlinjer

Följande riktlinjer hjälper dig att förstå när du ska **structure** inkludera strukturinformation och vad som ska ingå i strukturavsnittet. Läs mer om hur datafabriken mappar källdata till handfat och när strukturinformation ska anges från [Schema och typmappning](copy-activity-schema-and-type-mapping.md).

- **För starka schemadatakällor**anger du bara strukturavsnittet om du vill att kartklumnerna ska sänka kolumnerna och deras namn inte är desamma. Den här typen av strukturerad datakälla lagrar dataschema och typinformation tillsammans med själva datan. Exempel på strukturerade datakällor är SQL Server, Oracle och Azure SQL Database.<br/><br/>Eftersom typinformation redan är tillgänglig för strukturerade datakällor bör du inte inkludera typinformation när du inkluderar strukturavsnittet.
- **För inga/svaga schemadatakällor, t.ex.** Och inkludera struktur när du vill mappa källkolumner till sinkkolumner..

## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa datauppsättningar med hjälp av något av dessa verktyg eller SDK:er: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager Template och Azure portal

## <a name="current-version-vs-version-1-datasets"></a>Aktuella datauppsättningar för version jämfört med version 1

Här är några skillnader mellan Data Factory och Data Factory version 1 datauppsättningar:

- Den externa egenskapen stöds inte i den aktuella versionen. Den ersätts av en [utlösare.](concepts-pipeline-execution-triggers.md)
- Egenskaperna för princip och tillgänglighet stöds inte i den aktuella versionen. Starttiden för en pipeline beror på [utlösare](concepts-pipeline-execution-triggers.md).
- Begränsade datauppsättningar (datauppsättningar som definierats i en pipeline) stöds inte i den aktuella versionen.

## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurs för steg-för-steg-instruktioner för att skapa pipelines och datauppsättningar med hjälp av något av dessa verktyg eller SDK:er.

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Snabbstart: skapa en datafabrik med PowerShell](quickstart-create-data-factory-powershell.md)
- [Snabbstart: skapa en datafabrik med REST API](quickstart-create-data-factory-rest-api.md)
- [Snabbstart: skapa en datafabrik med Azure-portalen](quickstart-create-data-factory-portal.md)
