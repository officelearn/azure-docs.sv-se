---
title: ForEach-aktiviteten i Azure Data Factory | Microsoft Docs
description: Varje aktivitet för definierar ett upprepat Kontrollflöde i din pipeline. Den används för att iterera över en samling och kör angivna aktiviteter.
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
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 23f00280a69212b9e623ae1da16a681ca30c9d51
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "42055580"
---
# <a name="foreach-activity-in-azure-data-factory"></a>ForEach-aktiviteten i Azure Data Factory
ForEach-aktiviteten definierar ett upprepat Kontrollflöde i din pipeline. Den här aktiviteten används till att iterera över en samling och kör angivna aktiviteter i en loop. Implementeringen av loopen för den här aktiviteten liknar Foreach-loopstrukturen i programmeringsspråk.

## <a name="syntax"></a>Syntax
Egenskaperna beskrivs senare i den här artikeln. Items-egenskapen är en samling och varje objekt i samlingen anges med hjälp av den `@item()` som visas i följande syntax:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Egenskaperna för anslutningstypen

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på den för varje aktiviteten. | Sträng | Ja
typ | Måste vara inställt på **ForEach** | Sträng | Ja
isSequential | Anger om loopen att köras sekventiellt eller parallellt.  Högst 20 iterationer av loopen kan köras på samma gång parallellt). Exempel: Om du har en ForEach-aktivitet som iterera över en Kopieringsaktivitet med 10 olika källa och mottagare datauppsättningar med **isSequential** inställt på FALSKT, alla kopior körs på samma gång. Standardvärdet är FALSKT. <br/><br/> Om ”isSequential” är inställt på FALSKT, måste du kontrollera att det finns en korrekt konfiguration för att köra flera körbara filer. I annat fall bör du använda den här egenskapen med försiktighet för att undvika konflikter för skrivning. Mer information finns i [parallell körning](#parallel-execution) avsnittet. | Boolesk | Nej. Standardvärdet är FALSKT.
batchCount | Batchantal som ska användas för att styra antalet parallell körning (när isSequential är inställd på false). | Heltal (maximalt 50) | Nej. Standardvärdet är 20.
Objekt | Ett uttryck som returnerar en JSON-matris att upprepas. | Uttryck (som returnerar en JSON-matris) | Ja
Aktiviteter | Aktiviteter som ska köras. | Lista över aktiviteter | Ja

## <a name="parallel-execution"></a>Parallell körning
Om **isSequential** är inställd på false, aktiviteten itererar parallellt med högst 20 samtidiga iterationer. Den här inställningen bör användas med försiktighet. Den här metoden är bra om samtidiga iterationer skriver till samma mapp men olika filer. Om samtidiga iterationer skriver samtidigt på exakt samma fil, orsakar ett fel troligen i den här metoden. 

## <a name="iteration-expression-language"></a>Iteration Uttrycksspråk
Ange en matris att upprepas för egenskapen i ForEach-aktiviteten **objekt**”. Använd `@item()` iterera över en enskild uppräkning i ForEach-aktiviteten. Till exempel om **objekt** är en matris: [1, 2, 3], `@item()` returnerar 1 i den första upprepningen 2 i den andra iterationen och 3 i den tredje iterationen.

## <a name="iterating-over-a-single-activity"></a>Iterera över en enskild aktivitet
**Scenario:** kopiera från samma käll-filen i Azure Blob till flera målfiler i Azure Blob.

### <a name="pipeline-definition"></a>Pipeline-definition

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Blobuppsättningsdefinitionen

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Kör parametervärden

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterera över flera aktiviteter
Det är möjligt att iterera över flera aktiviteter (till exempel: kopiera och web aktiviteter) i en ForEach-aktivitet. I det här scenariot rekommenderar vi att du abstrahera ut flera aktiviteter i en separat pipeline. Du kan sedan använda den [aktiviteten ExecutePipeline](control-flow-execute-pipeline-activity.md) i pipeline med ForEach-aktiviteten anropa separat pipeline med flera aktiviteter. 


### <a name="syntax"></a>Syntax

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```
### <a name="example"></a>Exempel
**Scenario:** Iterate över en InnerPipeline inom en ForEach-aktivitet med aktiviteten kör Pipeline. Inre pipelinen kopierar med schemadefinitioner som innehåller parametrar.

#### <a name="master-pipeline-definition"></a>Master Pipeline-definition

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Inre pipeline-definition

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Definitionen av datauppsättningen för källan

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definitionen av datauppsättningen för mottagare

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Master pipeline-parametrar
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}

```
## <a name="aggregating-metric-output"></a>Sammanställning av mått utdata
Uttryck för att samla in utdata från alla iterationer av en ForEach är `@activity('NameofInnerActivity')`. Till exempel om en ForEach-aktiviteten itereras via en ”MyCopyActivity” syntaxen är: `@activity('MyCopyActivity')`. Utdata är en matris med varje objekt som ger information om en viss iteration.

> [!NOTE]
> Om du vill ha mer information om en viss iteration syntaxen blir: `@activity('NameofInnerActivity')[0]` för den senaste iterationen. Använda numret inom parentes för att få åtkomst till den specifika iterationen av matrisen. Du skulle använda för att komma åt en viss egenskap för en viss iteration: `@activity('NameofInnerActivity')[0].output` eller `@activity('NameofInnerActivity')[0].pipelineName`.

**Matris utdatainformation alla iterationer:**
```json
[    
    {      
        "pipelineName": "db1f7d2b-dbbd-4ea8-964e-0d9b2d3fe676",      
        "jobId": "a43766cb-ba13-4c68-923a-8349af9a76a3",      
        "activityRunId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",      
        "linkedServiceName": "ADFService",      
        "status": "Succeeded",      
        "statusCode": null,      
        "output": 
            {        
                "progress": 100,        
                "loguri": null,        
                "dataRead": "6.00 Bytes",        
                "dataWritten": "6.00 Bytes",        
                "regionOrGateway": "West US",        
                "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",        
                "copyDuration": "00:00:05",        
                "dataVolume": "6.00 Bytes",        
                "throughput": "1.16 Bytes/s",       
                 "totalDuration": "00:00:10"      
            },      
        "resumptionToken": 
            {       
                "ExecutionId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",        
                "ResumptionToken": 
                    {          
                        "in progress": "217526fa-0218-42f1-b85c-e0b4f7b170ce/wu/cloud/"       
                    },        
                "ExtendedProperties": 
                    {          
                        "dataRead": "6.00 Bytes",          
                        "dataWritten": "6.00 Bytes",          
                        "regionOrGateway": "West US",          
                        "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",          
                        "copyDuration": "00:00:05",          
                        "dataVolume": "6.00 Bytes",          
                        "throughput": "1.16 Bytes/s",          
                        "totalDuration": "00:00:10"        
                    }      
            },      
        "error": null,      
        "executionStartTime": "2017-08-01T04:17:27.5747275Z",      
        "executionEndTime": "2017-08-01T04:17:46.4224091Z",     
        "duration": "00:00:18.8476816"    
    },
    {      
        "pipelineName": "db1f7d2b-dbbd-4ea8-964e-0d9b2d3fe676",      
        "jobId": "54232-ba13-4c68-923a-8349af9a76a3",      
        "activityRunId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",      
        "linkedServiceName": "ADFService",      
        "status": "Succeeded",      
        "statusCode": null,      
        "output": 
            {        
                "progress": 100,        
                "loguri": null,        
                "dataRead": "6.00 Bytes",        
                "dataWritten": "6.00 Bytes",        
                "regionOrGateway": "West US",        
                "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",        
                "copyDuration": "00:00:05",        
                "dataVolume": "6.00 Bytes",        
                "throughput": "1.16 Bytes/s",       
                 "totalDuration": "00:00:10"      
            },      
        "resumptionToken": 
            {       
                "ExecutionId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",        
                "ResumptionToken": 
                    {          
                        "in progress": "217526fa-0218-42f1-b85c-e0b4f7b170ce/wu/cloud/"       
                    },        
                "ExtendedProperties": 
                    {          
                        "dataRead": "6.00 Bytes",          
                        "dataWritten": "6.00 Bytes",          
                        "regionOrGateway": "West US",          
                        "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",          
                        "copyDuration": "00:00:05",          
                        "dataVolume": "6.00 Bytes",          
                        "throughput": "1.16 Bytes/s",          
                        "totalDuration": "00:00:10"        
                    }      
            },      
        "error": null,      
        "executionStartTime": "2017-08-01T04:18:27.5747275Z",      
        "executionEndTime": "2017-08-01T04:18:46.4224091Z",     
        "duration": "00:00:18.8476816"    
    }
]

```
## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
