---
title: "Azure CLI-skript exempel – skapa två virtuella datorer med en interna och externa NSG | Microsoft Docs"
description: "Azure CLI-skript exempel – skapa två virtuella datorer med interna och externa NSG"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 61e1c060f7dd9857311f92573d075f97b724aecf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Säkra nätverkstrafik mellan virtuella datorer

Det här skriptet skapar två virtuella datorer och skyddar inkommande trafik till båda. En virtuell dator är tillgänglig på internet och har en nätverkssäkerhetsgrupp (NSG) konfigurerat för att tillåta trafik på port 22 och port 80. Den andra virtuella datorn är inte tillgänglig på internet och har en NSG som konfigurerats för att endast tillåta trafik från den första virtuella datorn. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ network vnet](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [Skapa AZ undernät för virtuellt nätverk](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Skapar ett undernät. |
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Skapar den virtuella datorn och ansluter till nätverkskort, virtuella nätverk, undernät och NSG. Det här kommandot anger också avbildning av virtuell dator som ska användas och administrativa autentiseringsuppgifter.  |
| [listan över AZ nätverk nsg regel](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_list) | Returnerar information om en grupp för nätverkssäkerhetsregeln. I det här exemplet lagras Regelnamnet i en variabel för användning senare i skriptet. |
| [Uppdatera regel för AZ nätverket nsg](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_update) | Uppdaterar en NSG-regel. I det här exemplet uppdateras backend-regeln för att släppa igenom trafik från frontend undernätet. |
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
