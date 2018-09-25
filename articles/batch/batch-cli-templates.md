---
title: Köra Azure Batch-jobb slutpunkt till slutpunkt med hjälp av mallar | Microsoft Docs
description: Skapa Batch-pooler, jobb och aktiviteter med mallfilerna och Azure CLI.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 08/02/2018
ms.author: danlep
ms.openlocfilehash: 753a36eb6fb7a0c007c62bbab7fe7390e706b1f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964300"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Använda Azure Batch CLI-mallar och filöverföring

Med hjälp av en Azure Batch utökning av Azure CLI, är det möjligt att köra Batch-jobb utan att skriva kod.

Skapa och använda JSON-mallfiler med Azure CLI för att skapa Batch-pooler, jobb och aktiviteter. Använd CLI-tillägg-kommandon för att enkelt ladda upp jobbindatafiler till storage-kontot som är associerade med Batch-konto och nedladdning utdatafilerna.

## <a name="overview"></a>Översikt

En utökning av Azure CLI kan Batch ska använda slutpunkt till slutpunkt av användare som inte är utvecklare. Med bara CLI-kommandon, kan du skapa en pool, ladda upp indata, skapa jobb och förknippade aktiviteter och hämta resulterande utdata. Ingen ytterligare kod krävs. Köra CLI-kommandon direkt eller integrera dem i skript.

Batch-mallar som bygger på den [befintliga Batch-stöd i Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) för JSON-filer att ange egenskapsvärden när du skapar pooler, jobb, uppgifter och andra objekt. Batch-mallar lägger du till följande funktioner:

-   Parametrar kan definieras. När mallen används anges bara parametervärden att skapa objektet som med andra objekt egenskapsvärden som anges i brödtexten i mallen. En användare som förstår Batch och de program som ska köras av Batch kan skapa mallar, ange pool, jobb och egenskapsvärden för uppgiften. En användare mindre bekant med Batch och/eller program behöver bara ange värden för de definierade parametrarna.

-   Jobbet uppgift fabriker skapa en eller flera uppgifter som är associerade med ett jobb, undvika behovet av många definitioner av uppgiften som ska skapas och avsevärt förenkla jobböverföring.


Jobb normalt använda filer som indata och producerar utdata datafiler. Ett storage-konto associeras som standard med varje Batch-konto. Överföra filer till och från lagringskontot med hjälp av CLI med ingen kodning och inga autentiseringsuppgifter för lagringskontot.

