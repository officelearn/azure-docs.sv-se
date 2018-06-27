---
title: Azure Snabbstart – CNTK-utbildning med Batch AI – Azure CLI | Microsoft Docs
description: Lär dig att snabbt köra ett CNTK-utbildningsjobb med Batch AI med hjälp av Azure CLI
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 06/14/2018
ms.author: Alexander.Yukhanov
ms.openlocfilehash: eb00c1d4ec74b5268a1497b11087030ab6a86e5a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294081"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Kör ett CNTK-utbildningsjobb med hjälp av Azure CLI

Med Azure CLI 2.0 kan du skapa och hantera Batch AI-resurser – skapa/ta bort Batch AI-filservrar och kluster, samt skicka/avsluta/ta bort/övervaka träningsjobb.

Den här snabbstarten visar hur du skapar ett GPU-kluster och kör ett träningsjobb med hjälp av Microsoft Cognitive Toolkit (CNTK).

Träningsskriptet [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) finns tillgängligt på GitHub-sidan för Batch AI. Det här skriptet ger träning för CNN-nätverk (Convolutional Neural Network) i MNIST-databasen med handskrivna siffror.

Det officiella CNTK-exemplet har ändrats till att godkänna platsen för träningsdatauppsättningen och utdatakatalogens plats via kommandoradsargument.

## <a name="quickstart-overview"></a>Snabbstart, översikt

* Skapa ett GPU-kluster med en nod (`Standard_NC6` i VM-storlek) med namnet `nc6`;
* Skapa ett lagringskonto där jobbets in- och utdata ska lagras;
* Skapa en Azure-filresurs med två mappar, `logs` och `scripts`, för att lagra jobbets utdata och träningsskript;
* Skapa Azure-blobbehållaren `data` där träningsdata ska lagras;
* Distribuera träningsskript och träningsdata till den skapade filresursen och behållaren;
* Konfigurera jobbet och montera Azure-filresursen och Azure-blobbehållaren på klusternoden. Gör dem sedan tillgängliga som ett normalt filsystem på `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` och `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` är en miljövariabel som anges av Batch AI för jobbet.
* Övervaka jobbkörningen genom att strömma dess standardutdata;
* När jobbet är slutfört granskar du dess utdata och genererade modeller;
* När du är klar tar du bort alla allokerade resurser.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure-prenumeration – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Åtkomst till Azure CLI 2.0 med version 0.3 eller högre av batchaimodulen. Du kan antingen använda Azure CLI 2.0 som finns i [Azure Cloud Shell](../cloud-shell/overview.md) eller installera det lokalt i enlighet med [instruktionerna](/cli/azure/install-azure-cli?view=azure-cli-latest).

  Om du använder Cloud Shell ändrar du arbetskatalogen till `/usr/$USER/clouddrive`, eftersom hemkatalogen saknar ledigt utrymme:

  ```azurecli
  cd /usr/$USER/clouddrive
  ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Följande kommando skapar den nya resursgruppen `batchai.quickstart` på platsen USA, östra:

```azurecli
az group create -n batchai.quickstart -l eastus
```
## <a name="create-batch-ai-workspace"></a>Skapa en Batch AI-arbetsyta

Följande kommando skapar en Batch AI-arbetsyta i resursgruppen. En Batch AI-arbetsyta är en toppnivå-samling med alla typer av Batch AI-resurser:

```azurecli
az batchai workspace create -g batchai.quickstart -n quickstart
```

## <a name="create-gpu-cluster"></a>Skapa GPU-kluster

Följande kommando skapar ett GPU-kluster med en nod (VM-storleken är Standard_NC6) på arbetsytan med Ubuntu DSVM (Data Science Virtual Machine) som operativsystemavbildning:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -w quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Med Ubuntu DSVM kan du köra alla träningsjobb i Docker-behållare och köra de populäraste ramverken för djupinlärning direkt på den virtuella datorn.

Med alternativet `--generate-ssh-keys` genererar Azure CLI privata och offentliga ssh-nycklar, om de inte redan finns. Du kan komma åt klusternoderna med hjälp av den aktuella användarens namn och genererade ssh-nyckel.

Om du använder Cloud Shell bör du säkerhetskopiera ~/.ssh-mappen till ett permanent lagringsutrymme.

Exempel på utdata:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "myuser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Följande kommando skapar ett nytt lagringskonto i samma resursgrupp som användes för att skapa Batch AI-klustret. Använd lagringskontot för att lagra jobbets in- och utdata. Uppdatera kommandot med ett unikt lagringskontonamn.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```


## <a name="deploy-data"></a>Distribuera data

### <a name="download-the-training-script-and-training-data"></a>Hämta träningsskript och träningsdata

* Ladda ned och extrahera den förbehandlade MNIST-databasen från [den här platsen](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D) till den aktuella mappen.

För GNU/Linux eller Cloud Shell:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Observera att du kan behöva installera `unzip` om din GNU/Linux-distribution inte har den.

* Hämta exempelskriptet [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) till den aktuella mappen:

