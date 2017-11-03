---
title: "Flytta data från Amazon Redshift med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig mer om att flytta data från Amazon Redshift med hjälp av Azure Data Factory-Kopieringsaktiviteten."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d423304c84bd03477f5e9ee2edb4763e2ae8d5b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Flytta data från Amazon Redshift med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-amazon-redshift-connector.md)
> * [Version 2 – förhandsversion](../connector-amazon-redshift.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Amazon Redshift connector i V2](../connector-amazon-redshift.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från Amazon Redshift. Artikeln bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet. 

Data Factory stöder för närvarande endast flytta data från Amazon Redshift till en [stöds sink datalagret](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Flytta data från andra dataarkiv till Amazon Redshift stöds inte.

> [!TIP]
> Om du vill uppnå bästa prestanda vid kopiering av stora mängder data från Amazon Redshift, Överväg att använda den inbyggda Redshift **UNLOAD** kommandot via Amazon enkla Storage-tjänst (Amazon S3). Mer information finns i [Använd INAKTIVERAS för att kopiera data från Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Krav
* Om du flyttar data till ett lokalt datalager, installera [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal dator. Bevilja åtkomst för en gateway i Amazon Redshift klustret med hjälp av den lokala dator IP-adressen. Instruktioner finns i [auktorisera åtkomst till klustret](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Om du vill flytta data till ett Azure datalager, finns det [Compute IP-adress och SQL-adressintervall som används av Microsoft Azure-Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet för att flytta data från en källa för Amazon Redshift med hjälp av olika verktyg och API: er.

Det enklaste sättet att skapa en pipeline är att använda guiden Kopiera Azure Data Factory. En snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera finns i [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också skapa en pipeline med hjälp av Azure portal, Visual Studio, Azure PowerShell eller andra verktyg. Azure Resource Manager-mallar, .NET-API: et eller REST API kan också användas för att skapa pipelinen. Stegvisa instruktioner för att skapa en pipeline med en kopieringsaktiviteten, finns det [Kopieringsaktiviteten kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare: 

1. Skapa länkade tjänster du vill länka indata och utdata datalager till din data factory.
2. Skapa datauppsättningar som representerar inkommande och utgående data för kopieringen. 
3. Skapa en pipeline med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden Kopiera skapas JSON definitioner för dessa Data Factory-enheter automatiskt. När du använder verktyg eller API: er (utom .NET-API) kan definiera du Data Factory-enheter med hjälp av JSON-format. Den [JSON-exempel: kopiera data från Amazon Redshift till Azure Blob storage](#json-example-copy-data-from-amazon-redshift-to-azure-blob) visar JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett Amazon Redshift dataarkiv.

I följande avsnitt beskrivs de JSON-egenskaper som används för att definiera Data Factory-entiteter för Amazon Redshift.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande tabell innehåller beskrivningar för JSON-element som är specifika för en Amazon Redshift länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **typ** |Den här egenskapen måste anges till **AmazonRedshift**. |Ja |
| **Server** |IP-adressen eller värdnamnet namnet på Amazon Redshift-server. |Ja |
| **port** |Antalet TCP-porten som Amazon Redshift-servern använder för att lyssna efter anslutningar. |Nej (standard är 5439) |
| **databasen** |Namnet på Amazon Redshift-databasen. |Ja |
| **användarnamn** |Namnet på den användare som har åtkomst till databasen. |Ja |
| **lösenord** |Lösenordet för användarkontot. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd

En lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar, finns det [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Den **struktur**, **tillgänglighet**, och **princip** avsnitt är liknande för alla typer av datauppsättningen. Exempel på dataset typer är Azure SQL Azure Blob storage och Azure Table storage.

Den **typeProperties** avsnitt är olika för varje typ av datauppsättningen och innehåller information om placeringen av data i arkivet. **TypeProperties** avsnittet för en dataset av typen **RelationalTable**, som innehåller datauppsättningen Amazon Redshift har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **tableName** |Namnet på tabellen i databasen Amazon Redshift som den länkade tjänsten refererar till. |Nej (om den **frågan** -egenskapen för en kopia aktivitet av typen **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Den **namn**, **beskrivning**, **indata** tabellen **matar ut** tabellen och **princip** egenskaper är tillgängligt för alla typer av aktiviteter. De egenskaper som är tillgängliga i den **typeProperties** avsnittet varierar för varje aktivitetstyp. För Kopieringsaktivitet kan variera egenskaperna beroende på vilka typer av datakällor och sänkor.

För Kopieringsaktiviteten när källan är av typen **AmazonRedshiftSource**, följande egenskaper är tillgängliga i **typeProperties** avsnitt:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **frågan** | Använd anpassad fråga för att läsa data. |Nej (om den **tableName** -egenskapen för en dataset har angetts) |
| **redshiftUnloadSettings** | Innehåller Egenskapsgruppen när du använder Redshift **UNLOAD** kommando. | Nej |
| **s3LinkedServiceName** | Amazon S3 ska användas som en mellanliggande arkivet. Den länkade tjänsten anges med ett Azure Data Factory-namn för typen **AwsAccessKey**. | Krävs när du använder den **redshiftUnloadSettings** egenskapen |
| **bucketName** | Anger Amazon S3-bucket ska lagras tillfälligt data. Om den här egenskapen inte anges, genererar Kopieringsaktiviteten auto-en bucket. | Krävs när du använder den **redshiftUnloadSettings** egenskapen |

Du kan också använda den **RelationalSource** typ, som inkluderar Amazon Redshift med följande egenskap i den **typeProperties** avsnitt. Obs den här typen av datakälla inte stöder Redshift **UNLOAD** kommando.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **frågan** |Använd anpassad fråga för att läsa data. | Nej (om den **tableName** -egenskapen för en dataset har angetts) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Använd ta bort för att kopiera data från Amazon Redshift

Amazon Redshift [ **UNLOAD** ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) kommando inaktiverar resultatet av en fråga till en eller flera filer på Amazon S3. Det här kommandot rekommenderas av Amazon för kopiering av stora datamängder från Redshift.

**Exempel: Kopiera data från Amazon Redshift till Azure SQL Data Warehouse**

Det här exemplet kopierar data från Amazon Redshift till Azure SQL Data Warehouse. I exemplet används Redshift **UNLOAD** kommandot mellanlagrade kopieringsdata och Microsoft PolyBase.

För det här exemplet används, Kopieringsaktiviteten först tar bort data från Amazon Redshift till Amazon S3 som konfigurerats i den **redshiftUnloadSettings** alternativet. Därefter kopieras data från Amazon S3 till Azure Blob storage som anges i den **stagingSettings** alternativet. Slutligen läser PolyBase data i SQL Data Warehouse. Alla mellanliggande format hanteras av Kopieringsaktiviteten.

![Kopiera arbetsflödet från Amazon Redshift till SQL Data Warehouse](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-exempel: kopiera data från Amazon Redshift till Azure Blob storage
Det här exemplet visas hur du kopierar data från en Amazon Redshift databas till Azure Blob Storage. Datan kan kopieras till någon [stöds sink](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopieringsaktiviteten.  

Exemplet har följande data factory enheter:

* En länkad tjänst av typen [AmazonRedshift](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [dataset](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties)
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med en kopia-aktivitet som använder den [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) egenskaper

Exemplet kopierar data från ett frågeresultat i Amazon Redshift till en Azure blob varje timme. JSON-egenskaper som används i samplet som beskrivs i avsnitten som följer entitet definitioner.

**Amazon Redshift länkad tjänst**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure Blob länkade lagringstjänsten**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Amazon Redshift inkommande dataset**

Den **externa** egenskap är inställd på ”true” för att informera Data Factory-tjänsten att datamängden är extern till datafabriken. Inställningen anger datauppsättningen inte skapas av en aktivitet i datafabriken. Ange egenskapen till true för en inkommande datauppsättningen som inte tillverkas av en aktivitet i pipelinen.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob utdatauppsättningen**

Data skrivs till en ny blob varje timme genom att ange den **frekvens** egenskapen till ”timme” och **intervall** egenskap till 1. Den **folderPath** -egenskapen för blob utvärderas dynamiskt. Egenskapens värde baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med en Azure Redshift källa (av typen RelationalSource) och ett Azure Blob-mottagare**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda datamängder inkommande och utgående. Pipeline är schemalagd att köras varje timme. I JSON-definitionen för pipelinen, den **källa** är inställd på **RelationalSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **frågan** egenskapen väljer vilka data att kopiera från den senaste timmen.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mappning för Amazon Redshift
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikeln Kopieringsaktiviteten utför automatisk konverteringar från källtyp till mottagare för typen. Typerna konverteras med hjälp av en metod i två steg:

1. Konvertera från en inbyggd typ till en .NET-typ
2. Konvertera från en .NET-typ till en intern Mottagartypen

Följande mappningar används när Kopieringsaktiviteten konverterar data från en Amazon Redshift-typ till en .NET-typ:

| Amazon Redshift typ | .NET-typ |
| --- | --- |
| SMALLINT |Int16 |
| HELTAL |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| VERKLIG |Enskild |
| DUBBEL PRECISION |dubbla |
| BOOLESKT VÄRDE |Sträng |
| CHAR |Sträng |
| VARCHAR |Sträng |
| DATUM |Datum och tid |
| TIDSSTÄMPEL |Datum och tid |
| TEXT |Sträng |

## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Information om hur du mappar kolumner i datauppsättning för källan till kolumner i datauppsättning för sink finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Repeterbara läsningar från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när du kopierar data från en relationsdatabas. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera den här gången **princip** för en dataset att köra ett segment när ett fel uppstår. Se till att samma data läses, oavsett hur många gånger sektorn körs. Kontrollera också att samma data läses oavsett hur du köra sektorn. Mer information finns i [Repeatable läser från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Lär dig mer om viktiga faktorer som påverkar prestanda för Kopieringsaktiviteten och sätt att optimera prestandan i den [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md). 

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att skapa en pipeline med Kopieringsaktiviteten finns i [Kopieringsaktiviteten kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