Till exempel [ffmpeg](http://ffmpeg.org/) är ett populärt program som bearbetar ljud- och bildfiler. Här följer stegen med Azure Batch CLI för att anropa ffmpeg att koda om källan videofiler till olika lösningar.

-   Skapa en pool-mall. Den användare som skapar mallen vet hur du anropar ffmpeg-programmet och dess krav. de anger rätt OS, VM storlek, hur ffmpeg är installerat (från ett programpaket eller använder en pakethanterare, till exempel) och andra programpoolen egenskapsvärden. Parametrar skapas så när mallen används endast pool-ID och antalet virtuella datorer måste anges.

-   Skapa en jobbmall. Den användare som skapar mallen vet hur ffmpeg måste anropas för att koda om källvideon till en annan upplösning och anger kommandoraden för aktiviteten. de vill också veta att det finns en mapp som innehåller video källfiler, med en aktivitet som behövs per indatafilen.

-   En användare med en uppsättning videofiler att koda om skapar först en pool med hjälp av pool-mall att ange endast pool-ID och antalet virtuella datorer krävs. De kan sedan överföra källfilerna för att koda om. Sedan kan skicka ett jobb med hjälp av jobbmallen, ange endast pool-ID och platsen för källfiler som laddats upp. Batch-jobb skapas med en aktivitet per indatafilen som genereras. Slutligen kan utdatafilerna omkodade laddas ned.

## <a name="installation"></a>Installation

Att installera Azure Batch CLI-tillägg [installera Azure CLI 2.0](/cli/azure/install-azure-cli), eller kör Azure CLI i [Azure Cloud Shell](../cloud-shell/overview.md).

Installera den senaste versionen av tillägget Batch med hjälp av följande Azure CLI-kommando:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Mer information om Batch CLI-tillägg och fler installationsalternativ finns i den [GitHub-lagringsplatsen](https://github.com/Azure/azure-batch-cli-extensions).


Om du vill använda CLI-tillägg-funktioner måste du behöver ett Azure Batch-konto och, för de kommandon som överför filer till och från lagring, ett länkat lagringskonto.

Logga in på ett Batch-konto med Azure CLI, se [hantera Batch-resurser med Azure CLI](batch-cli-get-started.md).

## <a name="templates"></a>Mallar

Azure Batch-mallar liknar Azure Resource Manager-mallar i funktioner och syntax. De är JSON-filer som innehåller egenskapsnamn och värden, men Lägg till följande huvudsakliga begrepp:

-   **Parametrar**

    -   Tillåt egenskapsvärden anges i brödtexten, med bara parametervärden som du behöver för att ange när mallen används. Till exempel placeras fullständiga definitionen för en pool i brödtexten och endast en parameter som definierats för pool-id endast en pool ID-sträng måste därför anges för att skapa en pool.
        
    -   Brödtexten i mallen kan vara författade av någon med kunskap om Batch- och de program som ska köras av Batch; endast värden för författare-definierade parametrar måste anges när mallen används. En användare utan djupgående Batch och/eller programkunskap kan därför använda mallarna.

-   **Variabler**

    -   Tillåt enkla eller komplexa parametervärden som ska anges på samma plats och på en eller flera platser i brödtexten i mallen. Variabler kan förenkla och minska storleken på mallen, samt gör det mer anpassningsbart genom att använda en plats för att ändra egenskaperna.

-   **Konstruktioner på högre nivå**

    -   Vissa på högre nivå konstruktioner är tillgängliga i den mall som inte är ännu tillgängliga i Batch-API: er. Exempelvis kan du definiera en fabrik för uppgiften i en jobbmall för som skapar flera aktiviteter för jobbet med hjälp av en gemensam uppgift-definition. Dessa konstruktioner undvika behovet av att koden för att dynamiskt skapa flera JSON-filer, till exempel en fil per aktivitet, samt skapa skriptfiler att installera program via en pakethanterare.

    -   Vid en viss tidpunkt kanske dessa konstruktioner har lagts till Batch-tjänsten och är tillgängliga i Batch-API: er, UIs osv.

### <a name="pool-templates"></a>Pool-mallar

Pool-mallar stöder standardmall funktionerna i parametrar och variabler. Stöder följande på högre nivå konstruktion:

-   **Paketet refererar till**

    -   Du kan också kan program som ska kopieras till poolnoder med hjälp av pakethanterare. Package manager och paket-ID har angetts. Genom att deklarera ett eller flera paket undvika du att skapa ett skript som hämtar de nödvändiga paketen, installera skriptet och kör skriptet på varje nod i poolen.

Följande är ett exempel på en mall som skapar en pool med virtuella Linux-datorer med ffmpeg installerad. Ange endast en sträng för pool-ID och hur många virtuella datorer i poolen för att använda den:

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
                    "sku": "16.04-LTS",
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

Om mallfilen hette _pool ffmpeg.json_, anropa mallen enligt följande:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

CLI uppmanar dig att ange värden för den `poolId` och `nodeCount` parametrar. Du kan också ange parametrar i en JSON-fil. Exempel:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Om JSON-fil parametrar hette *pool-parameters.json*, anropa mallen enligt följande:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Jobbmallar

Jobbmallar stöder standardmall funktionerna i parametrar och variabler. Stöder följande på högre nivå konstruktion:

-   **Uppgiften factory**

    -   Skapar flera uppgifter för ett jobb från en aktivitetsdefinition. Tre typer av uppgiften factory stöds – parametrisk svepvinkeln aktivitet per fil och uppgift samling.

Följande är ett exempel på en mall som skapar ett jobb för att koda om MP4-filer med ffmpeg till en av två lägre upplösningar. Den skapar en aktivitet per video källfilen. Se [filer grupper och överföring](#file-groups-and-file-transfer) för mer information om filgrupper för jobbet indata och utdata.

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

Om mallfilen hette _jobbet ffmpeg.json_, anropa mallen enligt följande:

```azurecli
az batch job create --template job-ffmpeg.json
```

Som uppmanar tidigare, CLI dig att ange värden för parametrarna. Du kan också ange parametrar i en JSON-fil.

### <a name="use-templates-in-batch-explorer"></a>Använda mallar i Batch Explorer

Du kan ladda upp en Batch CLI-mall för att den [Batch Explorer](https://github.com/Azure/BatchExplorer) skrivbordsprogram (tidigare kallade BatchLabs) för att skapa en Batch-pool eller jobb. Du kan också välja från fördefinierade mallar på poolen och jobbet i Batch Explorer-galleriet.

Så här överför en mall:

1. I Batch Explorer väljer **galleriet** > **lokala mallar**.

2. Välj, eller dra och släpp, en lokal pool eller en jobbmall för.

3. Välj **Använd den här mallen**, och följ de anvisningarna på skärmen.

## <a name="file-groups-and-file-transfer"></a>Filgrupper och filöverföring

De flesta jobben och aktiviteterna kräver indatafiler och producera utdata. Vanligtvis är överförs inkommande och utgående filer, antingen från klienten till noden eller från noden till klienten. Azure Batch CLI-tillägget avlägsnar away filöverföring och använder det lagringskonto som du kan associera med varje Batch-konto.

En filgrupp motsvarar en behållare som har skapats i Azure storage-kontot. Filgruppen kan ha undermappar.

Batch CLI-tillägget innehåller kommandon för att ladda upp filer från klienten till en grupp för den angivna filen och ladda ned filer från den angivna filgruppen till en klient.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Mallar för poolen och jobbet kan filer som lagras i filgrupper måste anges för kopia på poolnoder eller inaktivera poolnoder tillbaka till en filgrupp. Till exempel i jobbet mallen angetts tidigare filgruppen *ffmpeg indata-* har angetts för aktiviteten factory som platsen för video källfilerna kopieras till noden omkodas. Filgruppen *ffmpeg-output* är den plats där utdatafilerna omkodade kopieras från noden som kör varje aktivitet.

## <a name="summary"></a>Sammanfattning

Stöd för överföring av mallen och filen lagts för närvarande endast för Azure CLI. Målet är att expandera den målgrupp som kan använda Batch för användare som inte behöver utveckla kod med hjälp av Batch API: er, till exempel forskare och IT-användare. Användare med kunskap om Azure Batch och de program som ska köras av Batch kan skapa mallar för att skapa en pool och jobb utan kodning. Användare utan detaljerad information för Batch och program kan använda mallarna med mallparametrar.

Prova Batch-tillägget för Azure CLI och ge oss feedback eller förslag, antingen i kommentarerna för den här artikeln eller via den [Batch Community lagringsplatsen](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Nästa steg

- Detaljerad dokumentation för installation och användning, exempel och källkod är tillgängliga i den [Azure GitHub-lagringsplatsen](https://github.com/Azure/azure-batch-cli-extensions).

- Läs mer om hur du använder [Batch Explorer](https://github.com/Azure/BatchExplorer) att skapa och hantera Batch-resurser.