För GNU/Linux eller Cloud Shell:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

### <a name="create-azure-file-share-and-deploy-the-training-script"></a>Skapa Azure-filresursen och distribuera träningsskriptet

Följande kommandon skapar Azure-filresurserna `scripts` och `logs`, samt kopierar träningsskript till `cntk`-mappen inuti `scripts`-resursen:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

### <a name="create-a-blob-container-and-deploy-training-data"></a>Skapa en blobbehållare och distribuera träningsdata

Följande kommandon skapar Azure-blobbehållaren med namnet `data` och kopierar träningsdata till `mnist_cntk`-mappen:

```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

## <a name="submit-training-job"></a>Skicka träningsjobb

### <a name="create-a-batch-ai-experiment"></a>Skapa ett Batch AI-experiment

Ett experiment är en logisk behållare för relaterade Batch AI-jobb. Använd följande kommando för att skapa ett experiment på arbetsytan:

```azurecli
az batchai experiment create -g batchai.quickstart -w quickstart -n quickstart
```

### <a name="prepare-job-configuration-file"></a>Förbereda jobbets konfigurationsfil

Skapa en konfigurationsfil för träningsjobbet `job.json` med följande innehåll. Uppdatera med namnet på ditt lagringskonto.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<YOUR_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<YOUR_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Den här konfigurationsfilen anger:

* `nodeCount` – antalet noder som krävs av jobbet (1 för den här snabbstarten)
* `cntkSettings` – anger sökvägen till träningsskriptet och kommandoradsargumenten. Kommandoradsargument innehåller sökvägen till träningsdata och målsökvägen för lagring av de modeller som genereras. `AZ_BATCHAI_OUTPUT_MODEL` är en miljövariabel som angetts av Batch AI baserat på utdatans katalogkonfiguration (se nedan)
* `stdOutErrPathPrefix` – sökväg där Batch AI skapar kataloger som innehåller jobbets utdata och loggar
* `outputDirectories` – samling med utdatakataloger som ska skapas av Batch AI. För varje katalog skapar Batch AI en miljövariabel med namnet `AZ_BATCHAI_OUTPUT_<id>`, där `<id>` är katalogidentifieraren
* `mountVolumes` – lista med filsystem som ska monteras under jobbkörningen. Filsystemen monteras under `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` är en miljövariabel som anges av Batch AI
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` – lagringskontonamn som ska anges under jobbet via `--storage-account-name parameter` eller `AZURE_BATCHAI_STORAGE_ACCOUNT`-miljövariabeln på datorn.

### <a name="submit-the-job"></a>Skicka jobbet

```azurecli
az batchai job create -n cntk_python_1 -c nc6 -g batchai.quickstart -w quickstart -e quickstart  -f job.json --storage-account-name <storage account name>
```

Exempel på utdata:
```
{
  "caffe2Settings": null,
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-06-14T22:22:57.543000+00:00",
  "customMpiSettings": null,
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "startTime": "2018-06-14T22:22:59.838000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2018-06-14T22:22:59.838000+00:00",
  "horovodSettings": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/f2d6ff09-7549-4e1a-8cd8-ec839f042a61",
  "jobPreparation": null,
  "mountVolumes": {
    "azureBlobFileSystems": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOUR STORAGE ACCOUNT NAME>",
        "azureFileUrl": "https://<YOUR STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null
    }
  ],
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-06-14T22:22:58.625000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "danlep0614b",
  "schedulingPriority": "normal",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/workspaces/experiments/jobs"
}

```

## <a name="monitor-job-execution"></a>Övervaka jobbkörningen

Träningsskriptet rapporterar träningsförloppet i `stderr.txt`-filen som finns i standardutdatakatalogen. Övervaka förloppet med följande kommando:

```azurecli
az batchai job file stream -j cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart -f stderr.txt
```

Exempel på utdata:
```
File found with URL "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.62% * 10000
```

Strömningen stoppas när jobbet är slutfört (oavsett om det lyckades eller misslyckades).

## <a name="inspect-generated-model-files"></a>Inspektera genererade modellfiler

Jobbet lagrar de skapade modellfilerna i den angivna katalogen med `id`-attribut som är lika med `MODEL`. Skapa en lista med modellfiler och hämta URL:er med följande kommandon:

```azurecli
az batchai job file list -j cntk_python_1 -w quickstart -e quickstart -g batchai.quickstart -d MODEL
```

Exempel på utdata:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

Alternativt kan du använda Azure Portal eller Azure Storage Explorer för att inspektera de genererade filerna. Batch AI skapar en unik mappstruktur för varje utdata för att kunna skilja dem från andra jobb. Hitta sökvägen till mappen som innehåller utdata med `jobOutputDirectoryPathSegment`-attributet för det skickade jobbet:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart -w quickstart -e quickstart --query jobOutputDirectoryPathSegment
```

Exempel på utdata:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/workspaces/quickstart/experiments/quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs, ta bort en resursgrupp och alla allokerade resurser med följande kommando:

```azurecli
az group delete -n batchai.quickstart -y
```
