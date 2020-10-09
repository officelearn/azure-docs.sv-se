---
title: Skript exempel för Azure CLI – skapa två virtuella datorer med en intern och extern NSG
description: Skriptexempel för Azure CLI – Skapa två virtuella datorer med en intern och extern NSG
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0d0e3dd53c592c991e342240b9c973e1677969d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87498386"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Säkra nätverkstrafik mellan virtuella datorer

Det här skriptet skapar två virtuella datorer och säkrar inkommande trafik till båda. En virtuell dator är tillgänglig på internet och har en nätverkssäkerhetsgrupp (NSG) som konfigurerats för att tillåta trafik på port 3389 och port 80. Den andra virtuella datorn är inte tillgänglig på internet och har en NSG som konfigurerats för att endast tillåta trafik från den första virtuella datorn. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet) | Skapar ett undernät. |
| [az vm create](/cli/azure/vm) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az network nsg rule update](/cli/azure/network/nsg/rule) | Uppdaterar en NSG-regel. I det här exemplet uppdateras klientdelsregeln för att släppa igenom trafik från serverdelens undernät. |
| [az network nsg rule list](/cli/azure/network/nsg/rule) | Returnerar information om en regel för nätverkssäkerhetsgrupp. I det här exemplet lagras regelnamnet i en variabel för användning senare i skriptet. |
| [az group delete](/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
