---
title: Köra jobb från på sida med mallar - Azure Batch
description: Med bara CLI-kommandon kan du skapa en pool, ladda upp indata, skapa jobb och associerade uppgifter och hämta de resulterande utdata.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: df7db30e987c408ff158acfc468010948c821b8d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397540"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Använda Azure Batch CLI-mallar och filöverföring

Med hjälp av ett Azure Batch-tillägg till Azure CLI är det möjligt att köra Batch-jobb utan att skriva kod.

Skapa och använd JSON-mallfiler med Azure CLI för att skapa batchpooler, jobb och uppgifter. Använd CLI-tilläggskommandon för att enkelt överföra jobbinmatningsfiler till lagringskontot som är kopplat till batch-kontot och hämta jobbutdatafiler.

## <a name="overview"></a>Översikt

Ett tillägg till Azure CLI gör att Batch kan användas från på sluten till sluttid av användare som inte är utvecklare. Med bara CLI-kommandon kan du skapa en pool, ladda upp indata, skapa jobb och associerade uppgifter och hämta de resulterande utdata. Ingen ytterligare kod krävs. Kör CLI-kommandona direkt eller integrera dem i skript.

Batchmallar bygger på det befintliga batchstöd i [Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) för JSON-filer för att ange egenskapsvärden när du skapar pooler, jobb, uppgifter och andra objekt. Batchmallar lägger till följande funktioner:

-   Parametrar kan definieras. När mallen används anges endast parametervärdena för att skapa objektet, med andra objektegenskapsvärden som anges i malltexten. En användare som förstår Batch och de program som ska köras av Batch kan skapa mallar och ange värden för pool, jobb och uppgiftsgenskap. En användare som är mindre bekant med Batch och/eller programmen behöver bara ange värdena för de definierade parametrarna.

-   Jobbuppgiftsfabriker skapar en eller flera aktiviteter som är associerade med ett jobb, vilket undviker att många aktivitetsdefinitioner skapas och avsevärt förenklar inlämnandet av jobb.


Jobb använder vanligtvis indatafiler och producerar utdatafiler. Ett lagringskonto associeras som standard med varje Batch-konto. Överför filer till och från det här lagringskontot med CLI, utan kodning och inga lagringsautentiseringsuppgifter.

