---
title: Skapa datapipelines med Azure Data Factory | Microsoft Docs
description: Beskriver hur du skapar skapa förutsägande pipelines med Azure Data Factory och Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6c71956bf43a0147d5abff4b708be93f62fe34fa
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545069"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Skapa förutsägande pipelines med Azure Machine Learning och Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Piggningsåtgärd](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduktion
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Transformera data med maskininlärning i Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) gör att du kan skapa, testa och distribuera lösningar för förutsägelseanalys. Från en övergripande synsätt, är det klart i tre steg:

1. **Skapa ett träningsexperiment**. Du kan göra det här steget med hjälp av Azure Machine Learning studio. Azure Machine Learning studio är en gemensam visual utvecklingsmiljö som används för att träna och testa en modell för förutsägelseanalys med hjälp av träningsdata.
2. **Konvertera den till ett förutsägelseexperiment**. När din modell har tränats med befintliga data och du är redo att använda den för att samla in nya data, förbereda och effektivisera experimentet för bedömning.
3. **Distribuera den som en webbtjänst**. Du kan publicera ditt bedömnings experiment som en Azure-webbtjänst. Du kan skicka data till din modell via den här web service-slutpunkt och ta emot resultatet förutsägelser från modellen.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar **förflyttning** och **transformering** av data. Du kan skapa dataintegrationslösningar med hjälp av Azure Data Factory som kan mata in data från olika datalager, transformera/bearbeta data och publicera Resultatdata till datalager.

Med Data Factory-tjänsten kan du skapa datapipelines som flyttar och transformerar data och kör pipelines enligt ett angivet schema (varje timme, varje dag, varje vecka osv.). Den innehåller också omfattande visualiseringar för att visa härkomst och beroenden mellan dina datapipelines och övervaka alla datapipelines från en enda enhetlig vy för att enkelt hitta problem och konfigurera övervakningsaviseringar.

Se [introduktion till Azure Data Factory](data-factory-introduction.md) och [skapa din första pipeline](data-factory-build-your-first-pipeline.md) artiklar för att snabbt komma igång med Azure Data Factory-tjänsten.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory och Machine Learning tillsammans
Azure Data Factory kan du enkelt skapa pipelines som använder en publiceringswebbkod [Azure Machine Learning] [ azure-machine-learning] webbtjänsten för förutsägande analys. Med hjälp av den **Batchkörningsaktivitet** i en Azure Data Factory-pipeline kan du anropa en Azure Machine Learning studio-webbtjänst för att göra förutsägelser på data i batch. Se aktivering en Azure Machine Learning studio-webbtjänst med hjälp av avsnittet Batch-Körningsaktivitet för information.

Framöver kommer måste förutsägande modeller i Azure Machine Learning studio bedömning experiment vara modellkomponenten med hjälp av nya indatauppsättningar. Du kan träna om en Azure Machine Learning studio-modell från Data Factory-pipeline genom att göra följande:

1. Publicera träningsexperiment (inte förutsägelseexperiment) som en webbtjänst. Du kan göra det här steget i Azure Machine Learning studio som du gjorde för att exponera förutsägelseexperiment som en webbtjänst i scenariot ovan.
2. Använda Azure Machine Learning studio-Batchkörningsaktivitet för att anropa webbtjänsten för träningsexperimentet. I princip kan du använda Azure Machine Learning studio-batchkörningsaktivitet för att anropa både webbtjänst för utbildning och bedömning av webbtjänsten.

När du är klar med att träna, uppdatera bedömning av webbtjänsten (förutsägelseexperiment visas som en webbtjänst) med den nyligen tränade modellen med hjälp av den **Azure Machine Learning studio aktivitet uppdatera resurs**. Se [uppdaterar modeller med Uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md) nedan för information.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Anropa en webbtjänst med hjälp av Batch-Körningsaktivitet
Du använder Azure Data Factory för att samordna dataförflyttning och bearbetning och utför sedan batchkörning med hjälp av Azure Machine Learning. Här är de högsta steg:

