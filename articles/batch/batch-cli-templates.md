---
title: Kör Azure Batch jobb slutpunkt till slutpunkt utan att skriva kod (förhandsversion) | Microsoft Docs
description: Skapa mallfilerna för Azure CLI att skapa Batch pooler, jobb och uppgifter.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/18/2017
ms.author: markscu
ms.openlocfilehash: 0a6e355d8f16fed9022cc2cf55dc09781364f0b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Använda Azure Batch CLI-mallar och filöverföring (förhandsversion)

Med Azure CLI är det möjligt att köra batchjobb utan att skriva kod.

Skapa och använda mallfilerna med Azure CLI för att skapa Batch pooler, jobb och uppgifter. Enkelt överför jobb indatafiler till storage-konto som är associerade med Batch-kontot och nedladdning utdatafilerna.

## <a name="overview"></a>Översikt

Ett tillägg till Azure CLI kan Batch ska använda slutpunkt till slutpunkt av användare som inte är utvecklare. Med bara CLI-kommandon, kan du skapa en pool, överför indata, skapa jobb och förknippade aktiviteter och hämta resulterande utdata. Ingen ytterligare kod krävs. Kör CLI-kommandona direkt eller integrera dem i skript.

Batch-mallar som bygger på den [befintliga Batch-stöd i Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) för JSON-filer att ange egenskapsvärden när du skapar pooler, projekt, uppgifter och andra objekt. Följande funktioner läggs över vad du kan göra med JSON-filer med Batch-mallar:

-   Du kan definiera parametrar. När mallen används anges bara parametervärden till att skapa objektet med andra objekt egenskapsvärden som anges i brödtexten i mallen. En användare som förstår Batch och program som ska köras av Batch kan skapa mallar, ange pool, jobb och egenskapsvärden för aktiviteten. En användare mindre känner till Batch och/eller program behöver bara ange värden för de definierade parametrarna.

-   Jobbet uppgiften fabriker skapa en eller flera uppgifter som är kopplad till ett jobb, vilket minskar behovet av att många definitioner för uppgiften att skapas och förenkla avsevärt jobbet.


Jobb vanligtvis använda filer som indata och skapar utdata datafiler. Ett lagringskonto associeras som standard med varje Batch-kontot. Överföra filer till och från det här lagringskontot med hjälp av CLI med ingen kodning och inga autentiseringsuppgifter för lagring.

