---
title: Snabbstart om Azure – Skapa Batch AI-kluster – Azure CLI | Microsoft Docs
description: Snabbstart – skapa ett Batch AI-kluster för träning av modeller för maskininlärning och AI – Azure CLI
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
ms.openlocfilehash: 0d4ba7edfb22a6710222c854ceb2bf86284d2d77
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057967"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>Snabbstart: Skapa ett kluster för Batch AI-träningsjobb med Azure CLI

Den här snabbstarten visar hur du använder Azure CLI för att skapa ett Batch AI-kluster som du kan använda för att träna modeller för AI och maskininlärning. Batch AI är en hanterad tjänst som datatekniker och AI-forskare kan använda för att träna modeller för AI och maskininlärning i stor skala på kluster av virtuella Azure-datorer.

Klustret har till en början en enskild GPU-nod. När du har slutfört den här snabbstarten har du ett kluster som du kan skala upp och använda för att träna dina modeller. Skicka träningsjobb till klustret med Batch AI, [Azure Machine Learning-verktyg](../machine-learning/service/overview-what-is-azure-ml.md) eller [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai).

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här snabbstarten att du kör Azure CLI version 2.0.38 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

Den här snabbstarten förutsätter att du kör kommandon i Bash, antingen i Cloud Shell eller på din lokala dator.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot `az group create`. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus2*. Se till att välja en plats, till exempel USA, östra 2, där Batch AI-tjänsten är tillgänglig.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>Skapa ett Batch AI-kluster

Använd först kommandot `az batchai workspace create` för att skapa en *Batch AI-arbetsyta*. Du behöver en arbetsyta för att organisera dina Batch AI-kluster och andra resurser.

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup 
```

Du kan skapa ett Batch AI-kluster med kommandot `az batchai cluster create`. I följande exempel skapas ett kluster med följande egenskaper:

* Innehåller en enskild nod i storleken NC6 VM, som har en NVIDIA Tesla K80 GPU. 
* Kör en standardversion av en Ubuntu Server-avbildning som utformats för att vara värd för containerbaserade program, som du kan använda för de flesta arbetsbelastningar vid träning. 
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

Utdata från kommandot visar klustrets egenskaper. Det tar några minuter att skapa och starta noden. Om du vill se klustrets status kör du kommandot `az batchai cluster show`. 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

Tidigt under processen när klustret skapas ser utdata ut ungefär som följande, som visar klustret i tillståndet `resizing`:

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
Klustret är klart att användas när tillståndet är `steady` och den enskilda noden är `Idle`.

## <a name="list-cluster-nodes"></a>Lista klusternoder 

Om du vill ansluta till klusternoderna (i det här fallet en enskild nod) för att installera program eller utföra underhåll så får du anslutningsinformation genom att köra kommandot `az batchai cluster node list`:


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

JSON-utdatan liknar följande:

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
Använd informationen för att upprätta en SSH-anslutning till noden. Ersätt till exempel med rätt IP-adress för din nod i följande kommando:

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
Avsluta SSH-sessionen för att fortsätta.

## <a name="resize-the-cluster"></a>Ändra storlek på klustret

När du använder ditt kluster för att köra ett träningsjobb kanske du behöver fler beräkningsresurser. Om du till exempel vill öka storleken till 2 noder för ett distribuerat träningsjobb kör du kommandot [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize):

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

Det tar några minuter att ändra storleken på klustret.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med Azure Batch AI-självstudier och -exempel ska du använda Batch AI-arbetsytan som skapats i denna snabbstart. 

Du debiteras för Azure Batch AI-kluster medan noderna körs. Om du vill behålla klusterkonfigurationen och inte har några jobb att köra bör du ändra storleken på klustret till 0 noder. 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

Senare, när du har jobb att köra, kan du byta till 1 eller flera noder. När du behöver inte längre ett kluster kan du ta bort det med kommandot `az batchai cluster delete`:

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
```

När de inte längre behövs kan du använda kommandot `az group delete` för att ta bort resursgruppen för Batch AI-resurserna. 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Batch AI-kluster med Azure CLI. Om du vill ha mer information om hur du använder Batch AI-kluster för att träna en modell fortsätter du till snabbstarten för att träna en modell för djupinlärning.

> [!div class="nextstepaction"]
> [Träna en modell för djupinlärning](./quickstart-tensorflow-training-cli.md)
