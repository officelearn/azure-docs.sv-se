---
title: Pipelines och aktiviteter i Azure Data Factory | Microsoft Docs
description: Läs mer om pipelines och aktiviteter i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: ca64c87a0211ae00218493fe7bfddcbbb81a032a
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43109447"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines och aktiviteter i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-create-pipelines.md)
> * [Aktuell version](concepts-pipelines-activities.md)

I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för dina dataförflyttnings- och databearbetningsscenarier.

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Till exempel kan en pipeline innehålla en uppsättning aktiviteter som infogar och rensar loggdata och sedan startar ett Spark-jobb på ett HDInsight-kluster för att analysera loggdata. Det fina med detta är att pipelinen låter dig hantera aktiviteterna som en grupp i stället för enskilt. Du kan till exempel distribuera och schemalägga en hel pipeline i stället för att göra detta separat för varje aktivitet.

Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från en lokal SQL Server till Azure Blob Storage. Använd sedan en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster för att bearbeta/transformera data från Blob Storage för att producera utdata. Till sist använder du en andra kopieringsaktivitet för att kopiera utdata till ett Azure SQL Data Warehouse som Business Intelligence-rapporteringslösningar har skapats på.

Data Factory stöder tre typer av aktiviteter: [dataförflyttning](copy-activity-overview.md), [datatransformering](transform-data.md) och [kontroll](control-flow-web-activity.md). En aktivitet kan ha noll eller flera [indatauppsättningar](concepts-datasets-linked-services.md) och kan producera en eller flera [utdatauppsättningar](concepts-datasets-linked-services.md). I följande diagram visas förhållandet mellan pipeline, aktivitet och datauppsättning i Data Factory:

![Förhållande mellan datauppsättning, aktivitet och pipeline](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

En indatauppsättning representerar indata för en aktivitet i pipeline och en utdatauppsättning representerar utdata för aktiviteten. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. När du har skapat en datauppsättning kan du använda den med aktiviteter i en pipeline. Till exempel kan en datauppsättning vara en in-/utdatauppsättning för en kopieringsaktivitet eller en HDInsightHive-aktivitet. Mer information om datauppsättning finns i artikeln [Datauppsättningar i Azure Data Factory](concepts-datasets-linked-services.md).

## <a name="data-movement-activities"></a>Dataförflyttningsaktiviteter
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory stödjer de datalager som listas i tabellen i det här avsnittet. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Mer information finns i artikeln [Kopieringsaktiviteten – översikt](copy-activity-overview.md).

## <a name="data-transformation-activities"></a>Datatransformeringsaktiviteter
Azure Data Factory stöder följande transformeringsaktiviteter som kan läggas till enskilt, eller kopplade till en annan aktivitet, i pipelines.

Datatransformeringsaktivitet | Compute-miljö
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Hadoop Streaming](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Machine Learning-aktiviteter: batchkörning och resursuppdatering](transform-data-using-machine-learning.md) | Azure VM
[Lagrad procedur](transform-data-using-stored-procedure.md) | Azure SQL, Azure SQL Data Warehouse eller SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Azure Data Lake Analytics
[Anpassad kod](transform-data-using-dotnet-custom-activity.md) | Azure Batch
[Databricks-anteckningsbok](transform-data-databricks-notebook.md) | Azure Databricks

Mer information finns i artikeln om [datatransformeringsaktiviteter](transform-data.md).

## <a name="control-activities"></a>Kontrollaktiviteter
Följande kontrollflödesaktiviteter stöds:

Kontrollaktivitet | Beskrivning
---------------- | -----------
[Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md) | Med Execute Pipeline-aktiviteten kan en Data Factory-pipeline anropa en annan pipeline.
[ForEach-aktivitet](control-flow-for-each-activity.md) | ForEach-aktiviteten definierar ett upprepat kontrollflöde i din pipeline. Den här aktiviteten används till att iterera över en samling och kör angivna aktiviteter i en loop. Implementeringen av loopen för den här aktiviteten liknar Foreach-loopstrukturen i programmeringsspråk.
[Webbaktivitet](control-flow-web-activity.md) | Webbaktiviteten kan används till att anropa en anpassad REST-slutpunkt från en Data Factory-pipeline. Du kan överföra datauppsättningar och länkade tjänster så att de förbrukas och används av aktiviteten.
[Lookup-aktivitet](control-flow-lookup-activity.md) | Lookup-aktiviteten kan användas till att läsa eller söka efter en post/ett tabellnamn/ett värde från valfri extern källa. Dessa utdata kan vidare refereras av efterföljande aktiviteter.
[GetMetadata-aktivitet](control-flow-get-metadata-activity.md) | GetMetadata-aktiviteten kan användas till att hämta metadata för data i Azure Data Factory.
[Until-aktivitet](control-flow-until-activity.md) | Implementerar Do-Until-loop som liknar Do-Until-loopstrukturen i programmeringsspråk. En uppsättning aktiviteter körs i en loop tills det villkor som är associerat med aktiviteten utvärderas till sant. Du kan ange en tidsgräns för Until-aktiviteten i Data Factory.
[If-villkorsaktivitet](control-flow-if-condition-activity.md) | If-villkoret kan användas grenbaserat på villkor som utvärderas som sanna eller falska. If-villkoret fungerar på samma sätt som en if-sats i ett programmeringsspråk. Det utvärderar en uppsättning aktiviteter om villkoret utvärderas till `true` och en annan uppsättning aktiviteter när villkoret utvärderas till `false`.
[Wait-aktivitet](control-flow-wait-activity.md) | När du använder en Wait-aktivitet i en pipeline väntar pipelinen under den angivna tidsperioden innan körningen återupptas med alla efterföljande aktiviteter.

