---
title: Ta bort i Azure Data Factory
description: Lär dig hur du tar bort filer i olika filarkiv med borttagningsaktiviteten i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f265cdc955becd53ae7ba61ad827b2be69b92907
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618273"
---
# <a name="delete-activity-in-azure-data-factory"></a>Ta bort i Azure Data Factory

Du kan använda Borttagningsaktiviteten i Azure Data Factory för att ta bort filer eller mappar från lokala lagringslager eller molnlagringslager. Använd den här aktiviteten för att rensa eller arkivera filer när de inte längre behövs.

> [!WARNING]
> Det går inte att återställa borttagna filer eller mappar (såvida inte lagringen har aktiverat för mjuk borttagning). Var försiktig när du använder aktiviteten Ta bort till att ta bort filer och mappar.

## <a name="best-practices"></a>Bästa praxis

Här är några rekommendationer för hur du använder borttagningsaktiviteten:

-   Säkerhetskopiera dina filer innan du tar bort dem med aktiviteten Ta bort om du behöver återställa dem i framtiden.

-   Kontrollera att Data Factory har skrivbehörighet för att ta bort mappar eller filer från lagringsarkivet.

-   Kontrollera att du inte tar bort filer som skrivs samtidigt. 

-   Om du vill ta bort filer eller mappar från ett lokalt system kontrollerar du att du använder en självvärderad integrationskörning med en version som är större än 3.14.

## <a name="supported-data-stores"></a>Datalager som stöds

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)

### <a name="file-system-data-stores"></a>Datalager för filsystem

-   [Filsystem](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)

