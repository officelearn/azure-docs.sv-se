---
title: Datauppsättningar och länkade tjänster i Azure Data Factory | Microsoft Docs
description: Läs mer om datauppsättningar och länkade tjänster i Datafabriken. Länkade tjänster länkar beräkning/datalager till datafabriken. Datauppsättningar representerar in-/ utdata.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 9e5da96cb02e681c83bd707fc038117050712ccf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262044"
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Datamängder och länkade tjänster i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuell version](concepts-datasets-linked-services.md)

Den här artikeln beskriver vilka datauppsättningar är, hur de definieras i JSON-format och hur de används i Azure Data Factory-pipelines.

Om du är nybörjare till Data Factory finns i [introduktion till Azure Data Factory](introduction.md) en översikt.

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en Kopieringsaktivitet som kopierar data från en lokal SQL Server till Azure Blob storage. Du kan sedan använda en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster att bearbeta data från Blob storage för att producera utdata. Slutligen kan du använda en andra Kopieringsaktivitet för att kopiera utdata till Azure SQL Data Warehouse ovanpå som business intelligence (BI) reporting-lösningarna. Läs mer om pipelines och aktiviteter, [Pipelines och aktiviteter](concepts-pipelines-activities.md) i Azure Data Factory.

Nu kan en **datauppsättning** är en namngiven vy över data som helt enkelt pekar eller refererar till de data som du vill använda i din **aktiviteter** som indata eller utdata. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel blobcontainern och mappen i Blob Storage som aktiviteten ska läsa data från.

Innan du skapar en datauppsättning, måste du skapa en **länkad tjänst** att länka ditt datalager till datafabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Se det här sättet. datamängden representerar strukturen för data i länkade datalager och den länkade tjänsten definierar anslutningen till datakällan. Till exempel länkad en Azure Storage-tjänsten länkar ett storage-konto till datafabriken. En Azure Blob-datauppsättning representerar blobbehållaren och mappen i den Azure storage-konto som innehåller indatablobbar som ska bearbetas.

Här är ett exempelscenario. Om du vill kopiera data från Blob storage till en SQL-databas, skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två datauppsättningar: Azure Blob-datauppsättning (som refererar till den länkade Azure Storage-tjänsten) och Azure SQL-tabelldatauppsättning (som refererar till länkad Azure SQL Database-tjänsten). Innehåller anslutningssträngar som Datafabriken använder vid körning för att ansluta till ditt Azure Storage och Azure SQL Database, respektive Azure Storage och länkad Azure SQL Database-tjänster. Azure Blob-datauppsättning anger blobbehållaren och blobbmapp som innehåller indatablobbar i Blob storage. Azure SQL-tabelldatauppsättning ange den SQL-tabellen i SQL-databasen som data ska kopieras.

Följande diagram visar relationerna mellan pipeline, aktivitet, datauppsättning och den länkade tjänsten i Data Factory:

