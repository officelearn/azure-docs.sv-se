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
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: shlo
ms.openlocfilehash: c5c12a66e8f66195a096588d779648d7486ab47b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808763"
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
namn | Namnet på den för varje aktiviteten. | String | Ja
typ | Måste vara inställt på **ForEach** | String | Ja
isSequential | Anger om loopen att köras sekventiellt eller parallellt.  Högst 20 iterationer av loopen kan köras på samma gång parallellt). Exempel: Om du har en ForEach-aktivitet som iterera över en Kopieringsaktivitet med 10 olika källa och mottagare datauppsättningar med **isSequential** inställt på FALSKT, alla kopior körs på samma gång. Standardvärdet är FALSKT. <br/><br/> Om ”isSequential” är inställt på FALSKT, måste du kontrollera att det finns en korrekt konfiguration för att köra flera körbara filer. I annat fall bör du använda den här egenskapen med försiktighet för att undvika konflikter för skrivning. Mer information finns i [parallell körning](#parallel-execution) avsnittet. | Boolean | Nej. Standardvärdet är FALSKT.
batchCount | Batchantal som ska användas för att styra antalet parallell körning (när isSequential är inställd på false). | Heltal (maximalt 50) | Nej. Standardvärdet är 20.
Objekt | Ett uttryck som returnerar en JSON-matris att upprepas. | Uttryck (som returnerar en JSON-matris) | Ja
Aktiviteter | Aktiviteter som ska köras. | Lista över aktiviteter | Ja

## <a name="parallel-execution"></a>Parallell körning
Om **isSequential** är inställd på false, aktiviteten itererar parallellt med högst 20 samtidiga iterationer. Den här inställningen bör användas med försiktighet. Den här metoden är bra om samtidiga iterationer skriver till samma mapp men olika filer. Om samtidiga iterationer skriver samtidigt på exakt samma fil, orsakar ett fel troligen i den här metoden. 

## <a name="iteration-expression-language"></a>Iteration Uttrycksspråk
Ange en matris att upprepas för egenskapen i ForEach-aktiviteten **objekt**”. Använd `@item()` iterera över en enskild uppräkning i ForEach-aktiviteten. Till exempel om **objekt** är en matris: [1, 2, 3], `@item()` returnerar 1 i den första upprepningen 2 i den andra iterationen och 3 i den tredje iterationen.

## <a name="iterating-over-a-single-activity"></a>Iterera över en enskild aktivitet
**Scenario:** Kopiera från samma käll-filen i Azure Blob till flera målfiler i Azure Blob.

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
**Scenario:** Iterera över en InnerPipeline inom en ForEach-aktivitet med aktiviteten kör Pipeline. Inre pipelinen kopierar med schemadefinitioner som innehåller parametrar.

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

## <a name="aggregating-outputs"></a>Sammanställning av utdata

Att sammanställd utdata för __foreach__ aktivitet,. Använd _variabler_ och _lägga till variabeln_ aktivitet.

Först måste deklarera en `array` _variabeln_ i pipelinen. Anropa sedan _lägga till variabeln_ aktivitet i var och en __foreach__ loop. Därefter kan du hämta aggregeringen från din matris.

## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

Här följer några begränsningar hos ForEach-aktiviteten och föreslagna lösningar.

| Begränsning | Lösning |
|---|---|
| Du kan inte kapsla en ForEach-loop i en annan ForEach-loop (eller en Until-loop). | Utforma en pipeline för två nivåer där de yttre pipelinen med yttre ForEach-loopen itererar över en inre pipeline med kapslade slinga. |
| ForEach-aktiviteten har en maximal `batchCount` 50 för parallell bearbetning och högst 100 000 objekt. | Utforma en pipeline för två nivåer där de yttre pipelinen med ForEach-aktiviteten itererar över en inre pipeline. |
| | |

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
