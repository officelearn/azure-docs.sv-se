---
title: ForEach-aktivitet i Azure Data Factory
description: För varje aktivitet definierar ett upprepande kontrollflöde i pipelinen. Den används för att iterera över en samling och utföra angivna aktiviteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 71528385563b29ce70edf396434be0174beac105
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804851"
---
# <a name="foreach-activity-in-azure-data-factory"></a>ForEach-aktivitet i Azure Data Factory
ForEach-aktiviteten definierar ett upprepande kontrollflöde i pipelinen. Den här aktiviteten används till att iterera över en samling och kör angivna aktiviteter i en loop. Implementeringen av loopen för den här aktiviteten liknar Foreach-loopstrukturen i programmeringsspråk.

## <a name="syntax"></a>Syntax
Egenskaperna beskrivs senare i den här artikeln. Egenskapen Items är samlingen och varje objekt i samlingen `@item()` refereras till med hjälp av följande syntax:  

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

## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namn på för varje aktivitet. | Sträng | Ja
typ | Måste ställas in på **ForEach** | Sträng | Ja
ärSequential | Anger om loopen ska köras sekventiellt eller parallellt.  Maximalt 20 loop iterationer kan utföras på en gång parallellt). Om du till exempel har en ForEach-aktivitet som itererar över en kopieringsaktivitet med 10 olika käll- och sink-datauppsättningar med **isEquential** inställd på Falskt, körs alla kopior samtidigt. Standard är Falskt. <br/><br/> Om "isSequential" är inställt på Falskt, se till att det finns en korrekt konfiguration för att köra flera körbara filer. Annars bör den här egenskapen användas med försiktighet för att undvika att ådra sig skrivkonflikter. Mer information finns i [avsnittet Parallell körning.](#parallel-execution) | Boolesk | Nej. Standard är Falskt.
batchCount(1)batchCount | Batchantal som ska användas för att kontrollera antalet parallella körning (när isSequential är inställt på false). Detta är den övre samtidighetsgränsen, men för varje aktivitet kommer inte alltid att köras vid detta nummer | Heltal (max 50) | Nej. Standard är 20.
Objekt | Ett uttryck som returnerar en JSON-matris som ska itereras över. | Uttryck (som returnerar en JSON-matris) | Ja
Aktiviteter | De aktiviteter som ska utföras. | Lista med aktiviteter | Ja

## <a name="parallel-execution"></a>Parallell körning
Om **isEquential** är inställd på false, iterererar aktiviteten parallellt med högst 20 samtidiga iterationer. Denna inställning bör användas med försiktighet. Om de samtidiga iterationerna skriver till samma mapp men till olika filer är den här metoden bra. Om de samtidiga iterationerna skriver samtidigt till exakt samma fil, orsakar den här metoden troligen ett fel. 

## <a name="iteration-expression-language"></a>Språk för iterationsuttryck
I aktiviteten ForEach anger du en matris som ska itereras över för **egenskapsobjekten**." Använd `@item()` för att iterera över en enda uppräkning i ForEach-aktivitet. Om **objekt** till exempel är en matris: [1, `@item()` 2, 3], returnerar 1 i den första iterationen, 2 i den andra iterationen och 3 i den tredje iterationen.

## <a name="iterating-over-a-single-activity"></a>Itererar över en enda aktivitet
**Scenario:** Kopiera från samma källfil i Azure Blob till flera målfiler i Azure Blob.

### <a name="pipeline-definition"></a>Definition av pipeline

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

### <a name="blob-dataset-definition"></a>Definition av Blob-datauppsättning

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
Det är möjligt att iterera över flera aktiviteter (till exempel kopiera och webbaktiviteter) i en ForEach-aktivitet. I det här fallet rekommenderar vi att du abstraherar flera aktiviteter i en separat pipeline. Sedan kan du använda [aktiviteten ExecutePipeline](control-flow-execute-pipeline-activity.md) i pipelinen med ForEach-aktivitet för att anropa den separata pipelinen med flera aktiviteter. 


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
**Scenario:** Iterera över en InnerPipeline i en ForEach-aktivitet med Utföra pipeline-aktivitet. Den inre pipelinen kopierar med schemadefinitioner som parameteriserats.

#### <a name="master-pipeline-definition"></a>Huvudpipelinedefinition

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

#### <a name="inner-pipeline-definition"></a>Definition av inre rörledning

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

#### <a name="source-dataset-definition"></a>Definition av källdatauppsättning

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

#### <a name="sink-dataset-definition"></a>Definition av sink-datauppsättning

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

#### <a name="master-pipeline-parameters"></a>Huvudpipellineparametrar
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

## <a name="aggregating-outputs"></a>Samla utdata

Om du vill aggregera utdata __för förensch-aktivitet__ använder du _variabler_ och _lägg till variabel_ aktivitet.

Deklarera först `array` en _variabel_ i pipelinen. Anropa sedan _Lägg till variabel_ aktivitet inuti varje __förödgliga__ slinga. Därefter kan du hämta aggregeringen från matrisen.

## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

Här är några begränsningar för ForEach-aktiviteten och föreslagna lösningar.

| Begränsning | Lösning |
|---|---|
| Du kan inte kapsla en ForEach-loop inuti en annan ForEach-loop (eller en Till-slinga). | Utforma en pipeline på två nivåer där den yttre rörledningen med den yttre ForEach-loopen itererar över en inre pipeline med den kapslade slingan. |
| ForEach-aktiviteten har `batchCount` högst 50 för parallell bearbetning och högst 100 000 artiklar. | Utforma en pipeline på två nivåer där den yttre pipelinen med ForEach-aktiviteten itererar över en inre pipeline. |
| | |

## <a name="next-steps"></a>Nästa steg
Se andra kontrollflödesaktiviteter som stöds av Data Factory: 

- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
