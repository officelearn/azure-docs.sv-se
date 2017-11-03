---
title: Azure CLI Script exempel - skapa en Windows Server-dator | Microsoft Docs
description: Azure CLI Script exempel - skapa en Windows Server-dator
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: 037ea2c0d4c61c7d8ca3b058b5c41c2e296da96f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-with-the-azure-cli"></a>Skapa en virtuell dator med Azure CLI

Det här skriptet skapar en Azure-dator som kör Windows Server 2016. När skriptet har körts kan du komma åt den virtuella datorn via en fjärrskrivbordsanslutning.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-detailed/create-windows-vm-detailed.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ network vnet](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [Skapa AZ nätverket offentliga-ip](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Skapar en offentlig IP-adress med en statisk IP-adress och en tillhörande DNS-namn. |
| [Skapa AZ nätverket nsg](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Skapar en nätverkssäkerhetsgrupp (NSG), vilket är en säkerhetsgräns mellan internet och den virtuella datorn. |
| [Skapa AZ nätverket nic](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Skapar ett virtuellt nätverkskort som kopplas till den virtuella nätverk och undernät NSG. |
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Skapar den virtuella datorn och ansluter till nätverkskort, virtuella nätverk, undernät och NSG. Det här kommandot anger också avbildning av virtuell dator som ska användas och administrativa autentiseringsuppgifter.  |
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
