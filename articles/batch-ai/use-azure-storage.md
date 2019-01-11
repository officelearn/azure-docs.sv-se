---
title: Lagring av Batch AI jobb-in- och utdata med Azure Storage | Microsoft Docs
description: Hur du använder Azure Storage med Batch AI för snabbt och enkelt molnlagring av inkommande och utgående filer
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: c1ebcae04b51fbf18da22872e3e4160a37a7e2fe
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201167"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Store Batch AI-jobbindata och utdata med Azure Storage

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Den här guiden beskriver hur du använder Azure Storage för lagring av inkommande och utgående filer när du kör ett jobb. Azure Storage är en av flera lagringsalternativ som stöds av Batch AI. Batch AI kan integreras med Azure Storage genom att montera Azure Storage-System till ett Batch AI-jobb eller ett kluster filsystem, så att sömlös åtkomst till filer som lagras i molnet. 

## <a name="introduction-to-azure-storage"></a>Introduktion till Azure Storage

Azure Storage är Microsofts molntjänstlagringslösning. Batch AI stöder montera Azure Blob-behållare och Azure-filresurser till Batch AI-jobb eller kluster, så att filer som kan nås från ett jobb som om de befann sig i det interna filsystemet. Batch AI monterar Azure Blob-behållare med [blobfuse](https://github.com/Azure/azure-storage-fuse), och Azure-filresurser via SMB-protokollet. Mer information om Azure Storage finns i [introduktion till Azure Storage](../storage/common/storage-introduction.md).

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Store-datauppsättningar och indata skript i Azure Storage

När du väljer Azure Storage för Batch AI-miljö rekommenderar vi att du lagrar dina indatafiler (till exempel datauppsättningar) i en Blob-behållare som har högre dataflöde, och du sparar din utbildning utdata i en filresurs, som har stöd för direktuppspelning (som tillåter läsning av utdataloggar medan jobbet körs samtidigt). 

Innan du kan använda Azure Storage, måste du [skapa ett Azure Storage-konto](../storage/common/storage-quickstart-create-account.md). Batch AI har stöd för montering volymer från båda General-purpose v1 (GPv1) och generell användning v2 (GPv2) Azure Storage-konton. Azure Storage-konto kan innehålla flera Blob-behållare eller fil-instanser av filresurser. Överväg att dina krav kostnad och prestanda när du väljer typ av lagringskonto för att skapa. Mer information finns i [kontoöversikten för Azure Storage](../storage/common/storage-account-overview.md). 

Skapa en Blob-behållare och om du vill ladda upp din datauppsättning till en Azure Blob-behållare, väljer du något av följande metoder:
- [Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md) för att ladda upp med ett webbaserat grafiskt användargränssnitt. Om du vill överföra ett litet antal filer, innehåller Azure portal enklaste igen.
- [Azure Storage-CLI](../storage/blobs/storage-quickstart-blobs-cli.md) för att ladda upp via kommandoraden (har stöd för directory överföring). Om du vill överföra kataloger för filer, använda `az storage blob upload-batch`.
- [andra metoder](../storage/common/storage-moving-data.md), inklusive användning av SDK: er för programmet.

På samma sätt för att skapa en Azure-filresurs, väljer du något av följande metoder:
- [Azure Portal](../storage/files/storage-how-to-use-files-portal.md)
- [Azure CLI-lagring](../storage/files/storage-how-to-use-files-cli.md)
- [andra tekniker](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Automatisk nyckellagring med Batch AI

Du kan också skapa ett Azure Storage-konto med en Azure-filresurs och Blob-behållare (och automatiskt montera volymerna till ett Batch AI-kluster) med hjälp av den `--use-auto-storage` parameter med `az batchai cluster create`. Mer information finns i [här](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account).

## <a name="mount-azure-storage"></a>Montera Azure Storage 

### <a name="mount-volumes-to-a-job"></a>Monteringsvolymer till ett jobb

Montera en Azure Storage-volym kan det nås via filsystemet skapas för varje jobb. Därför kan ett jobb läsa och skriva filer sömlöst till molnlagring som om de vore lokala filer.

Om du vill montera en Azure Storage-volym i ett jobb som skapats med Azure CLI, använder den `mountVolumes` -egenskapen i din `job.json` filen när du kör `az batchai job create`. Ett exempel finns i den [Tensorflow GPU distribuerade recept](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json). Schemat för `mountVolumes` är:
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` och `azureBlobFileSystems` är båda matriser av objekt som representerar volymer för att montera. Beskrivningar av platshållarna:

- < RELATIVE_MOUNT_PATH > - volymen kommer att monteras på den här sökvägen. Till exempel om `relativeMountPath` är `jobs`, volymen kommer att finnas på `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`)
- < STORAGE_ACCOUNT_NAME > - namnet på det Azure Storage-konto som innehåller filresursen eller Blob-behållare
- < FILE_SHARE_NAME > - namnet på filresursen
- < BLOB_CONTAINER_NAME > - namnet på Blob-behållaren

Om du vill montera Azure Storage-volymer med Azure Batch AI-SDK: er, ange den `mount_volumes` (Python) eller `MountVolumes` (C#, Java)-egenskapen i `JobCreateParameters`. Du måste ange lagringskontots autentiseringsuppgifter vid montering av volymer med Azure Batch AI-SDK: er. Visa scheman för att montera volymer i [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet), och [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai.mountvolumes?view=azure-java-stable).

### <a name="mount-volumes-to-a-cluster"></a>Monteringsvolymer till ett kluster

Batch AI stöder också montera Azure Storage-volymer till ett Batch AI-kluster. När en volym monteras till ett kluster, kommer alla jobb som körs i klustret kan använda volymer monterade i klustret. Medan montering på jobbnivå ger större flexibilitet (vilket innebär att varje jobb att ha olika monterade volymer), kan klusternivå montering vara tillräckligt i grundläggande scenarier.

Om du vill montera en Azure Storage-volym till ett kluster som skapats med Azure CLI, använder den `mountVolumes` -egenskapen i din `cluster.json` filen när du kör `az batchai cluster create`. Schemat för `mountVolumes` när Montera ett kluster är samma som när du monterar till ett jobb. 

På samma sätt kan du använda den `mount_volumes` (Python) eller `MountVolumes` (C#, Java)-egenskapen i `ClusterCreateParameters` när du monterar med Azure Batch AI-SDK: er. 

## <a name="access-mounted-filesystem-in-a-job"></a>Åtkomst monterade filsystem i ett jobb

### <a name="azbatchaijobmountroot-environment-variable"></a>$AZ_BATCHAI_JOB_MOUNT_ROOT miljövariabeln

Inuti körningsmiljö för jobbets den katalog som innehåller de monterade lagringssystem kan användas med den `$AZ_BATCHAI_JOB_MOUNT_ROOT` miljövariabeln (om du använder montering på jobbnivå). Om du använder klusternivå montera den här miljövariabeln är `$AZ_BATCHAI_MOUNT_ROOT`. I följande exempel förutsätter att du använder montering på jobbnivå.

För att ange sökvägen till data i en monterad volym, använder du miljövariabeln `$AZ_BATCHAI_JOB_MOUNT_ROOT` tillsammans med den monterade sökvägen. Till exempel om inlärningsskript `train.py` har överförts till en Azure-fil resursen monterad på relativ monteringssökväg `scripts`, filen kommer att finnas på `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`.

Om dina utbildningsskript kräver kunskaper om en sökväg, bör du skickar den som ett argument på kommandoraden. Exempel: Om du har sparat dina data i en mapp med namnet `train_data` i en Azure Blob-behållare som är monterad på sökvägen `data`, du kan skicka `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` som ett argument på kommandoraden i skriptet. Därför måste du ändra skriptet för att godkänna kommandoradsargument.

### <a name="abbreviate-input-paths"></a>Förkorta indatasökvägarna

Om du vill förkorta indatasökvägarna till en miljövariabel, använder den `inputDirectories` egenskapen för din `job.json` fil (eller `models.JobCreateParameters.input_directories` om du använder Batch AI-SDK). Schemat för `inputDirectories` är:

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

Varje sökväg som angetts kommer att placeras i en miljövariabel som heter `$AZ_BATCHAI_INPUT_<ID>`. Med den här metoden kan du förenkla sökvägar för att ange filer eller kataloger. Till exempel för att förkorta sökvägen till ett inlärningsskript: om `"id"` är `"SCRIPT"` och `"path"` är `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`, och sedan på den sökvägen finns på `$AZ_BATCHAI_INPUT_SCRIPT` i körningsmiljö för jobbets.

Mer information finns i [här](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories).

### <a name="abbreviate-output-paths"></a>Förkorta utdata sökvägar

Om du vill förkorta utdata sökvägar till en miljövariabel, använder den `outputDirectories` egenskapen för din `job.json` fil (eller `models.JobCreateParameters.output_directories` om du använder Batch AI-SDK). Med den här metoden kan du förenkla sökvägarna för utdatafilerna. Schemat för `outputDirectories` är:

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

Varje sökväg som angetts kommer att placeras i en miljövariabel som heter `$AZ_BATCHAI_OUTPUT_<ID>`. Den `pathPrefix` avgör den monterade volymen för att lagra utdata (till exempel `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`). Den `pathSuffix` anger namnet på mappen för utdata (till exempel `"logs"`, `"checkpoints"`). Den faktiska sökvägen för utdata är en sammansättning av `pathPrefix`, `jobOutputDirectoryPathSegment` (automatiskt skapade för varje jobb) och `pathSuffix`.

Mer information finns i [här](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories).

## <a name="retrieve-job-output-from-azure-storage"></a>Hämta jobbutdata från Azure Storage

### <a name="use-azure-portal"></a>Använda Azure-portalen

Azure-portalen är ett praktiskt sätt att visa utdata från jobb med hjälp av en GUI-Utforskaren. Men om du vill visa utdata från Stdout- och Stderr eller från en sökväg i `outputDirectories`, filerna placeras i den automatiskt genererade sökvägen i Azure Storage-volym. Mer information finns nedan.

### <a name="access-stdout-and-stderr-output"></a>Åtkomst Stdout och Stderr-utdata

Använd den `stdOutErrPathPrefix` egenskapen för `job.json` ska berätta var du ska placera jobbets loggarna för jobbkörning och Stdout och Stderr-utdata för jobbet. Exempel: Om du har monterat en filresurs på relativ monteringssökväg `outputs`, och du anger den `stdOutErrPathPrefix` vara `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`, så jobbutdata Stdout- och Stderr kommer att vara tillgängliga på `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` i som monterad volym. Den här sökvägen för automatiskt genererade används för att förhindra att utdata kollisioner mellan jobb med samma namn.

Mer information finns i [här](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output).

### <a name="list-the-output-files"></a>Lista utdatafilerna

Du kan använda Azure CLI för att lista tillgängliga utdatafilerna i ett jobb med den `az batchai job file list` kommando. Till exempel om du vill visa filerna i katalogen standardutdata för ett jobb, använda `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`.

Mer information och exempel finns i [här](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

### <a name="stream-output-files"></a>Stream utdatafiler

Du kan använda Azure CLI till stream-filer med den `az batchai job file stream` kommando. Till exempel vill visa:
- STDOUT: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

Mer information och exempel finns i [här](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

## <a name="next-steps"></a>Nästa steg
- Om du vill veta mer om CLI-kommandon för att samverka med Azure Storage kan du visa den [Azure Batch AI CLI-dokumentationen](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
- Du hittar mer användningsexempel av Batch AI, inklusive montera lagring och läsa utdatafilerna, den [Jupyter Notebook recept för Batch AI](https://github.com/Azure/BatchAI).
- Utforska andra alternativ för att montera lagring, inklusive [montera en NFS-server](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) och [montera NFS, cifs eller GlusterFS klustret](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)