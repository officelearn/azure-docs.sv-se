---
title: Skriptexempel Azure CLI - Peer två virtuella nätverk | Microsoft Docs
description: Azure CLI skriptexempel - Peer två virtuella nätverk.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 244d7f6ff64643386c417d708f7fb1e9bbc34209
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="peer-two-virtual-networks"></a>Peer-två virtuella nätverk

Det här skriptet skapar och ansluter två virtuella nätverk i samma region via Azure-nätverk. När du kör skriptet har en peering mellan två virtuella nätverk.

Du kan köra skriptet från Azure [moln Shell](https://shell.azure.com/bash), eller från en lokal installation av Azure CLI. Om du använder CLI lokalt skriptet kräver att du har version 2.0.28 eller senare. Du hittar den installerade versionen genom att köra `az --version`. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Om du kör CLI lokalt, måste du också köra `az login` att skapa en anslutning med Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i följande tabell länkar till kommando-specifika dokumentation:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [Skapa AZ network vnet-peering](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) | Skapar en peering mellan två virtuella nätverk.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare virtuella nätverk CLI skriptexempel finns i [virtuella nätverk CLI exempel](../cli-samples.md).
