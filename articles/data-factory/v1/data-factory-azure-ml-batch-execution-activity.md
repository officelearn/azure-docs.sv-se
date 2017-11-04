---
title: "Skapa förutsägande data pipelines med Azure Data Factory | Microsoft Docs"
description: "Beskriver hur du skapar skapa förutsägande pipelines med hjälp av Azure Data Factory och Azure Machine Learning"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3169584bc884107ccd34b01264683d8c73c0fecb
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Skapa förutsägande pipelines med hjälp av Azure Machine Learning och Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Pig-aktivitet](data-factory-pig-activity.md)
> * [MapReduce Activity](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [Anpassad aktivitet för .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduktion
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Transformera data med hjälp av machine learning i Data Factory version 2](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) gör det möjligt att bygga, testa och distribuera prediktiva Analyslösningar. Från en övergripande synsätt görs i tre steg:

1. **Skapa en träningsexperiment**. Du kan göra det här steget med hjälp av Azure ML Studio. ML studio är en gemensam visual utvecklingsmiljö som används för att träna och testa en förutsägelseanalysmodell med hjälp av utbildningsdata.
2. **Konvertera den till en prediktivt experiment**. När din modell har tränats med befintliga data och du är redo att använda den för att samla in nya data, förbereda och förenkla experimentet för resultatfunktioner.
3. **Distribuera den som en webbtjänst**. Du kan publicera experimentet bedömningsprofil som Azure-webbtjänst. Du kan skicka data till din modell via den här slutet för webbtjänst och ta emot resultatet förutsägelser från modellen.  

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar **förflyttning** och **transformering** av data. Du kan skapa data integration lösningar med hjälp av Azure Data Factory som kan mata in data från olika datakällor, transformera/bearbeta data och publicera Resultatdata till datalager.

Med Data Factory-tjänsten kan du skapa datapipelines som flyttar och transformerar data och kör pipelines enligt ett angivet schema (varje timme, varje dag, varje vecka osv.). Den innehåller också omfattande visualiseringar för att visa härkomst och beroenden mellan dina datapipelines och övervaka alla datapipelines från en enda enhetlig vy för att enkelt hitta problem och konfigurera övervakningsaviseringar.

Se [introduktion till Azure Data Factory](data-factory-introduction.md) och [skapa din första pipeline](data-factory-build-your-first-pipeline.md) artiklar för att snabbt komma igång med Azure Data Factory-tjänsten.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory och Machine Learning tillsammans
Azure Data Factory kan du enkelt skapa pipelines som använder en publicerade [Azure Machine Learning] [ azure-machine-learning] webbtjänsten för förutsägelseanalys. Med hjälp av den **Batchkörningsaktivitet** i Azure Data Factory-pipelinen, du kan anropa en Azure ML-webbtjänst för att göra förutsägelser på data i batch. Se [anropa en Azure ML-webbtjänst med hjälp av den Batchkörningsaktivitet](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) information.

Förutsägelsemodeller i Azure ML bedömningen experiment måste vara retrained med nya indatauppsättningar med tiden. Du kan träna om Azure ML-modell från Data Factory-pipelinen genom att göra följande:

1. Publicera träningsexperiment (inte prediktivt experiment) som en webbtjänst. Du kan göra det här steget i Azure ML Studio som du gjorde för att exponera prediktivt experiment som en webbtjänst i scenariot ovan.
2. Använda Azure ML-Batchkörningsaktivitet för att anropa webbtjänsten för utbildning experimentet. I princip kan du använda Azure ML-batchkörning aktiviteten anropa både utbildning webbtjänsten och webbtjänsten för bedömningsprofil.

När du är klar med omtränings uppdatera bedömningsprofil webbtjänsten (prediktivt experiment visas som en webbtjänst) med den nyligen tränade modellen med hjälp av den **Azure ML uppdatera resurs aktiviteten**. Se [uppdatering modeller med Uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md) artikeln för information.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Anropa en webbtjänst med hjälp av Batchkörningsaktivitet
Du använder Azure Data Factory för att samordna dataförflyttning och bearbetning och sedan utför med hjälp av Azure Machine Learning-batchkörning. Här är de högsta nivån steg:

1. Skapa en Azure Machine Learning länkad tjänst. Behöver du följande värden:

   1. **Begärande-URI** för batchkörning API. Du kan hitta begärd URI genom att klicka på den **BATCH EXECUTION** länken i sidan tjänster.
   2. **API-nyckel** publicerade Azure Machine Learning-webbtjänst. API-nyckeln hittar du genom att klicka på den webbtjänst som du har publicerat.
   3. Använd den **AzureMLBatchExecution** aktivitet.

      ![Machine Learning-instrumentpanelen](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario: Försök med hjälp av Web service indata/utdata som refererar till data i Azure Blob Storage
I det här scenariot Azure Machine Learning-webbtjänsten gör förutsägelser med hjälp av data från en fil i ett Azure blob storage och lagrar förutsägelse resultaten i blob storage. Följande JSON definierar Data Factory-pipelinen med en AzureMLBatchExecution aktivitet. Aktiviteten har datauppsättningen **DecisionTreeInputBlob** som indata och **DecisionTreeResultBlob** som utdata. Den **DecisionTreeInputBlob** skickas som indata till webbtjänsten genom att använda den **webServiceInput** JSON-egenskapen. Den **DecisionTreeResultBlob** skickas som utdata till webbtjänsten genom att använda den **webServiceOutputs** JSON-egenskapen.  

> [!IMPORTANT]
> Om webbtjänsten tar flera inmatningar kan använda den **webServiceInputs** egenskapen istället för att använda **webServiceInput**. Finns det [webbtjänst kräver flera indata](#web-service-requires-multiple-inputs) avsnittet ett exempel på hur du använder egenskapen webServiceInputs.
>
> Datauppsättningar som refererar till den **webServiceInput**/**webServiceInputs** och **webServiceOutputs** egenskaper (i **typeProperties**) måste också tas med i aktiviteten **indata** och **matar ut**.
>
> Ha standardnamnen (”input1”, ”input2”) som du kan anpassa i experimentet Azure ML webbtjänst och portar och globala parametrar. De namn som du använder för webServiceInputs, webServiceOutputs och globalParameters inställningar måste exakt matcha namnen i experiment. Du kan visa nyttolasten i begäran av exemplet på Batch Execution sidan för din Azure ML-slutpunkt att verifiera förväntade mappningen.
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
> Endast indata och utdata för aktiviteten AzureMLBatchExecution kan skickas som parametrar till webbtjänsten. Ovanstående JSON-kodutdrag är till exempel DecisionTreeInputBlob indata till aktiviteten AzureMLBatchExecution som skickas som indata till webbtjänsten via webServiceInput-parametern.   
>
>

### <a name="example"></a>Exempel
Det här exemplet använder Azure Storage för att lagra både inkommande och utgående data.

Vi rekommenderar att du går igenom den [skapa din första pipeline med Data Factory] [ adf-build-1st-pipeline] självstudiekursen innan du fortsätter med det här exemplet. Använd Data Factory-Redigeraren för att skapa de artefakter som Data Factory (länkade tjänster, datauppsättningar, rörledningar) i det här exemplet.   

1. Skapa en **länkade tjänsten** för din **Azure Storage**. Om inkommande och utgående filer finns i olika storage-konton, måste två länkade tjänster. Här är en JSON-exempel:

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
2. Skapa den **inkommande** Azure Data Factory **dataset**. Till skillnad från vissa andra Data Factory datauppsättningar, måste dessa data inte innehålla både **folderPath** och **fileName** värden. Du kan använda partitionering för att varje batchkörning (varje datasektorn) att bearbeta eller skapa unika inkommande och utgående filer. Du kan behöva ta någon överordnad aktivitet om du vill omvandla indata till CSV-filformatet och placera det i lagringskontot för varje segment. I så fall kan du inte innehåller den **externa** och **externalData** inställningar som visas i följande exempel och din DecisionTreeInputBlob skulle vara datamängd för utdata för en annan aktivitet.

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

    Inkommande csv-filen måste ha rubrikraden kolumn. Om du använder den **Kopieringsaktiviteten** för att skapa/flytta CSV-filen till blob-lagring, bör du ställa in egenskapen sink **blobWriterAddHeader** till **SANT**. Exempel:

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    Om csv-filen saknar rubrikraden, kan du se följande fel: **fel i aktiviteten: fel vid läsning av strängen. Oväntad token: StartObject. Sökvägen '', rad 1, position 1**.
3. Skapa den **utdata** Azure Data Factory **dataset**. Det här exemplet använder partitionering för att skapa en unik Utdatasökväg för varje segment-körning. Aktiviteten skulle utan partitionering, skriva över filen.

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
4. Skapa en **länkade tjänsten** av typen: **AzureMLLinkedService**, tillhandahålla API-nyckeln och modellen batch execution URL.

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
5. Slutligen skapar en pipeline som innehåller en **AzureMLBatchExecution** aktivitet. Vid körning utför pipeline följande steg:

   1. Hämtar platsen för indatafilen från dina indata datauppsättningar.
   2. Anropar batchkörning Azure Machine Learning API
   3. Kopierar batch utförande-utdatan till blob som anges i datamängd för utdata.

      > [!NOTE]
      > AzureMLBatchExecution aktivitet kan ha noll eller flera in- och utdata för en eller flera.
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

      Båda **starta** och **end** datum och tid måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. Den **end** tid är valfritt. Om du inte anger värdet för den **end** egenskapen, det beräknas som ”**start + 48 timmar.**” Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen. Se [Referens för JSON-skript](https://msdn.microsoft.com/library/dn835050.aspx) för information om JSON-egenskaper.

      > [!NOTE]
      > Ange indata för AzureMLBatchExecution är aktiviteten valfria.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario: Försök med Reader/Writer-moduler för att referera till data i olika lagringsobjekt
Ett annat vanligt scenario när du skapar Azure ML experiment är att använda läsare och skrivare moduler. Modul för dataläsare används för att läsa in data i ett experiment och modulen skrivaren är att spara data från dina experiment. Mer information om läsare och skrivare finns [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) avsnitt i MSDN Library.     

När du använder modulerna som läsare och skrivare, är bra idé att använda en Web service-parameter för varje egenskap för dessa moduler reader/writer. Dessa webb-parametrar kan du konfigurera värden under körningen. Du kan till exempel skapa ett experiment med en modul för läsare som använder en Azure SQL Database: XXX.database.windows.net. När webbtjänsten har distribuerats, som du vill aktivera användare för webbtjänsten att ange en annan Azure SQL-Server som kallas YYY.database.windows.net. Du kan använda en Web service-parametern så att det här värdet som ska konfigureras.

> [!NOTE]
> Webbtjänst och utdata skiljer sig från webbtjänstparametrar. I det första scenariot har du sett hur indata och utdata kan anges för en Azure ML-webbtjänst. I det här scenariot skicka parametrar för en webbtjänst som är kopplade till egenskaperna för läsare/skrivare moduler.
>
>

Nu ska vi titta på ett scenario för att använda webbtjänstparametrar. Du har en distribuerad Azure Machine Learning-webbtjänst som använder en modul för dataläsare för att läsa data från en av de datakällor som stöds av Azure Machine Learning (till exempel: Azure SQL Database). När batch-körningen har utförts skrivs resultaten med skrivarmodul (Azure SQL Database).  Ingen service indata och utdata har definierats i experiment. I detta fall rekommenderar vi att du konfigurerar relevanta webbtjänstparametrar för läsare och skrivare moduler. Den här konfigurationen kan du reader/writer konfigureras när du använder aktiviteten AzureMLBatchExecution-moduler. Du anger webbtjänstparametrar i den **globalParameters** avsnittet i JSON-aktivitet på följande sätt.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Du kan också använda [Data Factory funktioner](data-factory-functions-variables.md) i skicka värden för Web service parametrar som visas i följande exempel:

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
Stordata rörledningar med aktiviteter, till exempel Pig och Hive kan ge en eller flera utgående filer utan filnamnstillägg. När du anger en extern Hive-tabell, kan data för den externa Hive-tabellen lagras i Azure blob storage med följande namn 000000_0. Du kan använda modulen läsare i ett experiment för att läsa flera filer och använda dem för förutsägelser.

När du använder modulen läsare i en Azure Machine Learning-experiment kan ange du Azure Blob som indata. Filerna i Azure blob storage kan vara utdatafilerna (exempel: 000000_0) som produceras av ett Pig och Hive-skript som körs på HDInsight. Modul för dataläsare kan du läsa filer (med inga) genom att konfigurera den **sökvägen till behållaren directory/blob**. Den **sökvägen till behållaren** pekar på behållaren och **directory/blob** pekar på mappen som innehåller filerna som visas i följande bild. Asterisken som är \*) **anger att alla filer i mappen/behållare (det vill säga år-aggregateddata-data = månad/2014-6 /\*)** läses som en del av experimentet.

![Azure Blob-egenskaper](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exempel
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline med AzureMLBatchExecution aktivitet med Webbtjänstparametrar

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
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

* Distribuerade Azure Machine Learning webbtjänsten använder en läsare och skrivare-modulen för att läsa/skriva data från/till en Azure SQL Database. Den här webbtjänsten visar följande fyra parametrar: databasen servernamnet, databasnamnet, Server-användarkonto och serverlösenord.  
* Båda **starta** och **end** datum och tid måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. Den **end** tid är valfritt. Om du inte anger värdet för den **end** egenskapen, det beräknas som ”**start + 48 timmar.**” Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen. Se [Referens för JSON-skript](https://msdn.microsoft.com/library/dn835050.aspx) för information om JSON-egenskaper.

### <a name="other-scenarios"></a>Andra scenarier
#### <a name="web-service-requires-multiple-inputs"></a>Webbtjänsten kräver flera indata
Om webbtjänsten tar flera inmatningar kan använda den **webServiceInputs** egenskapen istället för att använda **webServiceInput**. Datauppsättningar som refererar till den **webServiceInputs** måste också tas med i aktiviteten **indata**.

Ha standardnamnen (”input1”, ”input2”) som du kan anpassa i experimentet Azure ML webbtjänst och portar och globala parametrar. De namn som du använder för webServiceInputs, webServiceOutputs och globalParameters inställningar måste exakt matcha namnen i experiment. Du kan visa nyttolasten i begäran av exemplet på Batch Execution sidan för din Azure ML-slutpunkt att verifiera förväntade mappningen.

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
Azure ML batch execution webbtjänster kan användas för att köra några arbetsflöden för R eller Python exempelskript, som inte kräver några indata. Eller experimentet kan konfigureras med en modul för läsare som inte exponerar någon GlobalParameters. I så fall skulle aktiviteten AzureMLBatchExecution konfigureras på följande sätt:

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
Azure ML batch execution webbtjänsten kanske inte har några utdata för webbtjänst konfigurerats. Det finns ingen webbtjänsten indata eller utdata eller konfigureras alla GlobalParameters i det här exemplet. Det finns fortfarande utdata konfigurerats på aktiviteten sig själv, men anges inte som en webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web Service använder läsare och skrivare och aktiviteten körs endast när andra aktiviteter har lyckades
Azure ML web service läsare och skrivare moduler kan konfigureras för körning med eller utan någon GlobalParameters. Du kanske vill bädda in tjänstanrop i en pipeline som använder dataset beroenden för att anropa tjänsten endast när vissa överordnade bearbetningen har slutförts. Du kan också utlösa någon annan åtgärd när batch-körningen har slutförts med den här metoden. I så fall kan du ange de beroenden som använder aktivitetens indata och utdata, utan att namnge någon av dem som webbtjänsten indata eller utdata.

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

* Om slutpunkten experimentet använder en webServiceInput: den representeras av en blobbdatauppsättning och ingår i aktivitetens indata- och egenskapen webServiceInput. Annars utelämnas egenskapen webServiceInput.
* Om slutpunkten experimentet använder webServiceOutput(s): de representeras av blob-datauppsättningar och ingår i aktivitetsutdata och i egenskapen webServiceOutputs. Aktiviteten matar ut och webServiceOutputs mappas av namnet på varje utdata i experimentet. Annars utelämnas egenskapen webServiceOutputs.
* Om din experiment slutpunkt visar globalParameter(s), anges de i egenskapen globalParameters aktivitet som nyckel, värde-par. Annars utelämnas egenskapen globalParameters. Nycklarna är skiftlägeskänsliga. [Azure Data Factory-funktioner](data-factory-functions-variables.md) får användas i värden.
* Ytterligare datauppsättningar kan ingå i Aktivitetsegenskaper för in- och utdataenheter utan refereras i aktiviteten typeProperties. De här datauppsättningarna styr körning med hjälp av sektorn beroenden men ignoreras annars av aktiviteten AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Uppdatering av modeller som använder Uppdateringsresursaktivitet
När du är klar med omtränings uppdatera bedömningsprofil webbtjänsten (prediktivt experiment visas som en webbtjänst) med den nyligen tränade modellen med hjälp av den **Azure ML uppdatera resurs aktiviteten**. Se [uppdatering modeller med Uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md) artikeln för information.

### <a name="reader-and-writer-modules"></a>Läsare och skrivare moduler
Ett vanligt scenario för att använda webbtjänstparametrar som används för Azure SQL-läsare och skrivare. Modul för dataläsare används för att läsa in data i ett experiment från data management-tjänster utanför Azure Machine Learning Studio. Modulen skrivaren är att spara data från dina experiment till data management services utanför Azure Machine Learning Studio.  

Mer information om Azure Blob-/ Azure SQL reader/writer finns [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) avsnitt i MSDN Library. Exemplet i det föregående avsnittet använda Azure Blob-läsare och skrivare i Azure Blob. Det här avsnittet beskrivs med SQL Azure reader och Azure SQL writer.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**F:** jag har flera filer som genereras av min stordata pipelines. Kan jag använda AzureMLBatchExecution aktiviteten för att fungera med alla filer?

**S:** Ja. Finns det **med hjälp av en modul för dataläsare för att läsa data från flera filer i Azure Blob** information.

## <a name="azure-ml-batch-scoring-activity"></a>Azure ML bedömningen batchaktiviteten
Om du använder den **AzureMLBatchScoring** aktivitet för att integrera med Azure Machine Learning, rekommenderar vi att du använder senast **AzureMLBatchExecution** aktivitet.

Aktiviteten AzureMLBatchExecution introduceras i augusti 2015-versionen av Azure SDK och Azure PowerShell.

Om du vill fortsätta använda aktiviteten AzureMLBatchScoring fortsätta läsa igenom det här avsnittet.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure ML-Batchbedömningen aktiviteten med Azure Storage för in-/ utdata

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
Värden för webbtjänstparametrar lägger du till en **typeProperties** avsnittet till den **AzureMLBatchScoringActivty** avsnittet i pipeline-JSON som visas i följande exempel:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Du kan också använda [Data Factory funktioner](data-factory-functions-variables.md) i skicka värden för Web service parametrar som visas i följande exempel:

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
* [Azure blogginlägg: komma igång med Azure Data Factory och Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
