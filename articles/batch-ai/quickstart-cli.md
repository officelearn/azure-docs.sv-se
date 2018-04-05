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
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Kör ett CNTK-utbildningsjobb med hjälp av Azure CLI

Den här snabbstarten visar dig hur du kan använd Azures kommandoradsgränssnitt (CLI) för att köra ett utbildningsjobb med Microsoft Cognitive Toolkit (CNTK) med hjälp av Batch AI-tjänsten. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript.

I det här exemplet använder du MNIST-databasen med handritade bilder för att träna ett CNN (Convolutional Neural Network) i ett GPU-kluster med en nod som hanteras av Batch AI. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

För den här snabbstarten krävs att du kör den senaste Azure CLI-versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

Batch AI-resursprovidrarna måste också ha registrerats en gång för din prenumeration med hjälp av Azure Cloud Shell eller Azure CLI. En providerregistrering kan ta upp till 15 minuter.

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Batch AI-kluster och jobb är Azure-resurser och måste placeras i en Azure-resursgrupp.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create).

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*. Därefter använder den kommandot [az configure](/cli/azure/reference-index#az_configure) för att konfigurera den här resursgruppen och platsen som standard.

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>Att konfigurera standardvärden för kommandot `az` är ett valfritt steg. Du kan välja att inte ange standardvärdena. Om du väljer att ange standardvärdena bör du ta bort standardinställningarna efter et att du har slutfört självstudiekursen. Ta bort standardinställningarna med hjälp av följande kommandon:
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

I den här snabbstarten använda ett Azure Storage-konto som värd för data och skript för utbildningsjobbet. Skapa ett lagringskonto med kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>Varje lagringskonto måste ha ett unikt namn. I föregående `az`-kommando och andra liknande kommandon i den här självstudiekursen ersätter du värdet för inställningen `mystorageaccount` med namnet på ditt lagringskonto.

## <a name="prepare-azure-file-share"></a>Förbered en Azure-filresurs

I illustrationssyfte använder den här snabbstarten en Azure-filresurs som värd för utbildningsinformationen och skripten för utbildningsjobbet.

1. Skapa en filresurs med namnet *batchaiquickstart* med hjälp av kommandot [az storage share create](/cli/azure/storage/share#az_storage_share_create).

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. Skapa en katalog i resursen med namnet *mnistcntksample* med hjälp av kommandot [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create).

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. Hämta [exempelpaketet](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) och packa upp det. Överför innehållet till en katalog med hjälp av kommandot [az storage file upload](/cli/azure/storage/file#az_storage_file_upload):

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>Skapa GPU-kluster
Använd kommandot [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create) för att skapa ett Batch AI-kluster som består av en enskild GPU VM-nod. I det här exemplet kör en virtuell dator Ubuntu LTS standardavbildningen. Ange `image UbuntuDSVM` i stället om du vill köra Microsoft Deep Learning Virtual Machine, som stöder ytterligare utbildningsramverk. NC6-storleken har en NVIDIA K80 GPU. Montera filresursen i en mapp med namnet *azurefileshare*. Den fullständiga sökvägen till den här mappen på GPU-beräkningsnoden är $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


När klustret har skapats ser utdata ut ungefär så här:

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>Hämta klusterstatus

Om du vill få en översikt över klusterstatusen för klustret kör du kommandot [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list):

```azurecli
az batchai cluster list -o table
```

De utdata som genereras liknar följande:

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

Mer information får du genom att köra kommandot [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show). Den returnerar alla de klusteregenskaper som visas när klustret har skapats.

Klustret är klart när noderna har allokerats och är färdigförberedda (se `nodeStateCounts`-attributet). Om något går fel så innehåller `errors`-attributet felbeskrivningen.

## <a name="create-training-job"></a>Skapa utbildningsjobb

När klustret är klart så konfigurerar du utbildningsjobbet och skickar in det.

1. Skapa en JSON-mallfil för jobbskapande med namnet job.json:

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. Skapa ett jobb med namnet *myjob* som ska köras på klustret med kommandot [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create):

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

De utdata som genereras liknar följande:

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>Övervaka jobb

Använd kommandot [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list) om du vill få en översikt över jobbstatusen:

```azurecli
az batchai job list -o table
```

De utdata som genereras liknar följande:

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

Mer information får du genom att köra kommandot [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show).

`executionState` innehåller jobbets aktuella körningsstatus:

* `queued`: jobbet väntar på att klusternoderna ska bli tillgängliga
* `running`: jobbet körs
*   `succeeded` (eller `failed`): jobbet har slutförts och `executionInfo` innehåller information om resultatet


## <a name="list-stdout-and-stderr-output"></a>Lista stdout- och stderr-utdata
Använd kommandot [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files) när du ska lista länkar till stdout- och stderr-loggfilerna:

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

De utdata som genereras liknar följande:

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>Observera utdata

Du kan strömma eller spåra ett jobbs utdatafiler medan jobbet körs. I följande exempel används kommandot [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) för att strömma stderr.txt-loggen:

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

De utdata som genereras liknar följande. Avbryt utdata genom att trycka på [Ctrl]-[C].

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>Ta bort resurser

Använd kommandot [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete) när du vill ta bort jobbet:

```azurecli
az batchai job delete --name myjob
```
Använd kommandot [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete) när du vill ta bort klustret:

```azurecli
az batchai cluster delete --name mycluster
```

Använd kommandot `az group delete` när du vill ta bort resursgruppen som du skapade för den här snabbstarten:

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Återställ standardinställningarna för Azure CLI 2.0

Ta bort de tidigare konfigurerade standardinställningarna för platsen och resursgruppen:

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du kör ett CNTK-utbildningsjobb i ett Batch AI-kluster med hjälp av Azure CLI. Mer information om hur du använder Batch AI med olika verktyg finns i [utbildningsrecept](https://github.com/Azure/BatchAI).

Mer information om hur du använder Azure CLI 2.0 för att hantera Batch AI finns i [github-dokumentationen](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