1. Skapa en Azure Machine Learning-länkad tjänst. Behöver du följande värden:

   1. **Begärande-URI** för batchkörning API. Du kan hitta begäran-URI genom att klicka på den **BATCHKÖRNING** länk på webbsidan för tjänster.
   2. **API-nyckel** publicerade Azure Machine Learning-webbtjänsten. Du hittar API-nyckeln genom att klicka på den webbtjänst som du har publicerat.
   3. Använd den **AzureMLBatchExecution** aktivitet.

      ![Machine Learning-instrumentpanel](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario: Experiment med hjälp av Web service indata/utdata som refererar till data i Azure Blob Storage
I det här scenariot, Azure Machine Learning-webbtjänsten gör förutsägelser med data från en fil i Azure blob storage och lagrar de förutsagda resultaten i blob storage. Följande JSON definierar en Data Factory-pipeline med en AzureMLBatchExecution aktivitet. Aktiviteten har datauppsättningen **DecisionTreeInputBlob** som indata och **DecisionTreeResultBlob** som utdata. Den **DecisionTreeInputBlob** skickas som indata till webbtjänsten genom att använda den **webServiceInput** JSON-egenskap. Den **DecisionTreeResultBlob** skickas som utdata till webbtjänsten genom att använda den **webServiceOutputs** JSON-egenskap.

> [!IMPORTANT]
> Om webbtjänsten tar flera inmatningar kan använda den **webServiceInputs** egenskapen istället för att använda **webServiceInput**. Se den [webbtjänst kräver flera inmatningar](#web-service-requires-multiple-inputs) avsnittet ett exempel på hur du använder egenskapen webServiceInputs.
>
> Datauppsättningar som refererar till den **webServiceInput**/**webServiceInputs** och **webServiceOutputs** egenskaper (i  **typeProperties**) måste också inkluderas i aktiviteten **indata** och **matar ut**.
>
> I Azure Machine Learning studio-experiment har webbtjänstindata och utgångsportar och globala parametrar du standardnamnen (”indata1”, ”indata2”) som du kan anpassa. De namn som du använder för webServiceInputs, webServiceOutputs och globalParameters inställningar måste exakt matcha namnen i experiment. Du kan visa nyttolasten i begäran av exemplet på hjälpsidan för batchkörning för din Azure Machine Learning studio-slutpunkt att verifiera förväntade mappningen.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Endast indata och utdata för aktiviteten AzureMLBatchExecution kan skickas som parametrar till webbtjänsten. I ovanstående JSON-kodfragmentet är till exempel DecisionTreeInputBlob indata för aktiviteten AzureMLBatchExecution som skickas som indata till webbtjänsten via webServiceInput-parametern.
>
>

### <a name="example"></a>Exempel
Det här exemplet använder Azure Storage för att lagra både inkommande och utgående data.

Vi rekommenderar att du går igenom den [skapa din första pipeline med Data Factory] [ adf-build-1st-pipeline] självstudiekursen innan du går igenom det här exemplet. Använd Data Factory-Redigeraren för att skapa Data Factory-artefakter (länkade tjänster, datauppsättningar, pipeline) i det här exemplet.

1. Skapa en **länkad tjänst** för din **Azure Storage**. Om inkommande och utgående filer finns i olika lagringskonton, behöver du två länkade tjänster. Här är en JSON-exempel:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Skapa den **inkommande** Azure Data Factory **datauppsättning**. Till skillnad från vissa andra Data Factory-datauppsättningar, måste dessa datauppsättningar innehålla både **folderPath** och **fileName** värden. Du kan använda partitionering för att varje batchkörning (datasektor) att bearbeta eller producera unika inkommande och utgående filer. Du kan behöva innehåller vissa överordnade aktiviteten för att transformera indata till CSV-filformat och placera den i lagringskontot för varje segment. I så fall kan du inte innehåller den **externa** och **externalData** inställningar som visas i följande exempel och din DecisionTreeInputBlob skulle vara datamängd för utdata för en annan aktivitet.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
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

    Inkommande csv-filen måste ha rubrikraden kolumn. Om du använder den **Kopieringsaktiviteten** för att skapa/flytta CSV-filen till blob-lagring, ska du ange egenskapen mottagare **blobWriterAddHeader** till **SANT**. Exempel:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Om csv-filen saknar rubrikrad, kan du se följande fel: **Fel i aktivitet: Felsträng för läsning. Oväntad token: StartObject. Sökvägen '', rad 1, position 1**.
3. Skapa den **utdata** Azure Data Factory **datauppsättning**. Det här exemplet använder partitionering för att skapa en unik Utdatasökväg för varje körning av sektorn. Utan att partitionering, aktiviteten skulle skriva över filen.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Skapa en **länkad tjänst** av typen: **AzureMLLinkedService**, med API-nyckeln och modellera batchkörnings-URL.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Slutligen skapar en pipeline som innehåller en **AzureMLBatchExecution** aktivitet. Vid körning utför pipelinen följande steg:

   1. Hämtar platsen för indatafilen från dina datauppsättningar som indata.
   2. Anropar Azure Machine Learning-batchkörning API
   3. Kopierar batch utförande-utdatan till bloben i datauppsättningen för utdata.

      > [!NOTE]
      > AzureMLBatchExecution aktivitet kan ha noll eller flera in- och en eller flera utdata.
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      Båda **starta** och **slutet** datum och tid måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. Den **slutet** är valfri. Om du inte anger värdet för den **slutet** egenskapen, beräknas det som ”**start + 48 timmar.**” Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen. Se [Referens för JSON-skript](https://msdn.microsoft.com/library/dn835050.aspx) för information om JSON-egenskaper.

      > [!NOTE]
      > Genom att ange indata för AzureMLBatchExecution är aktivitet valfritt.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario: Experiment som använder Reader/Writer moduler för att referera till data i olika lagringsutrymmen
Ett annat vanligt scenario när du skapar Azure Machine Learning studio-experiment är att använda läsare och skrivare moduler. Läsmodulen används för att läsa in data i ett experiment och modulen skrivaren är att spara data från dina experiment. Mer information om läsare och skrivare moduler finns i [läsare](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [skrivaren](https://msdn.microsoft.com/library/azure/dn905984.aspx) ämnen i MSDN Library.

När du använder moduler läsare och skrivare, är det bra att använda en Web service-parameter för varje egenskap i modulerna läsare/skrivare. Dessa webb-parametrar kan du konfigurera värden under körning. Du kan till exempel skapa ett experiment med en modul för läsare som använder en Azure SQL Database: XXX.database.windows.net. När webbtjänsten har distribuerats, som du vill aktivera konsumenter för webbtjänsten att ange en annan Azure SQL-Server som kallas YYY.database.windows.net. Du kan använda en Web service-parameter så att det här värdet som ska konfigureras.

> [!NOTE]
> Skiljer sig från webbtjänstparametrar Web service indata och utdata. I det första scenariot har du sett hur indata och utdata kan anges för en Azure Machine Learning studio-webbtjänst. I det här scenariot kan skicka du parametrar för en webbtjänst som motsvarar till egenskaperna för läsare/skrivare moduler.
>
>

Låt oss titta på ett scenario för att använda webbtjänstparametrar. Du har en distribuerad Azure Machine Learning-webbtjänst som använder en modul för dataläsare för att läsa data från en av de datakällor som stöds av Azure Machine Learning (till exempel: Azure SQL Database). När batchkörningen utförs, skrivs resultaten med hjälp av en skrivarmodul (Azure SQL Database).  Inga web service indata och utdata har definierats i experiment. I det här fallet rekommenderar vi att du konfigurerar relevanta webbtjänstparametrar för läsare och skrivare moduler. Den här konfigurationen gör att läsaren/skrivaren moduler konfigureras när du använder aktiviteten AzureMLBatchExecution. Du anger webbtjänstparametrar i den **globalParameters** avsnittet i JSON-aktiviteten på följande sätt.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Du kan också använda [Data Factory-funktioner](data-factory-functions-variables.md) i skicka värden för webben tjänsten parametrar som du ser i följande exempel:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web service-parametrar är skiftlägeskänsliga, så se till att de namn som du anger i aktiviteten JSON matchar de som visas av webbtjänsten.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Med hjälp av en modul för dataläsare för att läsa data från flera filer i Azure Blob
Stordata pipelines med aktiviteter, till exempel Pig och Hive kan producera en eller flera utgående filer utan filnamnstillägg. När du anger en extern Hive-tabell, kan data för den externa Hive-tabellen lagras i Azure blob storage med följande namn 000000_0. Du kan använda läsmodulen i ett experiment för att läsa flera filer och använda dem för förutsägelser.

När du använder läsmodulen i ett Azure Machine Learning-experiment, kan du ange Azure Blob som indata. Filerna i Azure blob storage kan vara utdatafilerna (exempel: 000000_0) som genereras av ett Pig och Hive-skript som körs på HDInsight. Modul för dataläsare kan du läsa filer (med inga tillägg) genom att konfigurera den **sökvägen till behållaren, katalog-/ blob**. Den **sökvägen till behållaren** pekar till behållaren och **directory/blob** pekar på mappen som innehåller filerna som visas i följande bild. Asterisken det vill säga \*) **anger att alla filer i behållaren/mapp (det vill säga data/aggregateddata/år = 2014/månad – 6 /\*)** läses som en del av experimentet.

![Azure Blob-egenskaper](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exempel
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline med AzureMLBatchExecution aktivitet med Webbtjänstparametrar

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning studio model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

I JSON-exemplet ovan:

* Den distribuerade Azure Machine Learning Web-tjänsten använder en läsare och en skrivarmodul för att läsa/skriva data från/till en Azure SQL Database. Den här webbtjänsten exponerar följande fyra parametrar:  -Databasens servernamn, databasnamn, Server användarens kontonamn och Server lösenord.
* Båda **starta** och **slutet** datum och tid måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. Den **slutet** är valfri. Om du inte anger värdet för den **slutet** egenskapen, beräknas det som ”**start + 48 timmar.**” Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen. Se [Referens för JSON-skript](https://msdn.microsoft.com/library/dn835050.aspx) för information om JSON-egenskaper.

### <a name="other-scenarios"></a>Andra scenarier
#### <a name="web-service-requires-multiple-inputs"></a>Webbtjänsten kräver flera inmatningar
Om webbtjänsten tar flera inmatningar kan använda den **webServiceInputs** egenskapen istället för att använda **webServiceInput**. Datauppsättningar som refererar till den **webServiceInputs** måste också inkluderas i aktiviteten **indata**.

I Azure Machine Learning studio-experiment har webbtjänstindata och utgångsportar och globala parametrar du standardnamnen (”indata1”, ”indata2”) som du kan anpassa. De namn som du använder för webServiceInputs, webServiceOutputs och globalParameters inställningar måste exakt matcha namnen i experiment. Du kan visa nyttolasten i begäran av exemplet på hjälpsidan för batchkörning för din Azure Machine Learning studio-slutpunkt att verifiera förväntade mappningen.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Webbtjänsten kräver inte indata
Azure Machine Learning studio batch körning webbtjänster kan användas för att köra alla arbetsflöden för exempel R eller Python-skript, som inte kräver inga indata. Eller experimentet kan konfigureras med en modul för läsare som inte exponerar någon GlobalParameters. I så fall kan skulle aktiviteten AzureMLBatchExecution konfigureras på följande sätt:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webbtjänsten kräver inte en in-/ utdata
Tjänsten Azure Machine Learning studio för webben av batch-körningen kanske inte har några Web Service-utdata som konfigurerats. I det här exemplet kräver ingen webbtjänsten indata eller utdata eller har konfigurerats för alla GlobalParameters. Det finns fortfarande utdata som konfigurerats på själva aktiviteten, men anges inte som en webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web Service använder läsare och skrivare och aktivitetskörningar endast när andra aktiviteter har lyckades
Azure Machine Learning studio web service läsare och skrivare moduler kan konfigureras för att köras med eller utan någon GlobalParameters. Dock kan du bädda in tjänst-anrop i en pipeline som använder datauppsättningsberoenden för att anropa tjänsten endast när viss överordnade bearbetning har slutförts. Du kan också utlösa någon annan åtgärd när batchkörningen har slutförts med den här metoden. I så fall kan du uttrycka beroenden med hjälp av aktivitetens indata och utdata, utan att namnge någon av dem som webbtjänsten indata eller utdata.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Den **takeaways** är:

* Om experiment slutpunkten använder en webServiceInput: det representeras av en blobdatauppsättning och ingår i aktivitetens indata och webServiceInput-egenskapen. I annat fall utelämnas egenskapen webServiceInput.
* Om experiment slutpunkten använder webServiceOutput(s): de representeras av blob-datamängder och ingår i aktivitetsutdata och i egenskapen webServiceOutputs. Aktiviteten matar ut och webServiceOutputs mappas av namnet på varje utdata i experimentet. I annat fall utelämnas egenskapen webServiceOutputs.
* Om slutpunkten för ditt experiment exponerar globalParameter(s), anges de i egenskapen globalParameters aktivitet som nyckel, värde-par. I annat fall utelämnas egenskapen globalParameters. Nycklarna är skiftlägeskänsliga. [Azure Data Factory-funktioner](data-factory-functions-variables.md) kan användas i värden.
* Ytterligare datauppsättningar kan ingå i Aktivitetsegenskaper för indata och utdata utan som refereras i aktiviteten typeProperties. Dessa datauppsättningar styr körningen med hjälp av sektorn beroenden men ignoreras annars av aktiviteten AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Uppdatering av modeller med Uppdateringsresursaktivitet
När du är klar med att träna, uppdatera bedömning av webbtjänsten (förutsägelseexperiment visas som en webbtjänst) med den nyligen tränade modellen med hjälp av den **Azure Machine Learning studio aktivitet uppdatera resurs**. Se [uppdaterar modeller med Uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md) nedan för information.

### <a name="reader-and-writer-modules"></a>Läsare och skrivare moduler
Ett vanligt scenario för att använda webbtjänstparametrar är användning av Azure SQL och skrivfunktioner. Modul för dataläsare används för att läsa in data i ett experiment från datahanteringstjänster utanför Azure Machine Learning Studio. Modulen skrivaren är att spara data från dina experiment i datahanteringstjänster utanför Azure Machine Learning Studio.

Mer information om Azure Blob-/ Azure SQL-läsare/skrivaren finns [läsare](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [skrivaren](https://msdn.microsoft.com/library/azure/dn905984.aspx) ämnen i MSDN Library. I exemplet i föregående avsnitt användes Azure Blob-läsare och Azure Blob-skrivaren. Det här avsnittet beskrivs med hjälp av Azure SQL-läsare och Azure SQL-skrivare.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**F:** Jag har flera filer som genereras av min stordatapipelines. Kan jag använda AzureMLBatchExecution aktiviteten för att arbeta med alla filer?

**S:** Ja. Se den **med hjälp av en modul för dataläsare för att läsa data från flera filer i Azure Blob** information.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Azure Machine Learning studio Batch bedömning aktivitet
Om du använder den **AzureMLBatchScoring** aktivitet för att integrera med Azure Machine Learning, rekommenderar vi att du använder senast **AzureMLBatchExecution** aktivitet.

Aktiviteten AzureMLBatchExecution introduceras i augusti 2015-versionen av Azure SDK och Azure PowerShell.

Om du vill fortsätta att använda aktiviteten AzureMLBatchScoring fortsätta att läsa igenom det här avsnittet.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning studio-Batchbedömningen aktivitet som använder Azure Storage för indata/utdata

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Webbtjänstparametrar
Värden för webbtjänstparametrar lägger du till en **typeProperties** avsnitt i den **AzureMLBatchScoringActivity** i JSON-pipelinen avsnittet som visas i följande exempel:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Du kan också använda [Data Factory-funktioner](data-factory-functions-variables.md) i skicka värden för webben tjänsten parametrar som du ser i följande exempel:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web service-parametrar är skiftlägeskänsliga, så se till att de namn som du anger i aktiviteten JSON matchar de som visas av webbtjänsten.
>
>

## <a name="see-also"></a>Se även
* [Azure-blogginlägget: Komma igång med Azure Data Factory och Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
