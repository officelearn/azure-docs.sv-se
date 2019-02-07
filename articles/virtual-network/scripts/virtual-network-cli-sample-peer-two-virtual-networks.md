---
title: Skriptexempel för Azure CLI – Peering i två virtuella nätverk | Microsoft Docs
description: Skriptexempel för Azure CLI – Peering i två virtuella nätverk.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 142b36e5c1f0908ccfc1bf2e475442028e02edf7
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728460"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Skriptexempel för peering i två virtuella nätverk

Skriptexemplet skapar och ansluter två virtuella nätverk i samma region via Azures nätverk. När du har kört skriptet har du en peering mellan två virtuella nätverk.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/bash) eller från en lokal installation av Azure CLI. Om du använder CLI lokalt kräver skriptet att du kör version 2.0.28 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör CLI lokalt måste du också köra `az login` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network vnet peering create](/cli/azure/network/vnet/peering) | Skapar en peering mellan två virtuella nätverk.  |
| [az group delete](/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Du kan hitta fler skriptexempel om virtuella nätverk för CLI i [CLI-exempel för virtuella nätverk](../cli-samples.md).
