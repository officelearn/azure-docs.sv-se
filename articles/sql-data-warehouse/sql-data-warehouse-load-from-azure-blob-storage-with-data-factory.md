<properties
   pageTitle="Läs in data från Azure blobblagring till Azure SQL Data Warehouse (Azure Data Factory) | Microsoft Azure"
   description="Lär dig hur man läser in data med Azure Data Factory"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/29/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Läs in data från Azure blobblagring till Azure SQL Data Warehouse (Azure Data Factory)

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

 De här självstudierna visar hur du skapar en pipeline i Azure Data Factory för att flytta data från Azure Storage Blob till SQL Data Warehouse. Med följande steg kommer du att:

+ Ställa in exempeldata i en Azure Storage Blob.
+ Ansluta resurser till Azure Data Factory.
+ Skapa en pipeline för att flytta data från Storage-blobbar till SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## Innan du börjar

Se [introduktion till Azure Data Factory][] för att bekanta dig med Azure Data Factory.

### Skapa eller identifiera resurser

Innan du påbörjar de här självstudierna behöver du följande resurser.

   + **Azure Storage Blob**: självstudierna använder Azure Storage Blob som datakälla för Azure Data Factory-pipelinen. Du behöver därmed ha en tillgänglig för att lagra exempeldata. Om du inte har ett redan, kan du lära dig hur du [skapar ett lagringskonto][].

   + **SQL Data Warehouse**: i självstudierna flyttas data från Azure Storage Blob till SQL Data Warehouse och du behöver därmed ha ett Data Warehouse online där du har läst in AdventureWorksDW-exempeldata. Om du inte redan har ett Data Warehouse, kan du lära dig hur du [etablerar ett][Skapa ett SQL Data Warehouse]. Om du har ett Data Warehouse men inte har etablerat det med exempeldata, kan du [läsa in det manuellt][läs in exempeldata i SQL Data Warehouse].

   + **Azure Data Factory**: Azure Data Factory kommer att genomföra den faktiska belastningen så du behöver ha en som du kan använda för att skapa pipelinen för dataflytt. Om du inte redan har en, kan du lära dig hur man skapar en i steg 1 av [Kom igång med Azure Data Factory (Data Factory-redigeraren)][].

   + **AZCopy**: du behöver AZCopy för att kopiera exempeldata från din lokala klient till din Azure Storage Blob. För installationsanvisningar kan du se [AZCopy-dokumentationen][].

## Steg 1: Kopiera exempeldata till Azure Storage Blob.

När du var har alla bitar på plats så är du redo för att kopiera exempeldata till din Azure Storage-Blob.

1. [Hämta exempeldata][]. Dessa data lägger till ytterligare tre års säljdata till dina AdventureWorksDW-exempeldata.

2. Använd det här AZCopy-kommandot för att kopiera tre års data till din Azure Storage Blob.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## Steg 2: Anslut resurser till Azure Data Factory.

Nu när du har dina data på plats, kan vi skapa Azure Data Factory-pipelinen för att flytta data från Azure Blob-lagring till SQL Data Warehouse.

Kom igång genom att öppna [Azure-portalen][] och välj din Data Factory i den vänstra menyn.

### Steg 2.1: Skapa länkad tjänst

Länka ditt Azure-lagringskonto och SQL Data Warehouse till din Data Factory.  

1. Starta först registreringsprocessen genom att klicka på avsnittet Länkade tjänster i din Data Factory och sedan på Nytt datalager. Välj ett namn att registrera ditt Azure Storage under, välj Azure Storage som typ och ange sedan ditt kontonamn och din kontonyckel.

2. För att registrera SQL Data Warehouse, går du till avsnittet Författare och distribution, väljer Nytt datalager och sedan Azure SQL Data Warehouse. Kopiera och klistra in den här mallen och fyll sedan i din specifika information.

