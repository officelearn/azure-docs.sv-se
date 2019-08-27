---
title: Data uppsättningar i Azure Data Factory | Microsoft Docs
description: Lär dig mer om data uppsättningar i Data Factory. Data uppsättningar representerar indata/utdata.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: bbf7159abf88ce70cc62d202a8375aad302a0552
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019972"
---
# <a name="datasets-in-azure-data-factory"></a>Data uppsättningar i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuell version](concepts-datasets-linked-services.md)

I den här artikeln beskrivs vilka data uppsättningar som är, hur de definieras i JSON-format och hur de används i Azure Data Factory pipelines.

Om du är nybörjare på Data Factory, se [Introduktion till Azure Data Factory](introduction.md) för en översikt.

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en aktivitet. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Nu är en **data uppsättning** en namngiven vy av data som bara pekar eller refererar till de data som du vill använda i dina **aktiviteter** som indata och utdata. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel blobcontainern och mappen i Blob Storage som aktiviteten ska läsa data från.

Innan du skapar en data uppsättning måste du skapa en [**länkad tjänst**](concepts-linked-services.md) för att länka ditt data lager till data fabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Tänk på det på det här sättet. data uppsättningen representerar strukturen för data i de länkade data lagringarna och den länkade tjänsten definierar anslutningen till data källan. Till exempel länkar en Azure Storage länkad tjänst ett lagrings konto till data fabriken. En Azure Blob-datauppsättning representerar BLOB-behållaren och mappen i det Azure Storage-konto som innehåller de blobar för indata som ska bearbetas.

Här är ett exempel scenario. Om du vill kopiera data från Blob Storage till en SQL-databas skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två data uppsättningar: Azure Blob-datauppsättningen (som refererar till den Azure Storage länkade tjänsten) och data uppsättningen för Azure SQL-tabellen (som refererar till den Azure SQL Database länkade tjänsten). De länkade tjänsterna Azure Storage och Azure SQL Database innehåller anslutnings strängar som Data Factory använder vid körning för att ansluta till dina Azure Storage respektive Azure SQL Database. Azure Blob-datauppsättningen anger BLOB-behållaren och blob-mappen som innehåller blobar för indata i blob-lagringen. Data uppsättningen för Azure SQL-tabellen anger den SQL-tabell i SQL-databasen som data ska kopieras till.

Följande diagram visar relationerna mellan pipeline, aktivitet, data uppsättning och länkad tjänst i Data Factory:

![Relation mellan pipeline, aktivitet, data uppsättning, länkade tjänster](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Data mängds-JSON
En data uppsättning i Data Factory definieras i följande JSON-format:

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

Egenskap | Beskrivning | Obligatorisk |
-------- | ----------- | -------- |
name | Data uppsättningens namn. Se [Azure Data Factory namngivnings regler](naming-rules.md). |  Ja |
type | Typ av data uppsättning. Ange en av de typer som stöds av Data Factory (till exempel: AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [data uppsättnings typer](#dataset-type). | Ja |
structure | Schema för data uppsättningen. Mer information finns i [data uppsättnings schema](#dataset-structure-or-schema). | Nej |
typeProperties | Typ egenskaperna är olika för varje typ (till exempel: Azure-Blob, Azure SQL-tabell). Mer information om de typer som stöds och deras egenskaper finns i [data uppsättnings typ](#dataset-type). | Ja |

### <a name="data-flow-compatible-dataset"></a>Data flöde-kompatibel data mängd

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Se de [data uppsättnings typer som stöds](#dataset-type) för en lista över data uppsättnings typer som är kompatibla med [data flöde](concepts-data-flow-overview.md) . Data uppsättningar som är kompatibla för ett data flöde kräver detaljerade data uppsättnings definitioner för omvandlingar. Därför är JSON-definitionen något annorlunda. I stället för en _struktur_ egenskap är data uppsättningar som är kompatibla med data flödet en _schema_ egenskap.

I data flöde används data uppsättningar i käll-och mottagar omvandlingar. Data uppsättningarna definierar de grundläggande data scheman. Om dina data saknar schema kan du använda schema avvikelser för din källa och mottagare. Schemat i data uppsättningen representerar den fysiska data typen och formen.

Genom att definiera schemat från data uppsättningen får du de relaterade data typerna, data formaten, fil platsen och anslutnings informationen från den associerade länkade tjänsten. Metadata från data uppsättningarna visas i käll omvandlingen som käll *projektion*. Projektionen i käll omvandlingen representerar data flödes data med definierade namn och typer.

När du importerar schemat för en data flödes data uppsättning väljer du knappen **Importera schema** och väljer att importera från källan eller från en lokal fil. I de flesta fall importerar du schemat direkt från källan. Men om du redan har en lokal schema fil (en Parquet-fil eller en CSV-fil med rubriker) kan du direkt Data Factory för att basera schemat på filen.


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

I följande tabell beskrivs egenskaperna i ovanstående JSON:

Egenskap | Beskrivning | Obligatorisk |
-------- | ----------- | -------- |
name | Data uppsättningens namn. Se [Azure Data Factory namngivnings regler](naming-rules.md). |  Ja |
type | Typ av data uppsättning. Ange en av de typer som stöds av Data Factory (till exempel: AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [data uppsättnings typer](#dataset-type). | Ja |
schema | Schema för data uppsättningen. Mer information finns i [Data Flow-kompatibla data uppsättningar](#dataset-type). | Nej |
typeProperties | Typ egenskaperna är olika för varje typ (till exempel: Azure-Blob, Azure SQL-tabell). Mer information om de typer som stöds och deras egenskaper finns i [data uppsättnings typ](#dataset-type). | Ja |


## <a name="dataset-example"></a>Exempel på data uppsättning
I följande exempel representerar data uppsättningen en tabell med namnet min tabell i en SQL-databas.

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

- typ är inställd på AzureSqlTable.
- Egenskapen tableName Type (som är speciell för AzureSqlTable-typ) har angetts till tabellen Table.
- linkedServiceName refererar till en länkad tjänst av typen AzureSqlDatabase, som definieras i nästa JSON-kodfragment.

## <a name="dataset-type"></a>Data uppsättnings typ
Det finns många olika typer av data uppsättningar, beroende på vilket data lager du använder. I följande tabell finns en lista över data lager som stöds av Data Factory. Klicka på ett data lager om du vill veta hur du skapar en länkad tjänst och en data uppsättning för det data lagret.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/connector-activity-support-matrix.md)]

I exemplet i föregående avsnitt är typ av data uppsättning inställd på **AzureSqlTable**. På samma sätt anges typ av data uppsättning till **AzureBlob**för en Azure Blob-datamängd, som du ser i följande JSON:

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

## <a name="dataset-structure-or-schema"></a>Data uppsättnings struktur eller schema
Avsnittet för **struktur** avsnittet eller **schemat** (Data Flow Compatible) är valfritt. Den definierar data uppsättningens schema genom att innehålla en samling med namn och data typer för kolumner. Du kan använda avsnittet struktur för att tillhandahålla typ information som används för att konvertera typer och mappa kolumner från källan till målet.

Varje kolumn i strukturen innehåller följande egenskaper:

Egenskap | Beskrivning | Obligatorisk
-------- | ----------- | --------
name | Kolumnens namn. | Ja
type | Kolumnens datatyp. Data Factory stöder följande Interimistiska data typer som tillåtna värden: **Int16, Int32, Int64, enkel, dubbel, decimal, byte [], Boolean, sträng, GUID, DateTime, DateTimeOffset och TimeSpan** | Nej
culture | . NET-baserad kultur som ska användas när typen är en .net-typ: `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. | Nej
format | Format sträng som ska användas när typen är en .net-typ: `Datetime` eller `Datetimeoffset`. Referera till [anpassade datum-och tids format strängar](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) för hur du formaterar DateTime. | Nej

### <a name="example"></a>Exempel
I följande exempel antar vi att käll-BLOB-data är i CSV-format och innehåller tre kolumner: UserID, Name och lastlogindate. De är av typen Int64, String och datetime med ett anpassat datetime-format med förkortade franska namn för veckodag.

Definiera strukturen för BLOB-datauppsättningen enligt följande, tillsammans med typ definitioner för kolumnerna:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Riktlinjer

Följande rikt linjer hjälper dig att förstå när du ska inkludera struktur information och vad som ska ingå i **struktur** avsnittet. Läs mer om hur Data Factory mappar källdata till Sink och när du ska ange struktur information från [schema och typ mappning](copy-activity-schema-and-type-mapping.md).

- **För data källor med stark schema**anger du avsnittet struktur endast om du vill mappa käll kolumner till Sink-kolumner, och deras namn är inte samma. Den här typen av strukturerad data källa lagrar data schema och skriver information tillsammans med själva data. Exempel på strukturerade data källor är SQL Server, Oracle och Azure SQL Database.<br/><br/>Eftersom typ information redan är tillgänglig för strukturerade data källor bör du inte ta med typ information när du inkluderar avsnittet struktur.
- **För data källor utan/svaga scheman, t. ex. text filen i Blob Storage**, inkluderar du en struktur när data uppsättningen är indata för en kopierings aktivitet och data typerna för käll data uppsättningen ska konverteras till interna typer för mottagaren. Och ta med struktur när du vill mappa käll kolumner till Sink-kolumner..

## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa data uppsättningar genom att använda något av dessa verktyg eller SDK: [er: .NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager mall och Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Aktuella versioner jämfört med version 1-datauppsättningar

Här följer några skillnader mellan Data Factory och Data Factory data uppsättningar för version 1:

- Den externa egenskapen stöds inte i den aktuella versionen. Den ersätts av en [](concepts-pipeline-execution-triggers.md)utlösare.
- Egenskaperna policy och Availability stöds inte i den aktuella versionen. Start tiden för en pipeline beror på utlösare. [](concepts-pipeline-execution-triggers.md)
- Data uppsättningar som har definierats i en pipeline stöds inte i den aktuella versionen.

## <a name="next-steps"></a>Nästa steg
I följande självstudie finns stegvisa anvisningar för hur du skapar pipelines och data uppsättningar med hjälp av något av dessa verktyg eller SDK: er.

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Snabb start: skapa en data fabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md)
- [Snabb start: skapa en data fabrik med hjälp av REST API](quickstart-create-data-factory-rest-api.md)
- [Snabb start: skapa en data fabrik med hjälp av Azure Portal](quickstart-create-data-factory-portal.md)
