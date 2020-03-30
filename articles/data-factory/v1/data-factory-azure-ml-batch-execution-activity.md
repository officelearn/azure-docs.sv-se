---
title: Skapa prediktiva datapipelpipelor med Azure Data Factory
description: Beskriver hur du skapar skapa förutsägande pipelines med Azure Data Factory och Azure Machine Learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: eba5df587d6bd6dda6083314cfb94836c6669393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73683144"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Skapa förutsägande pipelines med Azure Machine Learning och Azure Data Factory

> [!div class="op_single_selector" title1="Omvandlingsaktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Grisaktivitet](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET anpassad aktivitet](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduktion
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [transformera data med hjälp av maskininlärning i Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Med Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) kan du skapa, testa och distribuera lösningar för prediktiv analys. Ur hög nivå görs det i tre steg:

1. **Skapa ett träningsexperiment**. Du gör det här steget med hjälp av Azure Machine Learning studio. Azure Machine Learning Studio är en samarbetsmiljö för visuell utveckling som du använder för att träna och testa en modell för prediktiv analys med hjälp av träningsdata.
2. **Konvertera den till ett prediktivt experiment**. När din modell har tränats med befintliga data och du är redo att använda den för att få nya data, förbereder och effektiviserar du experimentet för bedömning.
3. **Distribuera den som en webbtjänst**. Du kan publicera ditt bedömningsexperiment som en Azure-webbtjänst. Du kan skicka data till din modell via den här slutpunkten för webbtjänsten och få resultatprognoser tillbaka till modellen.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory är en molnbaserad dataintegrationstjänst som dirigerar och automatiserar **förflyttning** och **omvandling** av data. Du kan skapa dataintegrationslösningar med Hjälp av Azure Data Factory som kan använda data från olika datalager, omvandla/bearbeta data och publicera resultatdata till datalager.

Med Data Factory-tjänsten kan du skapa datapipelines som flyttar och transformerar data och kör pipelines enligt ett angivet schema (varje timme, varje dag, varje vecka osv.). Den innehåller också omfattande visualiseringar för att visa härkomst och beroenden mellan dina datapipelines och övervaka alla datapipelines från en enda enhetlig vy för att enkelt hitta problem och konfigurera övervakningsaviseringar.

Se [Introduktion till Azure Data Factory](data-factory-introduction.md) och Bygg dina första [pipelineartiklar](data-factory-build-your-first-pipeline.md) för att snabbt komma igång med Azure Data Factory-tjänsten.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory och Machine Learning tillsammans
Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad [Azure Machine Learning-webbtjänst][azure-machine-learning] för prediktiv analys. Med hjälp av **batchkörningsaktiviteten** i en Azure Data Factory-pipeline kan du anropa en Azure Machine Learning studio-webbtjänst för att göra förutsägelser om data i batch. Mer information finns i Anropa en azure Machine Learning studio-webbtjänst med hjälp av avsnittet Batch Execution Activity.

Med tiden måste de prediktiva modellerna i Azure Machine Learning-studiobedömningsexperimenten tränas om med hjälp av nya indatauppsättningar. Du kan träna om en Azure Machine Learning studio-modell från en Data Factory-pipeline genom att göra följande:

1. Publicera utbildningsexperimentet (inte förutsägelseexperimentet) som en webbtjänst. Du gör det här steget i Azure Machine Learning-studion som du gjorde för att exponera förutsägelseexperiment som en webbtjänst i föregående scenario.
2. Använd Azure Machine Learning studio Batch Execution Activity för att anropa webbtjänsten för utbildningsexperimentet. I grund och botten kan du använda Azure Machine Learning studio Batch Execution aktivitet för att anropa både utbildning webbtjänst och scoring webbtjänst.

När du är klar med omskolningen uppdaterar du bedömningswebbtjänsten (förutsägelseexperiment som exponeras som en webbtjänst) med den nyligen utbildade modellen med hjälp av **Azure Machine Learning studio Update Resource Activity**. Mer information finns i Uppdatera modeller med artikeln [Uppdatera resursaktivitet.](data-factory-azure-ml-update-resource-activity.md)

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Anropa en webbtjänst med batchkörningsaktivitet
Du använder Azure Data Factory för att dirigera data förflyttning och bearbetning och utför sedan batchkörning med Azure Machine Learning. Här är stegen på den översta nivån:

1. Skapa en Azure Machine Learning-länkad tjänst. Du behöver följande värden:

   1. **Begär URI** för API:et för batchkörning. Du hittar URI:n för begäran genom att klicka på länken **BATCH EXECUTION** på sidan webbtjänster.
   2. **API-nyckel** för den publicerade Azure Machine Learning-webbtjänsten. Du hittar API-nyckeln genom att klicka på webbtjänsten som du har publicerat.
   3. Använd **AzureMLBatchExecution-aktiviteten.**

      ![Instrumentpanel för maskininlärning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario: Experiment med webbtjänstindata/utdata som refererar till data i Azure Blob Storage
I det här fallet gör Azure Machine Learning Web-tjänsten förutsägelser med hjälp av data från en fil i en Azure blob lagring och lagrar förutsägelse resultat i blob lagring. Följande JSON definierar en Data Factory-pipeline med en AzureMLBatchExecution-aktivitet. Aktiviteten har datauppsättningen **DecisionTreeInputBlob** som input och **DecisionTreeResultBlob** som utdata. **DecisionTreeInputBlob** skickas som en indata till webbtjänsten med hjälp av egenskapen **webServiceInput** JSON. **DecisionTreeResultBlob** skickas som utdata till webbtjänsten med hjälp av egenskapen **webServiceOutputs** JSON.

> [!IMPORTANT]
> Om webbtjänsten tar flera indata använder du egenskapen **webServiceInputs** i stället för att använda **webServiceInput**. Se [webbtjänsten kräver flera indataavsnitt](#web-service-requires-multiple-inputs) för ett exempel på hur du använder egenskapen webServiceInputs.
>
> Datauppsättningar som refereras av egenskaperna **webServiceInputs**/**webServiceInputs** och **webServiceOutputs** (i **typeProperties)** måste också **inkluderas** i in- och **utgångarna**för aktivitet .
>
> I ditt Azure Machine Learning studio-experiment har webbtjänstindata och utdataportar och globala parametrar standardnamn ("input1", "input2") som du kan anpassa. Namnen som du använder för inställningar för webServiceInputs, webServiceOutputs och globalParameters måste exakt matcha namnen i experimenten. Du kan visa exempelbegärans nyttolast på hjälpsidan för batchkörning för slutpunkten för Azure Machine Learning Studio för att verifiera den förväntade mappningen.
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
> Endast indata och utdata för AzureMLBatchExecution-aktiviteten kan skickas som parametrar till webbtjänsten. I ovanstående JSON-kodavsnitt är DecisionTreeInputBlob till exempel en indata till AzureMLBatchExecution-aktiviteten, som skickas som en indata till webbtjänsten via parametern webServiceInput.
>
>

### <a name="example"></a>Exempel
I det här exemplet används Azure Storage för att lagra både indata och utdata.

Vi rekommenderar att du går igenom [build your första pipeline med datafabrikens][adf-build-1st-pipeline] självstudiekurs innan du går igenom det här exemplet. Använd Data Factory Editor för att skapa datafabriksartefakter (länkade tjänster, datauppsättningar, pipeline) i det här exemplet.

1. Skapa en **länkad tjänst** för din **Azure Storage**. Om in- och utdatafilerna finns i olika lagringskonton behöver du två länkade tjänster. Här är ett JSON exempel:

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
2. Skapa **indatadatauppsättningen** Azure Data Factory . **dataset** Till skillnad från vissa andra Data Factory-datauppsättningar måste dessa datauppsättningar innehålla både **mappPath-** och **fileName-värden.** Du kan använda partitionering för att varje batchkörning (varje datasegment) ska bearbeta eller producera unika in- och utdatafiler. Du kan behöva inkludera en del aktivitet uppströms för att omvandla indata till CSV-filformatet och placera den i lagringskontot för varje segment. I så fall skulle du inte inkludera de **externa** och **externaDatainställningar** som visas i följande exempel, och ditt DecisionTreeInputBlob skulle vara utdatauppsättningen för en annan aktivitet.

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

    Filen för indata csv måste ha kolumnrubrikraden. Om du använder **kopieringsaktiviteten** för att skapa/flytta csv till blob-lagringen bör du ange att sink-egenskapen **blobWriterAddHeader** **är true**. Ett exempel:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Om csv-filen inte har rubrikraden kan följande fel visas: **Fel i Aktivitet: Felläsningssträng. Oväntad token: StartObject. Bana '', linje 1, position 1**.
3. Skapa **utdatadatauppsättningen** Azure Data Factory . **dataset** I det här exemplet används partitionering för att skapa en unik utdatasökväg för varje segmentkörning. Utan partitioneringen skulle aktiviteten skriva över filen.

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
4. Skapa en **länkad tjänst** av typen **AzureMLLinkedService**, som tillhandahåller API-nyckeln och url:en för körning av modellbatchen.

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
5. Slutligen, skapa en pipeline som innehåller en **AzureMLBatchExecution-aktivitet.** Vid körning utför pipelinen följande steg:

   1. Hämtar platsen för indatafilen från indatauppsättningarna.
   2. Anropar AZURE Machine Learning batchkörning API
   3. Kopierar utdata för batchkörning till den blob som anges i utdatauppsättningen.

      > [!NOTE]
      > AzureMLBatchExecution-aktivitet kan ha noll eller fler indata och en eller flera utdata.
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

      Både **start-** och **slutdatumtider** måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Sluttiden** är valfri. Om du inte anger **end** värdet för slutegenskapen beräknas det som "**start + 48 timmar.**" Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen. Se [Referens för JSON-skript](https://msdn.microsoft.com/library/dn835050.aspx) för information om JSON-egenskaper.

      > [!NOTE]
      > Det är valfritt att ange indata för AzureMLBatchExecution-aktiviteten.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario: Experiment med läsar-/skrivmoduler för att referera till data i olika lagringar
Ett annat vanligt scenario när du skapar Azure Machine Learning studio experiment är att använda Reader och Writer moduler. Läsarmodulen används för att läsa in data i ett experiment och skrivmodulen är att spara data från dina experiment. Mer information om läsar- och skrivmoduler finns i [Läsar-](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [writerämnen](https://msdn.microsoft.com/library/azure/dn905984.aspx) på MSDN-bibliotek.

När du använder läsar- och skrivmodulerna är det bra att använda en webbtjänstparameter för varje egenskap för dessa läsar-/writer-moduler. Med de här webbparametrarna kan du konfigurera värdena under körning. Du kan till exempel skapa ett experiment med en läsarmodul som använder en Azure SQL Database: XXX.database.windows.net. När webbtjänsten har distribuerats vill du aktivera konsumenter av webbtjänsten för att ange en annan Azure SQL Server som kallas YYY.database.windows.net. Du kan använda en webbtjänstparameter för att tillåta att det här värdet konfigureras.

> [!NOTE]
> Webbtjänstinmatning och utdata skiljer sig från webbtjänstparametrar. I det första scenariot har du sett hur en indata och utdata kan anges för en Azure Machine Learning studio webbtjänst. I det här fallet skickar du parametrar för en webbtjänst som motsvarar egenskaper för läsar-/writer-moduler.
>
>

Låt oss titta på ett scenario för att använda webbtjänstparametrar. Du har en distribuerad Azure Machine Learning-webbtjänst som använder en läsarmodul för att läsa data från en av de datakällor som stöds av Azure Machine Learning (till exempel Azure SQL Database). När batchkörningen har utförts skrivs resultaten med en Writer-modul (Azure SQL Database).  Inga webbtjänstindata och utdata definieras i experimenten. I det här fallet rekommenderar vi att du konfigurerar relevanta webbtjänstparametrar för läsar- och skrivmoduler. Med den här konfigurationen kan läsar-/skrivningsmodulerna konfigureras när du använder AzureMLBatchExecution-aktiviteten. Du anger webbtjänstparametrar i avsnittet **globalParameters** i aktiviteten JSON enligt följande.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Du kan också använda [Data Factory Functions](data-factory-functions-variables.md) i överföringsvärden för webbtjänstparametrarna enligt följande exempel:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Webbtjänstparametrarna är skiftlägeskänsliga, så se till att namnen du anger i aktiviteten JSON matchar de som visas av webbtjänsten.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Använda en läsarmodul för att läsa data från flera filer i Azure Blob
Stordatapipelledningar med aktiviteter som Pig och Hive kan producera en eller flera utdatafiler utan tillägg. När du till exempel anger en extern Hive-tabell kan data för den externa Hive-tabellen lagras i Azure blob storage med följande namn 000000_0. Du kan använda läsarmodulen i ett experiment för att läsa flera filer och använda dem för förutsägelser.

När du använder läsarmodulen i ett Azure Machine Learning-experiment kan du ange Azure Blob som indata. Filerna i Azure blob-lagringen kan vara utdatafilerna (Exempel: 000000_0) som produceras av ett Pig- och Hive-skript som körs på HDInsight. Med läsarmodulen kan du läsa filer (utan tillägg) genom att konfigurera **sökvägen till behållaren, katalogen/bloben**. **Behållaren Sökväg till** pekar på behållaren och **katalogen/bloben** pekar på mappen som innehåller filerna enligt följande bild. Asterisken som \*är ) **anger att alla filer i behållaren/mappen (det vill vara data/aggregeradedata/year=2014/month-6/\*)** läss som en del av experimentet.

![Egenskaper för Azure Blob](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exempel
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline med AzureMLBatchExecution-aktivitet med webbtjänstparametrar

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

I ovanstående JSON exempel:

* Webbtjänsten distribuerad Azure Machine Learning använder en läsare och en skrivmodul för att läsa/skriva data från/till en Azure SQL-databas. Den här webbtjänsten visar följande fyra parametrar: Databasservernamn, Databasnamn, Server-användarkontonamn och Server-användarkontolösenord.
* Både **start-** och **slutdatumtider** måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Sluttiden** är valfri. Om du inte anger **end** värdet för slutegenskapen beräknas det som "**start + 48 timmar.**" Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen. Se [Referens för JSON-skript](https://msdn.microsoft.com/library/dn835050.aspx) för information om JSON-egenskaper.

### <a name="other-scenarios"></a>Andra scenarier
#### <a name="web-service-requires-multiple-inputs"></a>Webbtjänsten kräver flera indata
Om webbtjänsten tar flera indata använder du egenskapen **webServiceInputs** i stället för att använda **webServiceInput**. Datauppsättningar som refereras av **webServiceInputs** måste också **inkluderas**i indata för aktivitet .

I ditt Azure Machine Learning studio-experiment har webbtjänstindata och utdataportar och globala parametrar standardnamn ("input1", "input2") som du kan anpassa. Namnen som du använder för inställningar för webServiceInputs, webServiceOutputs och globalParameters måste exakt matcha namnen i experimenten. Du kan visa exempelbegärans nyttolast på hjälpsidan för batchkörning för slutpunkten för Azure Machine Learning Studio för att verifiera den förväntade mappningen.

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

#### <a name="web-service-does-not-require-an-input"></a>Webbtjänsten kräver ingen indata
Azure Machine Learning studio batch execution web services kan användas för att köra alla arbetsflöden, till exempel R- eller Python-skript, som kanske inte kräver några indata. Eller så kan experimentet konfigureras med en reader-modul som inte exponerar några GlobalParameters. I så fall konfigureras AzureMLBatchExecution-aktiviteten på följande sätt:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webbtjänsten kräver ingen indata/utdata
Webbtjänsten Azure Machine Learning studio batch execution kanske inte har konfigurerat webbtjänstutdata. I det här exemplet finns det ingen webbtjänstinmatning eller utdata, och inga GlobalParameters är inte konfigurerade. Det finns fortfarande en utdata som konfigurerats för själva aktiviteten, men den anges inte som ett webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web Service använder läsare och skribenter, och aktiviteten körs endast när andra aktiviteter har lyckats
Webbtjänstläsaren och skrivmodulerna i Azure Machine Learning studio kan konfigureras för att köras med eller utan GlobalParameters. Du kanske vill bädda in tjänstanrop i en pipeline som använder datauppsättningsberoenden för att anropa tjänsten endast när viss uppströmsbearbetning har slutförts. Du kan också utlösa en annan åtgärd när batchkörningen har slutförts med den här metoden. I så fall kan du uttrycka beroenden med hjälp av aktivitetsindata och utdata, utan att namnge någon av dem som webbtjänstindata eller utdata.

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

**Takeaways** är:

* Om experimentslutpunkten använder en webServiceInput: den representeras av en blob-datauppsättning och ingår i aktivitetsindata och egenskapen webServiceInput. Annars utelämnas egenskapen webServiceInput.
* Om experimentslutpunkten använder webServiceOutput(s): representeras de av blob-datauppsättningar och ingår i aktivitetsutdata och i egenskapen webServiceOutputs. Aktivitetsutdata och webServiceOutputs mappas efter namnet på varje utdata i experimentet. Annars utelämnas egenskapen webServiceOutputs.
* Om experimentslutpunkten exponerar globalParameter anges de i egenskapen activity globalParameters som nyckel, värdepar. Annars utelämnas egenskapen globalParameters. Nycklarna är skiftlägeskänsliga. [Azure Data Factory-funktioner](data-factory-functions-variables.md) kan användas i värdena.
* Ytterligare datauppsättningar kan inkluderas i egenskaperna Aktivitetsindata och utdata, utan att refereras i aktivitetstypenEgenskaper. Dessa datauppsättningar styr körningen med hjälp av segmentberoenden men ignoreras annars av AzureMLBatchExecution Activity.


## <a name="updating-models-using-update-resource-activity"></a>Uppdatera modeller med uppdatera resursaktivitet
När du är klar med omskolningen uppdaterar du bedömningswebbtjänsten (förutsägelseexperiment som exponeras som en webbtjänst) med den nyligen utbildade modellen med hjälp av **Azure Machine Learning studio Update Resource Activity**. Mer information finns i Uppdatera modeller med artikeln [Uppdatera resursaktivitet.](data-factory-azure-ml-update-resource-activity.md)

### <a name="reader-and-writer-modules"></a>Läsar- och skrivmoduler
Ett vanligt scenario för att använda webbtjänstparametrar är användningen av Azure SQL-läsare och skribenter. Läsarmodulen används för att läsa in data i ett experiment från datahanteringstjänster utanför Azure Machine Learning Studio. Skrivmodulen är att spara data från dina experiment i datahanteringstjänster utanför Azure Machine Learning Studio.

Mer information om Azure Blob/Azure SQL-läsare/-brännare finns i [Läsar-](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [writer-ämnen](https://msdn.microsoft.com/library/azure/dn905984.aspx) på MSDN-bibliotek. I exemplet i föregående avsnitt användes Azure Blob-läsaren och Azure Blob-skrivaren. I det här avsnittet beskrivs hur du använder Azure SQL-läsare och Azure SQL-skrivare.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**F:** Jag har flera filer som genereras av mina stordatapipelpipelsar. Kan jag använda AzureMLBatchExecution-aktiviteten för att arbeta med alla filer?

**S:** Ja. Mer information finns i avsnittet Använda en läsare för **att läsa data från flera filer i Azure Blob.**

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Azure Machine Learning studio batch poängsättningsaktivitet
Om du använder **AzureMLBatchScoring-aktiviteten** för att integrera med Azure Machine Learning rekommenderar vi att du använder den senaste **AzureMLBatchExecution-aktiviteten.**

AzureMLBatchExecution-aktiviteten introduceras i augusti 2015-versionen av Azure SDK och Azure PowerShell.

Om du vill fortsätta använda AzureMLBatchScoring-aktiviteten fortsätter du att läsa igenom det här avsnittet.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning studio Batch Scoring aktivitet med Azure Storage för indata/utdata

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

### <a name="web-service-parameters"></a>Parametrar för webbtjänst
Om du vill ange värden för webbtjänstparametrar lägger du till avsnittet **typeProperties** i avsnittet **AzureMLBatchScoringActivity** i pipeline-JSON som visas i följande exempel:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Du kan också använda [Data Factory Functions](data-factory-functions-variables.md) i överföringsvärden för webbtjänstparametrarna enligt följande exempel:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Webbtjänstparametrarna är skiftlägeskänsliga, så se till att namnen du anger i aktiviteten JSON matchar de som visas av webbtjänsten.
>
>

## <a name="see-also"></a>Se även
* [Azure-blogginlägg: Komma igång med Azure Data Factory och Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