![Förhållandet mellan pipeline, aktivitet, datauppsättning, länkade tjänster](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Länkad tjänst-JSON
En länkad tjänst i Data Factory har definierats i JSON-format på följande sätt:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

I följande tabell beskrivs egenskaperna i ovanstående JSON:

Egenskap  | Beskrivning | Krävs |
-------- | ----------- | -------- |
namn | Namnet på den länkade tjänsten. Se [Azure Data Factory – namnregler](naming-rules.md). |  Ja |
typ | Typ av den länkade tjänsten. Exempel: AzureStorage (datalager) eller AzureBatch (beräkning). Se beskrivningen av typeProperties. | Ja |
typeProperties | Typegenskaperna är olika för varje datalager eller databeräkningar. <br/><br/> Lagra typer och deras egenskaper för data som stöds, se den [datauppsättningstypen](#dataset-type) tabellen i den här artikeln. Gå till data store connector artikeln för att lära dig om typegenskaperna som är specifika för ett datalager. <br/><br/> Stöds beräkningstyper och deras egenskaper finns i [länkade tjänster för Compute](compute-linked-services.md). | Ja |
connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. | Nej

## <a name="linked-service-example"></a>Länkad tjänst-exempel
Följande länkade tjänst är en länkad Azure Storage-tjänst. Observera att typen har angetts till AzureStorage. Typegenskaperna för länkad Azure Storage-tjänsten innehåller en anslutningssträng. Data Factory-tjänsten använder den här anslutningssträngen för att ansluta till datalagret i körningsfasen.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-json"></a>Datauppsättnings-JSON
En datauppsättning i Data Factory har definierats i JSON-format på följande sätt:

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
I följande tabell beskrivs egenskaperna i ovanstående JSON:

Egenskap  | Beskrivning | Krävs |
-------- | ----------- | -------- |
namn | Namnet på datauppsättningen. Se [Azure Data Factory – namnregler](naming-rules.md). |  Ja |
typ | Typ av datauppsättningen. Ange en av de typer som stöds av Data Factory (till exempel: AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [datauppsättningstyperna](#dataset-type). | Ja |
struktur | Schemat för datauppsättningen. Mer information finns i [datauppsättningsstrukturen](#dataset-structure). | Nej |
typeProperties | Typegenskaperna är olika för varje typ (till exempel: Azure Blob, Azure SQL-tabell). Mer information om typerna som stöds och deras egenskaper finns [datauppsättningstypen](#dataset-type). | Ja |

## <a name="dataset-example"></a>Exempel med datauppsättningen
I följande exempel representerar en tabell med namnet MyTable i en SQL-databas i datauppsättningen.

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

- typ har angetts till AzureSqlTable.
- tableName typegenskapen (specifikt för AzureSqlTable typ) har angetts till MyTable.
- linkedServiceName refererar till en länkad tjänst av typen AzureSqlDatabase som definieras i nästa JSON-kodfragmentet.

## <a name="dataset-type"></a>Typ av datauppsättning
Det finns många olika typer av datauppsättningar, beroende på datalagret som du använder. Se tabellen nedan för en lista över datalager som stöds av Data Factory. Klicka på ett datalager om du vill veta hur du skapar en länkad tjänst och en datauppsättning för det datalagringen.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

I exemplet i föregående avsnitt, vilken typ av datauppsättningen är inställd **AzureSqlTable**. På samma sätt för en Azure Blob-datauppsättning, även typ av datauppsättningen är inställt på **AzureBlob**, enligt följande JSON:

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
## <a name="dataset-structure"></a>DataSet-struktur
Den **struktur** avsnittet är valfritt. Den definierar schemat för datauppsättningen genom som innehåller en uppsättning namn och datatyperna för kolumnerna. Du kan använda avsnittet struktur för att ange av typinformation som används för att konvertera typer och mappa kolumner från källan till målet.

Varje kolumn i strukturen innehåller följande egenskaper:

Egenskap  | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namnet på kolumnen. | Ja
typ | Datatypen för kolumnen. Data Factory stöder följande datatyper av mellanliggande som tillåtna värden: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], booleskt, sträng, Guid, Datetime, Datetimeoffset och Timespan** | Nej
kultur | . NET-baserade språkmiljö som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. | Nej
Format | Formatera strängen som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset`. Referera till [anpassade datum- och Datumformatsträngar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) om hur du formaterar datetime. | Nej

### <a name="example"></a>Exempel
Anta att källan Blob-data i CSV-format och innehåller tre kolumner i följande exempel: användar-ID, namn och lastlogindate. De är av typen Int64, sträng- och Datetime med en anpassad datetime-format med hjälp av förkortade franska namnen för dag i veckan.

Definiera strukturen för Blob-datauppsättningen på följande sätt tillsammans med typdefinitioner för kolumner:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Riktlinjer

Följande riktlinjer hjälper dig att förstå när du ska inkludera strukturinformation och vad som ska ingå i den **struktur** avsnittet. Lär dig mer om hur data factory mappar källdata till mottagare och när du ska ange strukturinformation från [Schema- och Typmappning](copy-activity-schema-and-type-mapping.md).

- **För datakällor för stark schemat**, ange avsnittet struktur endast om du vill mappa källkolumner för att mottagare kolumner och deras namn inte är desamma. Den här typen av datakälla för strukturerade lagrar data schema och ange information tillsammans med själva informationen. Exempel på strukturerade datakällor är SQL Server, Oracle och Azure SQL Database.<br/><br/>Så anger du följande information är redan tillgänglig för strukturerade datakällor måste ta du inte anger du följande information när du inkluderar avsnittet struktur.
- **För Nej/weak schemat datakällor som t.ex. textfil i blob storage**, inkludera struktur när datauppsättningen utgör indata för en Kopieringsaktivitet och datatyperna för datauppsättningen för källan ska konverteras till inbyggda typer för mottagaren. Och inkludera struktur när du vill mappa källkolumner för att mottagare kolumner...

## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa datauppsättningar med någon av dessa verktyg och SDK: er: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager-mall och Azure portal

## <a name="current-version-vs-version-1-datasets"></a>Aktuell version jämfört med version 1 datauppsättningar

Här följer några skillnader mellan Data Factory och Data Factory version 1 datauppsättningar:

- Den externa egenskapen stöds inte i den aktuella versionen. Ersätts med en [utlösaren](concepts-pipeline-execution-triggers.md).
- Princip och tillgänglighet egenskaper stöds inte i den aktuella versionen. Starttiden för en pipeline beror på [utlösare](concepts-pipeline-execution-triggers.md).
- Begränsade datauppsättningar (datauppsättningar som definierats i en pipeline) stöds inte i den aktuella versionen.

## <a name="next-steps"></a>Nästa steg
Se följande självstudie för stegvisa instruktioner för att skapa pipelines och datauppsättningar med någon av dessa verktyg och SDK: er.

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Snabbstart: skapa en datafabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md)
- [Snabbstart: skapa en datafabrik med hjälp av REST API](quickstart-create-data-factory-rest-api.md)
- Snabbstart: skapa en datafabrik med hjälp av Azure portal
