---
title: Flytta data från Amazon Redshift med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig mer om att flytta data från Amazon Redshift med hjälp av Azure Data Factory Kopieringsaktivitet.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ee0cd90b8d1b901f9e8a506674b3f04167b48899
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968791"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Flytta data från Amazon Redshift med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-amazon-redshift-connector.md)
> * [Version 2 (aktuell version)](../connector-amazon-redshift.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Amazon Redshift-anslutningsappen i V2](../connector-amazon-redshift.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från Amazon Redshift. Artikeln bygger vidare på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten. 

Data Factory stöder för närvarande endast flyttar data från Amazon Redshift till en [mottagarens datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Flytta data från andra datalager till Amazon Redshift stöds inte.

> [!TIP]
> För att uppnå bästa möjliga prestanda vid kopiering av stora mängder data från Amazon Redshift, Överväg att använda den inbyggda Redshift **INAKTIVERAS** kommandot via Amazon Simple Storage Service (Amazon S3). Mer information finns i [Använd INAKTIVERAS för att kopiera data från Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Förutsättningar
* Om du flyttar data till ett lokalt datalager, installera [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal dator. Bevilja åtkomst för en gateway till Amazon Redshift-klustret med hjälp av IP-adressen för den lokala datorn. Anvisningar finns i [auktorisera åtkomst till klustret](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* För att flytta data till en Azure data store, ser de [Compute IP-adress och SQL-intervall som används av Microsoft Azure-datacentren](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet för att flytta data från en Amazon Redshift-källan med hjälp av olika verktyg och API: er.

Det enklaste sättet att skapa en pipeline är att använda Azure Data Factory-Kopieringsguiden. En snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera finns i den [självstudie: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också skapa en pipeline med hjälp av Azure portal, Visual Studio, Azure PowerShell eller andra verktyg. Azure Resource Manager-mallar, .NET-API eller REST API kan också användas för att skapa pipelinen. Stegvisa anvisningar om hur du skapar en pipeline med en Kopieringsaktivitet finns i den [Kopieringsaktiviteten självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager: 

1. Skapa länkade tjänster för att länka indata och utdata datalager till datafabriken.
2. Skapa datauppsättningar som representerar inkommande och utgående data för kopieringen. 
3. Skapa en pipeline med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden Kopiera skapas automatiskt JSON-definitioner för dessa Data Factory-entiteter. När du använder verktyg eller API: er (med undantag för .NET-API) kan definiera du Data Factory-entiteter med hjälp av JSON-format. Den [JSON-exempel: kopiera data från Amazon Redshift till Azure Blob storage](#json-example-copy-data-from-amazon-redshift-to-azure-blob) visar JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett datalager för Amazon Redshift.

I följande avsnitt beskrivs de JSON-egenskaper som används för att definiera Data Factory-entiteter för Amazon Redshift.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

I följande tabell innehåller beskrivningar av JSON-element som är specifika för en Amazon Redshift länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| **typ** |Den här egenskapen måste anges till **AmazonRedshift**. |Ja |
| **Server** |IP-adressen eller värdnamnet namnet på Amazon Redshift-servern. |Ja |
| **Port** |Antalet TCP-porten som Amazon Redshift-servern använder för att lyssna efter klientanslutningar. |Nej (standard är 5439) |
| **databasen** |Namnet på Amazon Redshift-databas. |Ja |
| **användarnamn** |Namnet på den användare som har åtkomst till databasen. |Ja |
| **Lösenord** |Lösenordet för användarkontot. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd

En lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Den **struktur**, **tillgänglighet**, och **princip** avsnitt är liknande för alla datauppsättningstyper av. Exempel på datauppsättningstyperna är Azure SQL, Azure Blob storage och Azure Table storage.

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i arkivet. **TypeProperties** avsnittet för en datauppsättning av typen **RelationalTable**, vilket inkluderar Amazon Redshift-datamängd har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| **Tabellnamn** |Namnet på tabellen i Amazon Redshift-databas som den länkade tjänsten refererar till. |Nej (om den **fråga** egenskapen för en Kopieringsaktivitet av typen **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Den **namn**, **beskrivning**, **indata** tabellen **matar ut** tabellen, och **princip** egenskaper är tillgängligt för alla typer av aktiviteter. De egenskaper som är tillgängliga i den **typeProperties** avsnittet variera för varje aktivitetstyp av. Egenskaperna varierar beroende på vilka typer av datakällor och mottagare för Kopieringsaktiviteten.

För Kopieringsaktiviteten, när källan är av typen **AmazonRedshiftSource**, följande egenskaper är tillgängliga i **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| **Fråga** | Använd anpassad fråga för att läsa data. |Nej (om den **tableName** egenskapen för en datauppsättning som har angetts) |
| **redshiftUnloadSettings** | Innehåller Egenskapsgruppen när du använder Redshift **INAKTIVERAS** kommando. | Nej |
| **s3LinkedServiceName** | Amazon S3 ska användas som en mellanliggande butik. Den länkade tjänsten har angetts med hjälp av ett Azure Data Factory-namn av typen **AwsAccessKey**. | Krävs när du använder den **redshiftUnloadSettings** egenskap |
| **bucketName** | Anger Amazon S3-bucket för att använda för att lagra tillfälliga data. Om den här egenskapen inte anges, genererar Kopieringsaktivitet auto-en bucket. | Krävs när du använder den **redshiftUnloadSettings** egenskap |

Du kan också använda den **RelationalSource** typ, som inkluderar Amazon Redshift, med följande egenskap i den **typeProperties** avsnittet. Obs den här typ av datakälla inte stöder Redshift **INAKTIVERAS** kommando.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| **Fråga** |Använd anpassad fråga för att läsa data. | Nej (om den **tableName** egenskapen för en datauppsättning som har angetts) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Använd ta bort för att kopiera data från Amazon Redshift

Amazon Redshift [ **INAKTIVERAS** ](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) kommando inaktiverar resultatet av en fråga till en eller flera filer på Amazon S3. Det här kommandot rekommenderas av Amazon för att kopiera stora datamängder från Redshift.

**Exempel: Kopiera data från Amazon Redshift till Azure SQL Data Warehouse**

Det här exemplet kopierar data från Amazon Redshift till Azure SQL Data Warehouse. I exemplet används Redshift **INAKTIVERAS** kommandot, mellanlagrad kopieringsdata och Microsoft PolyBase.

För det här exemplet, Kopieringsaktivitet först tar bort data från Amazon Redshift till Amazon S3 som konfigurerats i den **redshiftUnloadSettings** alternativet. Därefter data kopieras från Amazon S3 till Azure Blob storage som anges i den **stagingSettings** alternativet. PolyBase läser slutligen in data till SQL Data Warehouse. Alla mellanliggande format hanteras av Kopieringsaktivitet.

![Kopiera arbetsflöde från Amazon Redshift till SQL Data Warehouse](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

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
Detta exempel visar hur du kopierar data från en Amazon Redshift-databas till Azure Blob Storage. Datan kan kopieras direkt till någon [stöds mottagare](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopieringsaktivitet.  

Exemplet har följande data factory-entiteter:

* En länkad tjänst av typen [AmazonRedshift](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties)
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder den [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) egenskaper

Exemplet kopierar data från ett frågeresultat i Amazon Redshift till en Azure-blob per timme. JSON-egenskaper som används i exemplet beskrivs i avsnitten som följer entitetsdefinitioner för.

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

**Azure Blob storage-länkade tjänst**

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
**Amazon Redshift datauppsättningen för indata**

Den **externa** egenskapen anges som ”true” om Data Factory-tjänsten att datauppsättningen är extern till datafabriken. Inställningen anger att datauppsättningen inte tillverkas av en aktivitet i data factory. Ange egenskapen till true på en inkommande datamängd som inte kommer från en aktivitet i pipelinen.

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

**Utdatauppsättning för Azure-blob**

Data skrivs till en ny blob varje timme genom att ange den **frekvens** egenskap ”Hour” och **intervall** egenskapen till 1. Den **folderPath** -egenskapen för blob utvärderas dynamiskt. Egenskapsvärdet baseras på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

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

**Kopiera aktivitet i en pipeline med en Azure-Redshift-källan (av typen RelationalSource) och en Azure Blob-mottagaren**

Det innehåller en Kopieringsaktivitet som är konfigurerad för att använda uppsättningar för indata och utdata för pipelinen. Pipelinen är schemalagd att köras varje timme. I JSON-definition för pipelinen den **källa** är **RelationalSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **fråga** egenskapen väljer data som ska kopieras från den senaste timmen.

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
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel, Kopieringsaktivitet utför automatisk konverteringar från källtyp till typen för mottagare. Typerna konverteras med hjälp av en metod i två steg:

1. Konvertera från en typ av interna datakälla till en .NET-typ
2. Konvertera från en .NET-typ till en intern mottagare

Följande mappningar används när Kopieringsaktiviteten konverterar data från en Amazon Redshift-typ till en .NET-typ:

| Amazon Redshift-typ | .NET-typ |
| --- | --- |
| SMALLINT |Int16 |
| HELTAL |Int32 |
| BIGINT |Int64 |
| DECIMALTAL |decimaltal |
| VERKLIGA |Enkel |
| DUBBEL PRECISION |Double-värde |
| BOOLESKT VÄRDE |Sträng |
| CHAR |Sträng |
| VARCHAR |Sträng |
| DATE |DateTime |
| TIDSSTÄMPEL |DateTime |
| TEXT |Sträng |

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Om du vill lära dig mer om att mappa kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Repeterbara läsningar från relationella källor
När du kopierar data från en relationsdatabas, Tänk repeterbarhet att undvika oväntade resultat. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsök **princip** för en datauppsättning för att köra en sektor när ett fel uppstår. Se till att samma data läses, oavsett hur många gånger sektorn ska köras på nytt. Kontrollera också att samma data är läst oavsett hur du vill köra sektorn. Mer information finns i [Repeatable läser från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Lär dig mer om viktiga faktorer som påverkar prestanda för Kopieringsaktiviteten och sätt att optimera prestanda i den [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md). 

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att skapa en pipeline med Kopieringsaktivitet finns i den [Kopieringsaktiviteten självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
