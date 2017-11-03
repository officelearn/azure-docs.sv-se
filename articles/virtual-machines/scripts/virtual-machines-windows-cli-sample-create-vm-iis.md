---
title: Azure CLI skriptexempel - installera IIS | Microsoft Docs
description: Azure CLI skriptexempel - installera IIS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: nepeters
ms.openlocfilehash: 224fbebd40a44dfb2e032150612467af3a8aca8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Snabbt skapa en virtuell dator med Azure CLI

Det här skriptet skapar en Azure-dator som kör Windows Server 2016 och använder tillägget för Azure Virtual Machine anpassat skript för att installera IIS. När skriptet har körts kan du komma åt IIS-standardwebbplatsen på den offentliga IP-adressen för den virtuella datorn.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-windows-iis/create-vm-windows-iis.sh "Quick Create VM")]

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
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Skapar den virtuella datorn och ansluter till nätverkskort, virtuella nätverk, undernät och nätverkssäkerhetsgruppen. Det här kommandot anger också avbildningen av virtuella datorn ska användas och administrativa autentiseringsuppgifter.  |
| [AZ vm öppna-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Skapar en grupp nätverkssäkerhetsregeln för att tillåta inkommande trafik. I det här exemplet används port 80 för HTTP-trafik. |
| [Azure vm-tillägget uppsättningen](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Lägger till och kör ett tillägg för virtuell dator till en virtuell dator. I det här exemplet används tillägget för anpassat skript för att installera IIS.|
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [Virtuella för Windows Azure-dokumentationen](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