## <a name="syntax"></a>Syntax

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Egenskaper för typ

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Datamängd | Anger datauppsättningsreferensen för att avgöra vilka filer eller vilken mapp som ska tas bort | Ja |
| Rekursiv | Anger om filerna tas bort rekursivt från undermapparna eller bara från den angivna mappen.  | Nej. Standardvärdet är `false`. |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt för att ta bort mapp eller filer.   |  Nej. Standardvärdet är `1`. |
| aktivera loggning | Anger om du behöver registrera mappen eller filnamnen som har tagits bort. Om värdet är true måste du ytterligare ange ett lagringskonto för att spara loggfilen, så att du kan spåra beteendet för borttagningsaktiviteten genom att läsa loggfilen. | Inga |
| logStorageSettings | Gäller endast när enablelogging = sant.<br/><br/>En grupp lagringsegenskaper som kan anges där du vill spara loggfilen som innehåller mapp- eller filnamnen som har tagits bort av aktiviteten Ta bort. | Inga |
| linkedServiceName | Gäller endast när enablelogging = sant.<br/><br/>Den länkade tjänsten [Azure Storage](connector-azure-blob-storage.md#linked-service-properties), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties)eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) för att lagra loggfilen som innehåller mapp- eller filnamn som har tagits bort av borttagningsaktiviteten. Tänk på att den måste konfigureras med samma typ av integrationskörning från den som används av borttagningsaktiviteten för att ta bort filer. | Inga |
| path | Gäller endast när enablelogging = sant.<br/><br/>Sökvägen för att spara loggfilen i ditt lagringskonto. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga |

## <a name="monitoring"></a>Övervakning

Det finns två platser där du kan se och övervaka resultatet av borttagningsaktiviteten: 
-   Från utdata för aktiviteten Ta bort.
-   Från loggfilen.

### <a name="sample-output-of-the-delete-activity"></a>Exempel på utdata för aktiviteten Ta bort

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Exempel på loggfil för aktiviteten Ta bort

| Namn | Kategori | Status | Fel |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Fil | Borttagen |  |
| test2/hello789.txt | Fil | Borttagen |  |
| test2/test3/hello000.txt | Fil | Borttagen |  |
| test2/test3/zzz.json | Fil | Borttagen |  |

## <a name="examples-of-using-the-delete-activity"></a>Exempel på hur du använder aktiviteten Ta bort

### <a name="delete-specific-folders-or-files"></a>Ta bort specifika mappar eller filer

Arkivet har följande mappstruktur:

Rot/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt (2.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv 3.csv 3.csv 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt (4.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv (5.csv)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt (6.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv (7.csv)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt (8.txt)

Nu använder du aktiviteten Ta bort för att ta bort mapp eller filer genom en kombination av ett annat egenskapsvärde än datauppsättningen och aktiviteten Ta bort:

| folderPath (från datauppsättning) | fileName (från datauppsättning) | rekursiv (från aktiviteten Ta bort) | Resultat |
|:--- |:--- |:--- |:--- |
| Rot/ Folder_A_2 | NULL | False | Rot/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt (2.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv 3.csv 3.csv 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt (4.txt)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv (5.csv)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt (6.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv (7.csv)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt (8.txt) |
| Rot/ Folder_A_2 | NULL | True | Rot/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt (2.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv 3.csv 3.csv 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt (4.txt)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv (5.csv)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt (6.txt)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv (7.csv)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt (8.txt)</strike> |
| Rot/ Folder_A_2 | *.txt | False | Rot/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt (2.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv 3.csv 3.csv 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt (4.txt)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv (5.csv)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt (6.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv (7.csv)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt (8.txt) |
| Rot/ Folder_A_2 | *.txt | True | Rot/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt (2.txt)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv 3.csv 3.csv 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt (4.txt)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv (5.csv)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt (6.txt)</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv (7.csv)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt (8.txt)</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Rensa regelbundet den tidspartilade mappen eller filerna

Du kan skapa en pipeline för att regelbundet rensa tidspartiat mapp eller filer.  Mappstrukturen liknar till exempel: `/mycontainer/2018/12/14/*.csv`.  Du kan utnyttja ADF-systemvariabeln från schemautlösaren för att identifiera vilken mapp eller vilka filer som ska tas bort i varje pipeline-körning. 

#### <a name="sample-pipeline"></a>Exempel på pipeline

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Exempel på datauppsättning

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Exempelutlösare

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Rensa de utgångna filer som senast ändrades före 2018.1.1

Du kan skapa en pipeline för att rensa de gamla eller utgångna filerna genom att utnyttja filattributfiltret: "LastModified" i datauppsättningen.  

#### <a name="sample-pipeline"></a>Exempel på pipeline

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Exempel på datauppsättning

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "*",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Flytta filer genom att länka kopieringsaktiviteten och ta bort aktiviteten

Du kan flytta en fil med hjälp av en kopieringsaktivitet för att kopiera en fil och sedan en borttagningsaktivitet för att ta bort en fil i en pipeline.  När du vill flytta flera filer kan du använda aktiviteten GetMetadata + Filteraktivitet + Foreach-aktivitet + Kopiera aktivitet + Ta bort aktivitet som i följande exempel:

> [!NOTE]
> Om du vill flytta hela mappen genom att definiera en datauppsättning som endast innehåller en mappsökväg och sedan använda en kopieringsaktivitet och en borttagningsaktivitet för att referera till samma datauppsättning som representerar en mapp, måste du vara mycket försiktig. Det beror på att du måste se till att det inte kommer nya filer som kommer in i mappen mellan kopiering och borttagning.  Om det finns nya filer som anländer till mappen vid den tidpunkt då kopieringsaktiviteten just slutfört kopieringsjobbet men aktiviteten Ta bort inte har stirrats, är det möjligt att ta bort aktiviteten Ta bort den nya ankommande filen som INTE har kopierats till målet ännu genom att ta bort hela mappen. 

#### <a name="sample-pipeline"></a>Exempel på pipeline

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Exempeldatauppsättningar

Datauppsättning som används av GetMetadata-aktivitet för att räkna upp fillistan.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

Datauppsättning för datakälla som används av kopieringsaktivitet och borttagningsaktiviteten.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Datauppsättning för datamål som används av kopieringsaktivitet.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```

Du kan också få mallen att flytta filer [härifrån](solution-template-move-files.md).

## <a name="known-limitation"></a>Känd begränsning

-   Ta bort aktivitet stöder inte att ta bort en lista över mappar som beskrivs av jokertecken.

-   När du använder filattributsfilter: modifiedDatetimeStart och modifiedDatetimeEnd för att välja filer som ska tas bort, se till att ange "fileName": "*" i datauppsättningen.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du flyttar filer i Azure Data Factory.

-   [Verktyget Kopiera data i Azure Data Factory](copy-data-tool.md)