Till exempel är [ffmpeg](https://ffmpeg.org/) ett populärt program som bearbetar ljud- och videofiler. Här är steg med Azure Batch CLI att anropa ffmpeg att koda om källvideofiler till olika upplösningar.

-   Skapa en poolmall. Användaren som skapar mallen vet hur man anropar ffmpeg-programmet och dess krav. De anger lämplig OS, VM-storlek, hur ffmpeg installeras (från ett programpaket eller med hjälp av en pakethanterare, till exempel) och andra gruppegenskapsvärden. Parametrar skapas så när mallen används behöver endast pool-ID och antal virtuella datorer anges.

-   Skapa en jobbmall. Användaren som skapar mallen vet hur ffmpeg måste anropas för att koda om källvideo till en annan upplösning och anger aktivitetskommandoraden. De vet också att det finns en mapp som innehåller källvideofilerna, med en uppgift som krävs per indatafil.

-   En slutanvändare med en uppsättning videofiler som ska kodas först skapar en pool med hjälp av poolmallen och anger endast pool-ID och antal virtuella datorer som krävs. De kan sedan ladda upp källfilerna för att koda om. Ett jobb kan sedan skickas med hjälp av jobbmallen och bara ange pool-ID och platsen för de källfiler som överförs. Batch-jobbet skapas, med en aktivitet per indatafil som genereras. Slutligen kan de omkodade utdatafilerna hämtas.

## <a name="installation"></a>Installation

Om du vill installera Azure Batch CLI-tillägget installerar du först [Azure CLI 2.0](/cli/azure/install-azure-cli)eller kör Azure CLI i [Azure Cloud Shell](../cloud-shell/overview.md).

Installera den senaste versionen av batchtillägget med följande Azure CLI-kommando:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Mer information om batch-CLI-tillägget och ytterligare installationsalternativ finns i [GitHub-repoen](https://github.com/Azure/azure-batch-cli-extensions).


Om du vill använda CLI-tilläggsfunktionerna behöver du ett Azure Batch-konto och, för de kommandon som överför filer till och från lagring, ett länkat lagringskonto.

Information om hur du loggar in på ett Batch-konto med Azure CLI finns i [Hantera batchresurser med Azure CLI](batch-cli-get-started.md).

## <a name="templates"></a>Mallar

Azure Batch-mallar liknar Azure Resource Manager-mallar, i funktionalitet och syntax. Det är JSON-filer som innehåller objektegenskapsnamn och värden, men lägger till följande huvudbegrepp:

-   **Parametrar**

    -   Tillåt att egenskapsvärden anges i ett brödtextavsnitt, där endast parametervärden måste anges när mallen används. Den fullständiga definitionen för en pool kan till exempel placeras i `poolId`brödtexten och endast en parameter som definierats för . Endast en pool-ID-sträng behöver därför levereras för att skapa en pool.
        
    -   Malltexten kan skapas av någon med kunskap om Batch och de program som ska köras av Batch; Endast värden för de författardefinierade parametrarna måste anges när mallen används. En användare utan fördjupad Batch och/eller applikationskunskap kan därför använda mallarna.

-   **Variabler**

    -   Tillåt att enkla eller komplexa parametervärden anges på ett ställe och användas på en eller flera platser i malltexten. Variabler kan förenkla och minska storleken på mallen, samt göra den mer underhållsbar genom att ha en plats för att ändra egenskaper.

-   **Konstruktioner på högre nivå**

    -   Vissa konstruktioner på högre nivå är tillgängliga i mallen som ännu inte är tillgängliga i batch-API:erna. En aktivitetsfabrik kan till exempel definieras i en jobbmall som skapar flera aktiviteter för jobbet med hjälp av en gemensam aktivitetsdefinition. Dessa konstruktioner undviker behovet av att koda för att dynamiskt skapa flera JSON-filer, till exempel en fil per uppgift, samt skapa skriptfiler för att installera program via en pakethanterare.

    -   Vid något tillfälle kan dessa konstruktioner läggas till i batch-tjänsten och vara tillgängliga i batch-API:er, UIs, etc.

### <a name="pool-templates"></a>Poolmallar

Poolmallar stöder standardmallfunktionerna för parametrar och variabler. De stöder också följande konstruktion på högre nivå:

-   **Paketreferenser**

    -   Du kan också kopiera programvara till poolnoder med hjälp av pakethanterare. Pakethanteraren och paket-ID:et har angetts. Genom att deklarera ett eller flera paket undviker du att skapa ett skript som hämtar de paket som krävs, installerar skriptet och kör skriptet på varje poolnod.

Följande är ett exempel på en mall som skapar en pool av Virtuella Linux-datorer med ffmpeg installerat. Om du vill använda den anger du bara en pool-ID-sträng och antalet virtuella datorer i poolen:

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

Om mallfilen hette _pool-ffmpeg.json_anropar du mallen på följande sätt:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

CLI uppmanar dig att ange `poolId` värden `nodeCount` för parametrar och parametrar. Du kan också ange parametrarna i en JSON-fil. Ett exempel:

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

Om parametern JSON-filen hette *pool-parameters.json*anropar du mallen på följande sätt:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Jobbmallar

Jobbmallar stöder standardmallfunktionerna för parametrar och variabler. De stöder också följande konstruktion på högre nivå:

-   **Aktivitetsfabrik**

    -   Skapar flera aktiviteter för ett jobb från en aktivitetsdefinition. Tre typer av aktivitetsfabrik stöds – parametrisk rensning, uppgift per fil och uppgiftssamling.

Följande är ett exempel på en mall som skapar ett jobb för att koda om MP4-videofiler med ffmpeg till en av två lägre upplösningar. Det skapar en uppgift per källvideofil. Se [Filgrupper och filöverföring](#file-groups-and-file-transfer) för mer information om filgrupper för jobbinmatning och utdata.

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

Om mallfilen hette _job-ffmpeg.json_anropar du mallen på följande sätt:

```azurecli
az batch job create --template job-ffmpeg.json
```

Precis som tidigare uppmanar CLI dig att ange värden för parametrarna. Du kan också ange parametrarna i en JSON-fil.

### <a name="use-templates-in-batch-explorer"></a>Använda mallar i Batch Explorer

Du kan ladda upp en Batch CLI-mall till det [batchutforskaren-skrivbordsprogrammet](https://github.com/Azure/BatchExplorer) (tidigare kallat BatchLabs) för att skapa en batchpool eller ett jobb. Du kan också välja bland fördefinierade pool- och jobbmallar i Galleriet för Grupputforskaren.

Så här laddar du upp en mall:

1. I Batch Explorer väljer du **Galleri** > **lokala mallar**.

2. Markera eller dra och släpp en lokal pool eller jobbmall.

3. Välj Använd den **här mallen**och följ anvisningarna på skärmen.

## <a name="file-groups-and-file-transfer"></a>Filgrupper och filöverföring

De flesta jobb och uppgifter kräver indatafiler och producerar utdatafiler. Vanligtvis överförs indatafiler och utdatafiler, antingen från klienten till noden eller från noden till klienten. Azure Batch CLI-tillägget abstraherar filöverföring och använder lagringskontot som du kan associera med varje batchkonto.

En filgrupp motsvarar en behållare som skapas i Azure-lagringskontot. Filgruppen kan ha undermappar.

Batch CLI-tillägget innehåller kommandon för att överföra filer från klient till en angiven filgrupp och hämta filer från den angivna filgruppen till en klient.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Pool- och jobbmallar gör att filer som lagras i filgrupper kan anges för kopiering på poolnoder eller av poolnoder tillbaka till en filgrupp. I den jobbmall som angetts tidigare anges till exempel filgruppen *ffmpeg-indata* för aktivitetsfabriken som platsen för källvideofilerna som kopieras ned till noden för omkodning. Filgruppen *ffmpeg-utdata* är den plats där de omkodade utdatafilerna kopieras från noden som kör varje uppgift.

## <a name="summary"></a>Sammanfattning

Stöd för mall- och filöverföring har för närvarande endast lagts till i Azure CLI. Målet är att utöka målgruppen som kan använda Batch till användare som inte behöver utveckla kod med hjälp av batch-API:er, till exempel forskare och IT-användare. Utan kodning kan användare med kunskap om Azure, Batch och de program som ska köras av Batch skapa mallar för pool och skapande av jobb. Med mallparametrar kan användare utan detaljerad kunskap om Batch och programmen använda mallarna.

Prova batchtillägget för Azure CLI och ge oss eventuell feedback eller förslag, antingen i kommentarerna för den här artikeln eller via [batch-community-repo.](https://github.com/Azure/Batch)

## <a name="next-steps"></a>Nästa steg

- Detaljerad installations- och användningsdokumentation, exempel och källkod finns i [Azure GitHub-repo.](https://github.com/Azure/azure-batch-cli-extensions)

- Läs mer om hur du använder [Batch Explorer](https://github.com/Azure/BatchExplorer) för att skapa och hantera batchresurser.
