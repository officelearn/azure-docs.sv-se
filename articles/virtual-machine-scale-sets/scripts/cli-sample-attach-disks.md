---
title: Azure CLI-exempel – Anslut och använd datadiskar | Microsoft Docs
description: Azure CLI-exempel
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
ms.openlocfilehash: 6966aead6ced88e0ff9b201dd12bec0a16799907
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661386"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-the-azure-cli"></a>Anslut och använd datadiskar med en VM-skalningsuppsättning med Azure CLI
Det här skriptet skapar en VM-skalningsuppsättning och bifogar och förbereder datadiskar.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.sh "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa en resursgrupp, en VM-skalningsuppsättning och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/ad/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az vmss create](/cli/azure/vmss) | Skapar VM-skalningsuppsättningen och ansluter den till det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen. En lastbalanserare skapas även för att distribuera trafik till flera virtuella datorinstanser. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az vmss disk attach](/cli/azure/vmss/disk) | Skapar och ansluter en datadisk till VM-skalningsuppsättningen. |
| [az vmss extension set](/cli/azure/vmss/extension) | Installerar det anpassade Azure-skripttillägget för att köra ett skript som förbereder datadiskarna på varje virtuell datorinstans. |
| [az group delete](/cli/azure/ad/group) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare Azure CLI-skriptexempel för skalningsuppsättningar för virtuella datorer finns i [dokumentationen för Azure-skalningsuppsättningar för virtuella datorer](../cli-samples.md).
