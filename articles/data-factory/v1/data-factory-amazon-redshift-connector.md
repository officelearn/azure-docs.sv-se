---
title: Flytta data från Amazon Redshift med hjälp av Azure Data Factory
description: Lär dig hur du flyttar data från Amazon Redshift med hjälp av Azure Data Factory Copy Activity.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c2e2394bbcee5294bfb752a0af2969457ffff0ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260532"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Flytta data från Amazon Redshift med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-amazon-redshift-connector.md)
> * [Version 2 (aktuell version)](../connector-amazon-redshift.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Amazon Redshift-anslutning i V2](../connector-amazon-redshift.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från Amazon Redshift. Artikeln bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Data Factory stöder för närvarande endast flytta data från Amazon Redshift till ett [diskbänksdatalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Det går inte att flytta data från andra datalager till Amazon Redshift.

> [!TIP]
> För att uppnå bästa prestanda när du kopierar stora mängder data från Amazon Redshift, överväga att använda den inbyggda Redshift **LOSS** kommandot via Amazon Simple Storage Service (Amazon S3). Mer information finns i [Använd TA BORT för att kopiera data från Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Krav
* Om du flyttar data till ett lokalt datalager installerar du [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal dator. Bevilja åtkomst för en gateway till Amazon Redshift-klustret med hjälp av den lokala datorns IP-adress. Instruktioner finns i [Auktorisera åtkomst till klustret](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Information om hur du flyttar data till ett Azure-datalager finns i [beräknings-IP-adressen och SQL-intervallen som används av Microsoft Azure Datacenters](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet för att flytta data från en Amazon Redshift-källa med hjälp av olika verktyg och API:er.

Det enklaste sättet att skapa en pipeline är att använda Azure Data Factory Copy Wizard. En snabb genomgång av hur du skapar en pipeline med hjälp av kopieringsguiden finns i [självstudiekursen: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md).

Du kan också skapa en pipeline med hjälp av Visual Studio, Azure PowerShell eller andra verktyg. Azure Resource Manager-mallar, .NET API eller REST API kan också användas för att skapa pipelinen. Steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet finns i [självstudien Kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa länkade tjänster för att länka in- och utdatalager till datafabriken.
2. Skapa datauppsättningar för att representera in- och utdata för kopieringen.
3. Skapa en pipeline med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder kopieringsguiden skapas JSON-definitioner för dessa datafabrikentiteter automatiskt. När du använder verktyg eller API:er (förutom .NET API) definierar du datafabrikentiteterna med hjälp av JSON-formatet. JSON-exemplet: Kopiera data från Amazon Redshift till Azure Blob-lagring visar JSON-definitionerna för datafabrikens entiteter som används för att kopiera data från ett Amazon Redshift-datalager.

I följande avsnitt beskrivs de JSON-egenskaper som används för att definiera datafabrikens entiteter för Amazon Redshift.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande tabell innehåller beskrivningar för JSON-element som är specifika för en Amazon Redshift-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **Typ** |Denna egenskap måste ställas in på **AmazonRedshift**. |Ja |
| **Server** |IP-adressen eller värdnamnet på Amazon Redshift-servern. |Ja |
| **Port** |Numret på TCP-porten som Amazon Redshift-servern använder för att lyssna efter klientanslutningar. |Nej (standard är 5439) |
| **Databas** |Namnet på Amazon Redshift-databasen. |Ja |
| **Användarnamn** |Namnet på den användare som har åtkomst till databasen. |Ja |
| **lösenord** |Lösenordet för användarkontot. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd

En lista över de avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitten **struktur,** **tillgänglighet**och **princip** är likartade för alla datauppsättningstyper. Exempel på datauppsättningstyper är Azure SQL, Azure Blob storage och Azure Table storage.

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i butiken. **Avsnittet typeProperties** för en datauppsättning av typen **RelationalTable**, som innehåller Amazon Redshift-datauppsättningen, har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **Tablename** |Namnet på tabellen i Amazon Redshift-databasen som den länkade tjänsten refererar till. |Nej (om **query** frågeegenskapen för en kopieringsaktivitet av typen **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) **Namn,** **beskrivning,** **indatatabell,** **utdatatabell** och **principegenskaper** är tillgängliga för alla typer av aktiviteter. Vilka egenskaper som är tillgängliga i avsnittet **typeProperties** varierar för varje aktivitetstyp. För Kopieringsaktivitet varierar egenskaperna beroende på vilka typer av datakällor och diskhoar.

För kopieringsaktivitet, när källan är av typen **AmazonRedshiftSource**, är följande egenskaper tillgängliga i **avsnittet typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **Fråga** | Använd den anpassade frågan för att läsa data. |Nej (om egenskapen **tableName** för en datauppsättning har angetts) |
| **redshiftUnloadSettings** | Innehåller egenskapsgruppen när du använder kommandot Redshift **UNLOAD.** | Inga |
| **s3LinkedServiceName** | Amazon S3 att använda som en tillfällig butik. Den länkade tjänsten anges med hjälp av ett Azure Data Factory-namn av typen **AwsAccessKey**. | Krävs när du använder egenskapen **redshiftUnloadSettings** |
| **bucketName (bucketName)** | Anger Amazon S3-bucketen som ska användas för att lagra interimsdata. Om den här egenskapen inte anges genererar kopiera aktivitet automatiskt en bucket. | Krävs när du använder egenskapen **redshiftUnloadSettings** |

Du kan också använda typen **RelationalSource,** som inkluderar Amazon Redshift, med följande egenskap i avsnittet **typeProperties.** Observera att den här källtypen inte stöder kommandot Redshift **UNLOAD.**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **Fråga** |Använd den anpassade frågan för att läsa data. | Nej (om egenskapen **tableName** för en datauppsättning har angetts) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Använd TA BORT för att kopiera data från Amazon Redshift

Amazon Redshift [**LOSS-kommandot**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) lossar resultatet av en fråga till en eller flera filer på Amazon S3. Detta kommando rekommenderas av Amazon för att kopiera stora datamängder från Redshift.

**Exempel: Kopiera data från Amazon Redshift till Azure SQL Data Warehouse**

I det här exemplet kopieras data från Amazon Redshift till Azure SQL Data Warehouse. I exemplet används kommandot Redshift **UNLOAD,** mellanlagrade kopieringsdata och Microsoft PolyBase.

För det här exemplets användningsfall tar Copy Activity först bort data från Amazon Redshift till Amazon S3 som konfigurerats i alternativet **redshiftUnloadSettings.** Därefter kopieras data från Amazon S3 till Azure Blob-lagring enligt alternativet **mellanlagringsinställningar.** Slutligen läser PolyBase in data i SQL Data Warehouse. Alla interimsformat hanteras av Copy Activity.

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

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON exempel: Kopiera data från Amazon Redshift till Azure Blob lagring
Det här exemplet visar hur du kopierar data från en Amazon Redshift-databas till Azure Blob Storage. Data kan kopieras direkt till valfri [diskho som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av Kopiera aktivitet.

Exemplet har följande datafabriksenheter:

* En länkad tjänst av typen [AmazonRedshift](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties)
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med en kopieringsaktivitet som använder egenskaperna [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Exemplet kopierar data från en fråga resultera i Amazon Redshift till en Azure blob varje timme. De JSON-egenskaper som används i exemplet beskrivs i de avsnitt som följer entitetsdefinitionerna.

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
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure Blob lagringslänkade tjänst**

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
**Amazon Redshift indatauppsättning**

Den **externa** egenskapen är inställd på "true" för att informera datafabrikstjänsten om att datauppsättningen är extern till datafabriken. Den här egenskapsinställningen anger att datauppsättningen inte produceras av en aktivitet i datafabriken. Ange egenskapen till true på en indatauppsättning som inte produceras av en aktivitet i pipelinen.

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

Data skrivs till en ny blob **frequency** varje timme genom att ange frekvensegenskapen till "Timme" och **intervallegenskapen** till 1. Egenskapen **folderPath** för blobben utvärderas dynamiskt. Egenskapsvärdet baseras på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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

**Kopiera aktivitet i en pipeline med en Azure Redshift-källa (av typen RelationalSource) och en Azure Blob-mottagare**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningarna. Pipelinen är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer de data som ska kopieras från den senaste timmen.

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
### <a name="type-mapping-for-amazon-redshift"></a>Typmappning för Amazon Redshift
Som nämns i artikeln [för dataflyttningsaktiviteter](data-factory-data-movement-activities.md) utför Copy Activity automatiska typkonverteringar från källtyp till sink-typ. Typerna konverteras med hjälp av en tvåstegsmetod:

1. Konvertera från en inbyggd källtyp till en .NET-typ
2. Konvertera från en .NET-typ till en inbyggd sink-typ

Följande mappningar används när Kopiera aktivitet konverterar data från en Amazon Redshift-typ till en .NET-typ:

| Amazon Redshift typ | .NET-typ |
| --- | --- |
| SMALLINT |Int16 (int16) |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Enkel |
| DUBBEL PRECISION |Double |
| Boolean |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättningen till kolumner i sink-datauppsättningen finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Repeterbara läsningar från relationskällor
När du kopierar data från ett relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera **återförsöksprincipen** för en datauppsättning för att köra ett segment igen när ett fel inträffar. Kontrollera att samma data läss, oavsett hur många gånger segmentet körs igen. Kontrollera också att samma data läss oavsett hur du kör segmentet igen. Mer information finns i [Repeterbara läsningar från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Lär dig mer om viktiga faktorer som påverkar prestanda för kopieringsaktivitet och sätt att optimera prestanda i [guiden Kopiera aktivitetsprestanda och justering](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för hur du skapar en pipeline med kopiera aktivitet finns i [självstudiekursen Kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