## <a name="pipeline-json"></a>Pipeline JSON
Så här definieras en pipeline i JSON-format:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Tagga | Beskrivning | Typ | Krävs
--- | ----------- | ---- | --------
namn | Namnet på pipeline. Ange ett namn som representerar åtgärden som pipeline utför. <br/><ul><li>Maximalt antal tecken: 140</li><li>Måste börja med en bokstav, en siffra eller ett understreck (_)</li><li>Följande tecken är inte tillåtna: ”.”, ”+”, ”?”, ”/”, ”<”,”>”,” * ”,”%”,”&”,”:”,”\”</li></ul> | Sträng | Ja
beskrivning | Ange texten som beskriver vad pipeline används till. | Sträng | Nej
activities | Avsnittet **activities** kan ha en eller flera definierade aktiviteter. I avsnittet [Aktivitets-JSON](#activity-json) finns information om aktivitets-JSON-elementet. | Matris | Ja
parameters | Avsnittet **parameters** kan ha en eller flera definierade parametrar i pipeline, vilket gör pipeline flexibel för återanvändning. | Visa lista | Nej

## <a name="activity-json"></a>Aktivitets-JSON
Avsnittet **activities** kan ha en eller flera definierade aktiviteter. Det finns två huvudtyper av aktiviteter: körnings- och kontrollaktiviteter.

### <a name="execution-activities"></a>Körningsaktiviteter
I körningsaktiviteter ingår [dataförflyttning](#data-movement-activities) och [datatransformering](#data-transformation-activities). De har följande toppnivåstruktur:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

I följande tabell beskrivs egenskaperna i definitionen för aktivitets-JSON:

Tagga | Beskrivning | Krävs
--- | ----------- | ---------
namn | Namnet på aktiviteten. Ange ett namn som representerar åtgärden som aktiviteten utför. <br/><ul><li>Maximalt antal tecken: 55</li><li>Måste börja med en bokstav, en siffra eller ett understreck (_)</li><li>Följande tecken är inte tillåtna: ”.”, ”+”, ”?”, ”/”, ”<”,”>”,” * ”,”%”,”&”,”:”,”\” | Ja</li></ul>
beskrivning | Text som beskriver vad aktiviteten används till | Ja
typ | Typ av aktivitet. Information om olika typer av aktiviteter finns i avsnitten [Dataförflyttningsaktiviteter](#data-movement-activities), [Datatransformeringsaktiviteter](#data-transformation-activities) och [Kontrollaktiviteter](#control-activities). | Ja
linkedServiceName | Namnet på den länkade tjänst som används av aktiviteten.<br/><br/>En aktivitet kan kräva att du anger den länkade tjänst som länkar till den nödvändiga beräkningsmiljön. | Ja för HDInsight-aktivitet, Azure Machine Learning-batchbedömningsaktivitet, lagrad proceduraktivitet. <br/><br/>Nej för alla andra
typeProperties | Egenskaperna i avsnittet typeProperties beror på varje typ av aktivitet. Om du vill visa typegenskaper för en aktivitet klickar du på länkarna till aktiviteten i föregående avsnitt. | Nej
policy | Principer som påverkar körningsbeteende för aktiviteten. I den här egenskapen ingår tidsgräns- och omförsökbeteende. Standardvärden används om inga anges. Mer information finns i avsnittet [Aktivitetsprincip](#activity-policy). | Nej
dependsOn | Den här egenskapen används till att definiera aktivitetsberoenden och hur efterföljande aktiviteter beror på tidigare aktiviteter. Mer information finns i [Aktivitetsberoende](#activity-dependency) | Nej

### <a name="activity-policy"></a>Aktivitetsprincip
Principer påverkar körningsbeteendet hos en aktivitet och ger konfigurationsalternativ. Aktivitetsprinciper är bar tillgängliga för körningsaktiviteter.

### <a name="activity-policy-json-definition"></a>JSON-definition för aktivitetsprincip

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```
JSON-namn | Beskrivning | Tillåtna värden | Krävs
--------- | ----------- | -------------- | --------
timeout | Anger tidsgränsen för aktivitetens körning. | Tidsintervall | Nej. Standardtidsgränsen är 7 dagar.
retry | Max. antal omförsök | Integer | Nej. Standardvärdet är 0
retryIntervalInSeconds | Fördröjningen mellan omförsök i sekunder | Integer | Nej. Standardvärdet är 20 sekunder
secureOutput | Utdata från aktiviteten betraktas som säkra och loggas inte för övervakning om värdet är inställt på sant. | Boolesk | Nej. Standardvärdet är false.

### <a name="control-activity"></a>Kontrollaktivitet
Kontrollaktiviteter har följande toppnivåstruktur:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Tagga | Beskrivning | Krävs
--- | ----------- | --------
namn | Namnet på aktiviteten. Ange ett namn som representerar åtgärden som aktiviteten utför.<br/><ul><li>Maximalt antal tecken: 55</li><li>Måste börja med en bokstav, en siffra eller ett understreck (_)</li><li>Följande tecken är inte tillåtna: ”.”, ”+”, ”?”, ”/”, ”<”,”>”,” * ”,”%”,”&”,”:”,”\” | Ja</li><ul>
beskrivning | Text som beskriver vad aktiviteten används till | Ja
typ | Typ av aktivitet. Information om olika typer av aktiviteter finns i avsnitten [Dataförflyttningsaktiviteter](#data-movement-activities), [Datatransformeringsaktiviteter](#data-transformation-activities) och [Kontrollaktiviteter](#control-activities). | Ja
typeProperties | Egenskaperna i avsnittet typeProperties beror på varje typ av aktivitet. Om du vill visa typegenskaper för en aktivitet klickar du på länkarna till aktiviteten i föregående avsnitt. | Nej
dependsOn | Den här egenskapen används till att definiera aktivitetsberoende och hur efterföljande aktiviteter beror på tidigare aktiviteter. Mer information finns i [Aktivitetsberoende](#activity-dependency). | Nej

### <a name="activity-dependency"></a>Aktivitetsberoende
Aktivitetsberoende definierar hur efterföljande aktiviteter beror på tidigare aktiviteter, vilket fastställer villkoret för om nästa uppgift ska köras. En aktivitet kan vara beroende av en eller flera tidigare aktiviteter med olika beroendevillkor.

De olika beroendevillkoren är Succeeded (Lyckades), Failed (Misslyckades), Skipped (Överhoppad), Completed (Slutförd).

Exempel: Om en pipeline har Aktivitet A -> Aktivitet B är de olika scenarier som kan ske följande:

- Aktivitet B har beroendevillkor på Aktivitet A med **Succeeded** (Lyckades): Aktivitet B körs bara om Aktivitet A har den slutgiltiga statusen Suceeded (Lyckades)
- Aktivitet B har beroendevillkor på Aktivitet A med **Failed** (Misslyckades): Aktivitet B körs bara om Aktivitet A har den slutgiltiga statusen Failed (Misslyckades)
- Aktivitet B har beroendevillkor på Aktivitet A med **Completed** (Slutförd): Aktivitet B körs om Aktivitet A har de slutgiltiga statusen Succeeded (Lyckades) eller Failed (Misslyckades)
- Aktivitet B har beroendevillkor på Aktivitet A med **Skipped** (Överhoppad): Aktivitet B körs bara om Aktivitet A har den slutgiltiga statusen Skipped (Överhoppad). Skipped (Överhoppad) inträffar i scenariot Aktivitet X -> Aktivitet Y -> Aktivitet Z, där varje aktivitet bara körs om den tidigare aktiviteten lyckas. Om Aktivitet X misslyckas har Aktivitet Y statusen Skipped (Överhoppad) eftersom den aldrig körs. Och även Aktivitet Z har statusen Skipped (Överhoppad).

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Exempel: Aktivitet 2 är beroende av att Aktivitet 1 lyckas

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Exempel på kopieringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **Copy** (Kopiera) i avsnittet **activities**. I det här exempel kopierar [kopieringsaktiviteten](copy-activity-overview.md) data från Azure Blob Storage till en Azure SQL-databas.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Observera följande punkter:

- I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**.
- Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**. I artikeln [Datauppsättningar](concepts-datasets-linked-services.md) finns information om hur du definierar datauppsättningar i JSON.
- I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp. I avsnittet för [dataförflyttningsaktiviteter](#data-movement-activities) klickar du på det datalager som du vill använda som källa eller mottagare för att lära dig mer om hur du flyttar data till/från det datalagret.

En fullständig genomgång om hur du skapar denna pipeline finns i [Snabbstart: skapa en datafabrik](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Exempel på transfomeringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **HDInsightHive** i avsnittet **activities**. I det här exemplet transformerar [HDInsight Hive-aktiviteten](transform-data-using-hadoop-hive.md) data från Azure Blob Storage genom att köra en Hive-skriptfil på ett Azure HDInsight Hadoop-kluster.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Observera följande punkter:

- I activities-avsnittet finns det bara en aktivitet vars **typ** anges till **HDInsightHive**.
- Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas AzureStorageLinkedService), och i mappen skript i containern `adfgetstarted`.
- Avsnittet `defines` används för att ange körningsinställningar som skickas till Hive-skriptet som Hive-konfigurationsvärden (till exempel $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`.

Avsnittet **typeProperties** är olika för varje transformeringsaktivitet. Om du vill ha mer information om vilka typegenskaper som stöds för en transformeringsaktivitet klickar du på transformeringsaktiviteten i [Datatransformeringsaktiviteter](#data-transformation-activities).

En fullständig genomgång av hur du skapar denna pipeline finns i [Självstudier: transformera data med Spark](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Flera aktiviteter i en pipeline
De två föregående exemplen innehåller bara en aktivitet. Du kan fler än en aktivitet i en pipeline. Om du har flera aktiviteter i en pipeline och efterföljande aktiviteter inte är beroende av tidigare aktiviteter kan aktiviteterna köras parallellt.

Du kan länka två aktiviteter genom att använda [aktivitetsberoende](#activity-dependency), som definierar hur efterföljande aktiviteter är beroende av tidigare aktiviteter, vilket fastställer villkoret för om nästa uppgift ska köras. En aktivitet kan vara beroende av en eller flera tidigare aktiviteter med olika beroendevillkor.

## <a name="scheduling-pipelines"></a>Schemaläggningspipelines
Pipelines schemaläggs av utlösare. Det finns olika typer av utlösare (scheduler-utlösare, som gör så att pipelines utlöses efter ett klocktidsschema (wall-clock) och en manuell utlösare, som utlöser pipelines på begäran). Mer information om utlösare finns i artikeln om [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

Om du vill att utlösaren startar en pipelinekörning måste du ta med en pipelinereferens i utlösardefinitionen. Pipelines och utlösare har ett n-m-förhållande. Flera utlösare kan starta en enda pipeline och samma utlösare kan starta flera pipelines. När utlösaren har definierats måste du starta utlösaren så att den kan börja utlösa pipeline. Mer information om utlösare finns i artikeln om [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

Anta till exempel att du har en scheduler-utlösare, ”TriggerA” som ska starta en pipeline, ”MyCopyPipeline.” Du definierar utlösaren enligt följande exempel:

### <a name="trigger-a-definition"></a>Definition av TriggerA

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Nästa steg
I följande självstudier får du stegvisa instruktioner för att skapa pipelines med aktiviteter:

- [Skapa en pipeline med en kopieringsaktivitet](quickstart-create-data-factory-powershell.md)
- [Skapa en pipeline med en datatransformeringsaktivitet](tutorial-transform-data-spark-powershell.md)
