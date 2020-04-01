---
title: Skapa en DNS-zon och spela in för ett domännamn - Azure CLI - Azure DNS
description: I det här Azure CLI-skriptexemplet visas hur du skapar en DNS-zon och -post för ett domännamn
services: dns
author: rohinkoul
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: b1db9ffb895470dd9224dc020eb82b0bbf6560c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939312"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI-skriptexempel: Skapa en DNS-zon och -post

I det här Azure CLI-skriptexemplet skapas en DNS-zon och -post för ett domännamn. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, DNS-zonen och alla relaterade resurser.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | Skapar en Azure DNS-zon. |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | Lägger till en *A*-post för en DNS-zon. |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | Visar alla *A*-postuppsättningar i en DNS-zon. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