Till exempel [ffmpeg](http://ffmpeg.org/) är ett populärt program som bearbetar ljud- och bildfiler. Här är åtgärder med Azure Batch CLI för att anropa ffmpeg att kodas om käll-filer till olika lösningar.

-   Skapa en mall för poolen. Användaren som skapar mallen vet hur du anropar ffmpeg programmet och dess krav. de ange lämpliga OS, VM storlek, hur ffmpeg är installerat (från ett programpaket eller med Pakethanteraren, till exempel) och andra poolen egenskapsvärden. Parametrar skapas när mallen används endast programpools-ID och antal virtuella datorer måste anges.

-   Skapa en jobbmall. Användaren som skapar mallen vet hur ffmpeg måste anropas för att kodas om källan video till en annan upplösning och anger vilken kommandorad för uppgift; de vet att det finns en mapp som innehåller källan videofiler, med en aktivitet som behövs per indatafilen.

-   En användare med en uppsättning videofiler ska kunna koda skapar först en pool med mallen poolen att ange pool-ID och antal virtuella datorer som krävs. De kan sedan överföra källfilerna ska kunna koda. Ett jobb kan sedan skickas med jobbmallen att ange pool-ID och platsen för källfiler som har överförts. Batch-jobbet har skapats med en aktivitet per indatafilen som genereras. Slutligen kan du hämta kodas utdatafilerna.

## <a name="installation"></a>Installation

Installera tillägget Azure Batch CLI för att använda en mall och fil överföring.

Instruktioner om hur du installerar Azure CLI finns [installera Azure CLI 2.0](/cli/azure/install-azure-cli).

När Azure CLI har installerats kan du installera den senaste versionen av Batch-tillägget med hjälp av kommandot CLI:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Mer information om Batch-tillägg finns [Microsoft Azure Batch CLI-tillägg för Windows, Mac och Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux).

## <a name="templates"></a>Mallar

Azure Batch CLI kan objekt, till exempel pooler, jobb och uppgifter som ska skapas genom att ange en JSON-fil som innehåller egenskapsnamn och värden. Exempel:

```azurecli
az batch pool create –-json-file AppPool.json
```

Azure Batch-mallar liknar Azure Resource Manager-mallar i funktioner och syntax. De är JSON-filer som innehåller egenskapsnamn och värden, men lägga till följande huvudkoncepten:

-   **Parametrar**

    -   Tillåt egenskapsvärden anges i ett body-avsnitt med bara parametervärden som ska anges när mallen används. Till exempel kan fullständig definitionen för en pool placeras i brödtexten och endast en parameter som definierats för pool-id endast en pool med ID-sträng måste därför anges för att skapa en pool.
        
    -   Brödtexten i mallen kan skapas av någon med kunskap om Batch och program som ska köras av Batch; endast värden för parametrarna författare definierats måste anges när mallen används. En användare utan djupgående Batch och/eller program knowledge kan därför använda mallarna.

-   **Variabler**

    -   Tillåt enkla eller avancerade parametervärden som ska anges i en enda plats och på en eller flera platser i brödtexten i mallen. Variabler kan förenkla och minska storleken på mallen samt göra den mer hanterbar genom olika platser för att ändra egenskaperna.

-   **Konstruktioner på högre nivå**

    -   Vissa på högre nivå konstruktioner är tillgängliga i den mall som inte är ännu tillgängliga i Batch-API: er. Exempelvis kan en aktivitet fabrik definieras i en jobbmall för som skapar flera uppgifter för jobbet med en gemensam aktivitetsdefinition. Dessa konstruktioner undvika att behöva koden för att dynamiskt skapa flera JSON-filer, till exempel en fil per aktivitet, samt skapa skriptfiler för att installera program via en Pakethanteraren.

    -   Dessa konstruktioner kanske på någon punkt läggas till i Batch-tjänsten och är tillgängliga i Batch-API: er, användargränssnitt, osv.

### <a name="pool-templates"></a>Pool-mallar

Poolen mallar stöder standardmall funktionerna i parametrar och variabler. De stöder även följande på högre nivå konstruktion:

-   **Paketet refererar till**

    -   Alternativt kan program som ska kopieras till poolen noder med hjälp av paketet chefer. Package manager och paket-ID har angetts. Genom att deklarera ett eller flera paket undvika du att skapa ett skript som hämtar de nödvändiga paketen, installera skriptet och kör skriptet på varje nod i poolen.

Följande är ett exempel på en mall som skapar en pool av virtuella Linux-datorer med ffmpeg installerad. Ange endast en pool med ID-sträng och antalet virtuella datorer i poolen för att använda den:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Om mallfilen hette _pool ffmpeg.json_, anropa mallen på följande sätt:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Jobbmallar

Jobbmallar stöder standardmall funktionerna i parametrar och variabler. De stöder även följande på högre nivå konstruktion:

-   **Uppgiften fabriken**

    -   Skapar flera uppgifter för ett jobb från en aktivitetsdefinition. Tre typer av aktiviteten fabriken stöds – parametrisk Sopa aktivitet per fil och uppgift samling.

Följande är ett exempel på en mall som skapar ett jobb för att kodas om MP4-filer med ffmpeg till en av två lägre upplösningar. Den skapar en aktivitet per video källfilen:

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Om mallfilen hette _jobbet ffmpeg.json_, anropa mallen på följande sätt:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Filgrupper och filöverföring

De flesta jobb och uppgifter kräver att inkommande filer och skapar utdatafilerna. Vanligtvis överförs inkommande och utgående filer, från klienten till noden, eller från noden till klienten. Azure Batch CLI-tillägget avlägsnar bort filöverföring och använder det lagringskonto som skapas som standard för varje Batch-kontot.

En filgrupp som är lika med en behållare som skapats i Azure storage-konto. Filgruppen kan ha undermappar.

Tillägget Batch CLI innehåller kommandon för att överföra filer från klienten till en angiven filgrupp och hämta filer från den angivna filgruppen till en klient.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Poolen och jobb med mallar kan filer som lagras i filgrupper anges för kopiering till poolen noder eller inaktivera poolen noder tillbaka till en filgrupp. Till exempel i jobbmallen angav tidigare, har filen grupp ”ffmpeg-indata” angetts för uppgiften fabriken som plats för video källfilerna kopieras ned till noden för omkodning; filen grupp ”ffmpeg-utdata” är den plats där utdatafiler kodas kopieras till från den nod som kör varje aktivitet.

## <a name="summary"></a>Sammanfattning

Stöd för överföring av mall och fil lagts till Azure CLI. Målet är att expandera den målgrupp som kan använda Batch för användare som inte behöver utveckla kod med Batch-API, till exempel forskare, IT-användare och så vidare. Utan kodning, kan användare med kunskaper om Azure Batch och program som ska köras av Batch skapa mallar för att skapa en pool och jobb. Användare utan detaljerade kunskaper om Batch- och programmen kan använda mallarna med mallparametrar.

Prova att använda Batch-tillägget i Azure CLI och ge oss feedback och förslag, antingen i kommentarer för den här artikeln eller via den [Azure Batch-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Nästa steg

- Bloggposten Batch-mallar: [kör Azure Batch-jobb med hjälp av Azure CLI – ingen kod krävs](https://azure.microsoft.com/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Utförlig information om installation och användning, exempel och källkoden är tillgängliga i den [Azure GitHub-lagringsplatsen](https://github.com/Azure/azure-batch-cli-extensions).