```JSON
{
    "name": "<Linked Service Name>",
    "properties": {
        "description": "",
        "type": "AzureSqlDW",
        "typeProperties": {
             "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
         }
    }
}
```

### Steg 2.2: Definiera datauppsättningen

När du har skapat de länkade tjänsterna, behöver vi definiera datauppsättningarna.  I det här fallet innebär det att definiera strukturen på de data som flyttas från ditt lager till ditt Data Warehouse.  Läsa mer om hur du skapar

1. Starta processen genom att gå till avsnittet Författare och distribution i din Data Factory.

2. Klicka på Ny datauppsättning och sedan på Azure Blob-lagring för att länka ditt lagringsutrymme till din Data Factory.  Du kan använda skriptet nedan för att definiera dina data i Azure Blob Storage:

```JSON
{
    "name": "<Dataset Name>",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "<linked storage name>",
        "typeProperties": {
            "folderPath": "<containter name>",
            "fileName": "FactInternetSales.csv",
            "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```


3. Nu ska vi också definiera datauppsättningarna för SQL Data Warehouse.  Vi börjar på samma sätt, genom att klicka på Ny datauppsättning och sedan på Azure SQL Data Warehouse.

```JSON
{
    "name": "DWDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "FactInternetSales"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

## Steg 3: Skapa och kör din pipeline

Slutligen ska vi ställa in och köra pipelinen i Azure Data Factory.  Det här är åtgärden som kommer att slutföra den faktiska dataflytten.  Du hittar en fullständig överblick över åtgärderna som du kan utföra med SQL Data Warehouse och Azure Data Factory [här][Flytta data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory].

I avsnittet Författare och distribution klickar du nu på Fler kommandon och Ny pipeline.  Efter att du skapat din pipeline, kan du använda koden nedan för att överföra data till ditt Data Warehouse:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
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
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## Nästa steg

Lär dig mer genom att se:

- [Azure Data Factory-utbildningsväg][].
- [Azure SQL Data Warehouse-anslutningsapp][]. Det här är det grundläggande referensämnet för att använda Azure Data Factory med Azure SQL Data Warehouse.


De här ämnena ger detaljerad information om Azure Data Factory. De behandlar Azure SQL Database eller HDinsight, men informationen är också relevant för Azure SQL Data Warehouse.

- [Självstudier: Kom igång med Azure Data Factory][] Det här är de grundläggande självstudierna för databearbetning med Azure Data Factory. I de här självstudierna skapar du din första pipeline som använder sig av HDInsight för att transformera och analysera webbloggar på månatlig basis. Observera att det inte sker några kopieringsaktiviteter i självstudierna.
- [Självstudier: Kopiera data från Azure Storage Blob till Azure SQL Database][]. I de här självstudierna skapar du en pipeline i Azure Data Factory för att kopiera data från Azure Storage Blob till Azure SQL Database.
- [Självstudier för ett verkligt scenario][]. Det här är detaljerade självstudier för användning av Azure Data Factory.

<!--Image references-->

<!--Article references-->
[AZCopy-dokumentationen]: ../storage/storage-use-azcopy.md
[Azure SQL Data Warehouse-anslutningsapp]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Skapa ett SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[skapar ett lagringskonto]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Kom igång med Azure Data Factory (Data Factory-redigeraren)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[introduktion till Azure Data Factory]: ../data-factory/data-factory-introduction.md
[läs in exempeldata i SQL Data Warehouse]: sql-data-warehouse-get-started-manually-load-samples.md
[Flytta data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Självstudier för ett verkligt scenario]: ../data-factory/data-factory-tutorial.md
[Självstudier: Kopiera data från Azure Storage Blob till Azure SQL Database]: ../data-factory/data-factory-get-started
[Självstudier: Kom igång med Azure Data Factory]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory-utbildningsväg]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure-portalen]: https://portal.azure.com
[Hämta exempeldata]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv



<!--HONumber=Jun16_HO2-->


