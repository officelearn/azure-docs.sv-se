---
title: Azure CLI 2.0-exempel – använd en anpassad virtuell datoravbildning | Microsoft Docs
description: Azure CLI 2.0-exempel
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c13b8275b69579e6645a5a0fa0c9d446c0246a48
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697508"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli-20"></a>Skapa en VM-skalningsuppsättning från en anpassad virtuell datoravbildning med Azure CLI 2.0
Det här skriptet skapar en VM-skalningsuppsättning som använder en anpassad virtuell datoravbildning som källa för de virtuella datorinstanserna.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.sh "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa en resursgrupp, en VM-skalningsuppsättning och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Skapar VM-skalningsuppsättningen och ansluter den till det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen. En belastningsutjämnare skapas även för att distribuera trafik till flera virtuella datorinstanser. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az group delete](/cli/azure/ad/group#delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure CLI 2.0 finns i [Azure CLI 2.0-dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure CLI 2.0-skriptexempel för VM-skalningsuppsättningar finns i [dokumentationen för VM-skalningsuppsättningar i Azure](../cli-samples.md).