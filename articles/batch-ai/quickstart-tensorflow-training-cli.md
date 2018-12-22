---
title: Snabbstart för Azure – Träning med djupinlärning – Azure CLI | Microsoft Docs
description: Snabbstart – Lär dig att snabbt träna ett neuralt TensorFlow-nätverk med djupinlärning och en enda GPU med hjälp av Azure Batch AI och Azure CLI
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408076"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>Snabbstart: Träna en modell för djupinlärning med Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

I den här snabbstarten får du lära dig att träna en exempelmodell med djupinlärning på en virtuell dator med GPU som hanteras av Azure Batch AI. Azure Batch AI är en hanterad tjänst som datatekniker och AI-forskare kan använda för att träna modeller för AI och maskininlärning i stor skala på kluster av virtuella Azure-datorer. 

I det här exemplet använder du Azure CLI för att konfigurera Azure Batch AI för att träna ett exempel på ett neuralt [TensorFlow](https://www.tensorflow.org/)-nätverk på [MNIST-databasen](http://yann.lecun.com/exdb/mnist/) för handskrivna siffror. När du har slutfört den här snabbstarten kommer du att ha grundläggande kunskaper om hur man använder Azure Batch AI för att träna en AI- eller maskininlärningsmodell och vara redo att testa olika typer av modellträning i större skala.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.38 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

Den här snabbstarten förutsätter att du kör kommandon i Bash, antingen i Cloud Shell eller på din lokala dator. Om du redan har slutfört snabbstarten för [hur man skapar ett Batch AI-kluster med Azure CLI](quickstart-create-cluster-cli.md) kan du hoppa över de två första stegen för att skapa en resursgrupp och ett Batch AI-kluster.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot `az group create`. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus2*. Glöm inte att välja platsen USA, östra 2 eller någon annan plats där Azure Batch AI-tjänsten är tillgänglig. 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Skapa ett Azure Batch AI-kluster

Använd först kommandot `az batchai workspace create` för att skapa en *Batch AI-arbetsyta*. Du behöver en arbetsyta för att organisera dina Batch AI-kluster och andra resurser.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

Du kan skapa ett Azure Batch AI-kluster med kommandot `az batchai cluster create`. I följande exempel skapas ett kluster med en nod och följande egenskaper:

* Använder VM-storleken NC6, som har en NVIDIA Tesla K80 GPU. Azure erbjuder flera VM-storlekar med olika grafikprocessorer (GPU:er) från NVIDIA.
* Kör en standardversion av en Ubuntu Server-avbildning som utformats för att vara värd för containerbaserade program. Du kan använda de flesta arbetsbelastningarna tillsammans med den här distributionen. 
* Lägger till ett användarkonto som heter *myusername*, och skapar SSH-nycklar om de inte redan finns på standardplatsen för nycklar (*~/.ssh*) i din lokala miljö.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

Utdata från kommandot visar klustrets egenskaper. Det tar några minuter att skapa och starta noden. Om du vill se status för klustret kör du kommandot `az batchai cluster show`.

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

Tidigt i processen med att skapa ett kluster liknar utdata det nedanstående och visar att klustret `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

Fortsätt med följande steg för att ladda upp träningsskriptet och skapa träningsjobb medan klustret ändras. Klustret är redo för att köra träningsjobbet när tillståndet är `steady` och den enskilda noden är `Idle`.

## <a name="upload-training-script"></a>Ladda upp träningsskript

Använd kommandot `az storage account create` för att skapa ett lagringskonto för att lagra dina träningsskript och träningsutdata.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

Skapa en Azure-filresurs med namnet `myshare` i kontot med hjälp av kommandot `az storage share create`:

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

Använd kommandot `az storage directory create` för att skapa kataloger på Azure-filresursen. Skapa katalogen `scripts` för träningsskriptet och `logs` för träningsutdata:

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

Skapa en lokal arbetskatalog i Bash-gränssnittet och ladda ned TensorFlow-exemplet [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py). Det här Python-skriptet ger träning för CNN-nätverk (Convolutional Neural Network) med MNIST-bilduppsättningen bestående av 60 000 handskrivna siffror från 0 till 9. Därefter testas modellen på en uppsättning textexempel.

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

Ladda upp skriptet till katalogen `scripts` i resursen med kommandot `az storage file upload`.

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>Skicka träningsjobb

Börja med att skapa ett Batch AI-*experiment* i din arbetsyta med hjälp av kommandot `az batchai experiment create`. Ett experiment är en logisk container för relaterade Batch AI-jobb.

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

I arbetskatalogen kan du skapa en konfigurationsfil för träningsjobbet (`job.json`) med följande innehåll. Du kan skicka med den här konfigurationsfilen när du skickar in träningsjobbet.

`job.json`-filen innehåller inställningar för att hitta Python-skriptfilen och köra den i en TensorFlow-container på GPU-noden. Den anger också var du vill spara filer för jobbets utdata på Azure-lagringskontot. `<AZURE_BATCHAI_STORAGE_ACCOUNT>` anger att namnet på lagringskontot kommer att anges när jobbet skickas.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Använd kommandot `az batchai job create` för att skicka jobbet på noden och skicka med konfigurationsfilen `job.json` med namnet på ditt lagringskonto:

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

Kommandot returnerar jobbegenskaperna och sedan tar några minuter på sig för att slutföra jobbet. Om du vill övervaka förloppet för det här jobbet använder du kommandot `az batchai job file stream` för att strömma filen `stdout-wk-0.txt` från katalogen med standardutdata på noden. Träningsskriptet genererar den här filen när jobbet börjar köras.  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

Exempel på utdata:

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

Strömningen upphör när jobbet har slutförts. Exempelskriptet tränar genom att köra 10 *epoker* eller genomkörningar av träningsdatauppsättningen. I det här exemplet har den tränade modellen fullföljt 10 epoker och har därefter en prestanda med så lite som 0,8 % testfel.

## <a name="get-job-output"></a>Jobbutdata

Azure Batch AI skapar en unik mappstruktur i lagringskontot för varje jobbs utdata. Ange miljövariabeln JOB_OUTPUT_PATH med den här sökvägen. Lista därefter utdatafilerna i lagringsutrymmet med hjälp av kommandot `az storage file list`:

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

Utdata liknar följande:

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

Använd kommandot `az storage file download` för att ladda ned en eller flera filer till din lokala arbetskatalog. Exempel:

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med Azure Batch AI-självstudier och -exempel ska du använda Batch AI-arbetsytan, klustret och det länkade lagringskontot som skapats i denna snabbstart. 

Du debiteras för Azure Batch AI-kluster medan noderna körs. Om du vill behålla klusterkonfigurationen och inte har några jobb att köra bör du ändra storleken på klustret till 0 noder. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Senare, när du har jobb att köra, kan du byta till 1 eller flera noder. När du inte längre behöver ett kluster kan du ta bort det med kommandot `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

När de inte längre behövs kan du använda kommandot `az group delete` för att ta bort resursgruppen för Batch AI- och lagringsresurserna. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du lärt dig hur du använder Azure Batch AI och Azure CLI för att träna ett exempel på en TensorFlow-modell för djupinlärning på en virtuell dator med en enda GPU. Mer information om hur du distribuerar modellträning över ett större GPU-kluster finns i självstudien för Azure Batch AI.

> [!div class="nextstepaction"]
> [Självstudie: Distribuerad träning](./tutorial-horovod-tensorflow.md)