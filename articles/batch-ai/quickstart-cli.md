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
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 8d2381f710e87751bd6547c7f435080f185020d5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608553"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Kör ett CNTK-utbildningsjobb med hjälp av Azure CLI

Med Azure CLI 2.0 kan du skapa och hantera Batch AI-resurser – skapa/ta bort Batch AI-filservrar och kluster, samt skicka/avsluta/ta bort/övervaka träningsjobb.

Den här snabbstarten visar hur du skapar ett GPU-kluster och kör ett träningsjobb med hjälp av Microsoft Cognitive Toolkit.

Träningsskriptet [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) finns tillgängligt på GitHub-sidan för Batch AI. Det här skriptet ger träning för CNN-nätverk (Convolutional Neural Network) i MNIST-databasen med handskrivna siffror.

Det officiella CNTK-exemplet har ändrats till att godkänna platsen för träningsdatauppsättningen och utdatakatalogens plats via kommandoradsargument.

## <a name="quickstart-overview"></a>Snabbstart, översikt

* Skapa ett GPU-kluster med en nod (`Standard_NC6` i VM-storlek) med namnet `nc6`;
* Skapa ett nytt lagringskonto där jobbets in- och utdata ska lagras;
* Skapa en Azure-filresurs med två mappar, `logs` och `scripts`, för att lagra jobbets utdata och träningsskript;
* Skapa Azure-blobbehållaren `data` där träningsdata ska lagras;
* Distribuera träningsskript och träningsdata till den skapade filresursen och behållaren;
* Konfigurera jobbet och montera Azure-filresursen och Azure-blobbehållaren på klusternoden. Gör dem sedan tillgängliga som ett normalt filsystem på `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` och `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` är en miljövariabel som anges av Batch AI för jobbet.
* Övervaka jobbkörningen genom att strömma dess standardutdata;
* När jobbet är slutfört granskar du dess utdata och genererade modeller;
* När du är klar tar du bort alla allokerade resurser.

# <a name="prerequisites"></a>Nödvändiga komponenter

* Azure-prenumeration – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Åtkomst till Azure CLI 2.0 med version 2.0.31 eller högre. Du kan antingen använda Azure CLI 2.0 som finns i [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) eller installera det lokalt i enlighet med [instruktionerna](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

# <a name="cloud-shell-only"></a>Endast Cloud Shell

Om du använder Cloud Shell ändrar du arbetskatalogen till `/usr/$USER/clouddrive`, eftersom hemkatalogen saknar ledigt utrymme:

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Följande kommando skapar den nya resursgruppen ```batchai.quickstart``` på platsen USA, östra:

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>Skapa GPU-kluster

Följande kommando skapar ett GPU-kluster med en nod (VM-storleken är Standard_NC6) med Ubuntu DSVM som operativsystemavbildning:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Med Ubuntu DSVM kan du köra alla träningsjobb i Docker-behållare och köra de populäraste ramverken för djupinlärning direkt på den virtuella datorn.

Med alternativet `--generate-ssh-keys` genererar Azure CLI privata och offentliga ssh-nycklar, om de inte redan finns. Du kan komma åt klusternoderna med hjälp av den aktuella användarens namn och genererade ssh-nyckel.

Observera att om du använder Cloud Shell säkerhetskopierar du ~/.ssh-mappen till ett permanent lagringsutrymme.

Exempel på utdata:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
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
    "adminUserName": "alex",
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

# <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Följande kommando skapar ett nytt lagringskonto i samma region som resursgruppen batchai.repices. Uppdatera kommandot med ett unikt lagringskontonamn.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

Om det valda lagringskontonamnet inte är tillgängligt, kommer kommandot ovan rapportera motsvarande fel. I det här fallet väljer du ett annat namn och försöker igen.

# <a name="data-deployment"></a>Datadistribution

## <a name="download-the-training-script-and-training-data"></a>Hämta träningsskript och träningsdata

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

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>Skapa Azure-filresursen och distribuera träningsskriptet

Följande kommandon skapar Azure-filresurserna `scripts` och `logs`, samt kopierar träningsskript till `cntk`-mappen inuti `scripts`-resursen:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>Skapa en blobbehållare och distribuera träningsdata

Följande kommandon skapar Azure-blobbehållaren `data` och kopierar träningsdata till `mnist_cntk`-mappen:
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>Skicka träningsjobb

## <a name="prepare-job-configuration-file"></a>Förbereda jobbets konfigurationsfil

Skapa en konfigurationsfil för träningsjobbet `job.json` med följande innehåll:
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
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Den här konfigurationsfilen anger:

* `nodeCount` – antalet noder som krävs av jobbet (1 för den här snabbstarten);
* `cntkSettings` – anger sökvägen till träningsskriptet och kommandoradsargumenten. Kommandoradsargument innehåller sökvägen till träningsdata och målsökvägen för lagring av de modeller som genereras. `AZ_BATCHAI_OUTPUT_MODEL` är en miljövariabel som angetts av Batch AI baserat på utdatans katalogkonfiguration (se nedan);
* `stdOutErrPathPrefix` – sökväg där Batch AI skapar kataloger som innehåller jobbets utdata och loggar;
* `outputDirectories` – samling med utdatakataloger som ska skapas av Batch AI. För varje katalog skapar Batch AI en miljövariabel med namnet `AZ_BATCHAI_OUTPUT_<id>`, där `<id>` är katalogidentifieraren;
* `mountVolumes` – lista med filsystem som ska monteras under jobbkörningen. Filsystemen monteras under `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` är en miljövariabel som anges av Batch AI;
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` visar att namnet på lagringskontot kommer att anges under när jobbet skickas via parametern --storage-account-name eller miljövariabeln `AZURE_BATCHAI_STORAGE_ACCOUNT` på datorn.

## <a name="submit-the-job"></a>Skicka jobbet

Använd följande kommando för att skicka jobbet på klustret:

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

Exempel på utdata:
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
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
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>Övervaka jobbkörningen

Träningsskriptet rapporterar träningsförloppet i `stderr.txt`-filen som finns i standardutdatakatalogen. Du kan övervaka förloppet med följande kommando:

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

Exempel på utdata:
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
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
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

Strömningen stoppas när jobbet är slutfört (oavsett om det lyckades eller misslyckades).

# <a name="inspect-generated-model-files"></a>Inspektera genererade modellfiler

Jobbet lagrar de genererade modellfilerna i utdatakatalogen med `id`-attributet är lika med `MODEL`. Du kan se en lista med modellfilerna och hämta URL:er med följande kommando:

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
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

Alternativt kan du använda portalen eller Azure Storage Explorer till att inspektera de genererade filerna. Batch AI skapar en unik mappstruktur för varje utdata för att kunna skilja dem från andra jobb. Du kan hitta sökvägen till mappen som innehåller utdata med `jobOutputDirectoryPathSegment`-attributet för det skickade jobbet:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

Exempel på utdata:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>Ta bort resurser

Ta bort en resursgrupp och alla allokerade resurser med följande kommando:

```azurecli
az group delete -n batchai.quickstart -y
```
