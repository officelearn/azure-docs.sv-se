---
title: "Datauppsättningar och länkade tjänster i Azure Data Factory | Microsoft Docs"
description: "Läs mer om datauppsättningar och länkade tjänster i Data Factory. Länkade tjänster länka beräknings-/ datalager till data factory. Datauppsättningar representerar i/o-data."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: bfc95588378466fe1e83bcc4e899eca6b66b358a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Datauppsättningar och länkade tjänster i Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-create-datasets.md)
> * [Version 2 – förhandsversion](concepts-datasets-linked-services.md)

Den här artikeln beskriver vilka datauppsättningar är hur de har definierats i JSON-format och hur de används i Azure Data Factory V2 rörledningar. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [datauppsättningar i Data Factory V1](v1/data-factory-create-datasets.md).

Om du är nybörjare på Data Factory finns [introduktion till Azure Data Factory](introduction.md) en översikt. 

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utföra en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en kopieringsaktiviteten för att kopiera data från en lokal SQL Server till Azure Blob storage. Du kan sedan använda en Hive-aktivitet som kör en Hive-skript i ett Azure HDInsight-kluster för bearbetning av data från Blob storage gav inga utdata. Slutligen kan du använda en andra kopia aktiviteten kopiera utdata till Azure SQL Data Warehouse ovanpå vilka business intelligence (BI) reporting lösningar har skapats. Mer information om pipelines och aktiviteter finns [Pipelines och aktiviteter](concepts-pipelines-activities.md) i Azure Data Factory.

Nu kan en **dataset** är en namngiven vy som bara pekar eller refererar till de data som du vill använda i din **aktiviteter** som indata och utdata. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel blobbehållaren och mappen i Blob Storage som aktiviteten ska läsa data från.

Innan du skapar en datamängd, måste du skapa en **länkade tjänsten** länka datalager till datafabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Se det här sättet. dataset representerar strukturen för data i länkade datalager och den länkade tjänsten definierar anslutningen till datakällan. Till exempel kopplad ett Azure Storage tjänst länkar ett lagringskonto till datafabriken. Azure-blobbdatauppsättning som representerar blob-behållaren och mappen inom det Azure storage-konto som innehåller den inkommande BLOB-objekt som ska bearbetas.

Här är ett exempelscenario. Om du vill kopiera data från Blob storage till en SQL-databas, skapar du två länkade tjänster: Azure Storage- och Azure SQL Database. Skapa sedan två datamängder: Azure-blobbdatauppsättning (som refererar till länkad Azure Storage service) och Azure SQL-tabell datauppsättningen (varvid refererar till Azure SQL Database länkade tjänsten). Azure Storage- och Azure SQL Database länkade tjänster innehålla anslutningssträngar som Data Factory använder vid körning för att ansluta till ditt Azure Storage och Azure SQL Database respektive. Azure-blobbdatauppsättning anger blob-behållaren och blob-mapp som innehåller de inkommande blobbarna i Blob storage. Azure SQL-tabell datauppsättningen anger SQL-tabellen i SQL-databasen som är data som ska kopieras.

Följande diagram visar relationerna mellan pipeline, aktivitet, datamängd och länkade tjänsten i Data Factory:

![Förhållandet mellan pipeline, aktivitet, dataset, länkade tjänster](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

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

Egenskap | Beskrivning | Krävs |
-------- | ----------- | -------- |
namn | Namnet på den länkade tjänsten. Se [Azure Data Factory - namngivningsregler](naming-rules.md). |  Ja |
typ | Typ av den länkade tjänsten. Till exempel: AzureStorage (datalager) eller AzureBatch (beräkning). Se beskrivningen av typeProperties. | Ja |
typeProperties | Typegenskaper är olika för varje dataarkiv eller compute. <br/><br/> Lagra typer och deras egenskaper för data som stöds, finns det [datauppsättningstypen](#dataset-type) tabellen i den här artikeln. Gå till data store connector artikeln lär dig mer om Typegenskaper som är specifika för ett datalager. <br/><br/> Stöds beräkningstyper och deras egenskaper finns i [Compute länkade tjänster](compute-linked-services.md). | Ja |
connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i ett privat nätverk). Om inget anges används standard-Azure Integration Runtime. | Nej

## <a name="linked-service-example"></a>Länkad tjänst-exempel
Följande länkade tjänst är en länkad Azure Storage-tjänst. Observera att typen har angetts till AzureStorage. Typegenskaperna för länkad Azure Storage-tjänsten innehåller en anslutningssträng. Data Factory-tjänsten använder den här anslutningssträngen för att ansluta till datalagret under körning. 

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

## <a name="dataset-json"></a>Dataset JSON
En datamängd i Data Factory har definierats i JSON-format på följande sätt:

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

Egenskap | Beskrivning | Krävs |
-------- | ----------- | -------- |
namn | Namnet på datamängden. Se [Azure Data Factory - namngivningsregler](naming-rules.md). |  Ja |
typ | Typ av datauppsättningen. Ange en av de typer som stöds av Data Factory (till exempel: AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [Dataset typer](#dataset-types). | Ja |
struktur | Schemat för datauppsättningen. Mer information finns i [datauppsättningsstrukturen](#dataset-structure). | Nej |
typeProperties | Typegenskaper är olika för varje typ (till exempel: Azure Blob, Azure SQL-tabell). Mer information om typerna som stöds och deras egenskaper finns [datauppsättningstypen](#dataset-type). | Ja |

## <a name="dataset-example"></a>DataSet-exempel
I följande exempel representerar datauppsättningen en tabell med namnet mytable prefix i en SQL-databas.

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

- typen har angetts till AzureSqlTable.
- tableName typegenskapen (specifikt för AzureSqlTable typ) anges till mytable prefix.
- linkedServiceName refererar till en länkad tjänst av typen AzureSqlDatabase som definieras i nästa JSON-fragment.

## <a name="dataset-type"></a>Typen av datauppsättning
Det finns många olika typer av datauppsättningar, beroende på dataarkivet du använder. Se följande tabell för en lista över datakällor som stöds av Data Factory. Klicka på ett datalager för att lära dig hur du skapar en länkad tjänst och en datauppsättning för att lagra data.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

I exemplet ovan är typ av datamängden har angetts till **AzureSqlTable**. För en Azure-blobbdatauppsättning anges på samma sätt typ av datamängden som **AzureBlob**, enligt följande JSON:

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
Den **struktur** avsnittet är valfritt. Den definierar schemat för datauppsättningen genom som innehåller en samling med namn och datatyperna för kolumnerna. Du kan använda avsnittet strukturen för att ange av typinformation som används för att konvertera typer och mappa kolumner från källan till målet. I följande exempel datamängden har tre kolumner: tidsstämpel projektnamn, och pageviews. De är av typen String, String och Decimal, respektive.

```json
[
    { "name": "timestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Varje kolumn i strukturen innehåller följande egenskaper:

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
namn | Namnet på kolumnen. | Ja
typ | Datatypen för kolumnen. | Nej
Kultur | . NET-baserade kulturen som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. | Nej
format | Formatsträng som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset`. | Nej

Följande riktlinjer hjälper dig att avgöra när du ska inkludera strukturinformation och vad som ska ingå i den **struktur** avsnitt.

- **För strukturerade datakällor**, ange struktur avsnittet om du vill mappa källkolumner för sink kolumner och deras namn är inte samma. Den här typen av datakälla för strukturerade lagrar data schema och ange information tillsammans med själva informationen. Exempel på strukturerade datakällor är SQL Server, Oracle och Azure SQL Database.<br/><br/>Som typen finns redan för strukturerade datakällor, kan inkludera du inte typinformation när du inkluderar avsnittet struktur.
- **För schemat för skrivskyddade datakällor (särskilt Blob storage)**, kan du lagra data utan att spara schemat eller typ information med data. Inkludera struktur för dessa typer av datakällor om du vill mappa källkolumner för sink kolumner. Inkludera även struktur när datauppsättningen utgör indata för en kopieringsaktiviteten och datatyper i källan dataset ska konverteras till inbyggda typer för sink.<br/><br/> Data Factory stöder följande värden för att ange information i strukturen: `Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset, and Timespan`. 

Lär dig mer om hur data factory mappar källdata till sink från [Schema och mappning]( copy-activity-schema-and-type-mapping.md) och när du ska ange strukturinformation.

## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa datauppsättningar på något av dessa verktyg och SDK: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager-mall och Azure-portalen

## <a name="v1-vs-v2-datasets"></a>V1 vs. V2 datauppsättningar

Här följer några skillnader mellan datauppsättningar som Data Factory v1 och v2: 

- Egenskapen externa stöds inte i v2. Den har ersatts av en [utlösaren](concepts-pipeline-execution-triggers.md).
- Principen och tillgänglighet egenskaper stöds inte i V2. Starttiden för en pipeline beror på [utlösare](concepts-pipeline-execution-triggers.md).
- Begränsade datauppsättningar (datauppsättningar som definierats i en pipeline) stöds inte i V2. 

## <a name="next-steps"></a>Nästa steg
Se följande självstudierna för stegvisa instruktioner för att skapa pipelines och datauppsättningar på något av dessa verktyg och SDK: er. 

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Snabbstart: skapa en datafabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md)
- [Snabbstart: skapa en datafabrik med hjälp av REST API](quickstart-create-data-factory-rest-api.md)
- Snabbstart: skapa en datafabrik med hjälp av Azure portal
