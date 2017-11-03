---
title: Azure CLI Script exempel - skapa en virtuell Linux-dator med NGINX | Microsoft Docs
description: Azure CLI Script exempel - skapa en virtuell Linux-dator med NGINX
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0a6c33d84f1fab85e6ed2933c47c041ca2e59520
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-nginx"></a>Skapa en virtuell dator med NGINX

Det här skriptet skapar en virtuell Azure-dator och använder tillägget för Azure Virtual Machine anpassat skript för att installera NGINX. När skriptet har körts kan du komma åt en demo-webbplats på den offentliga IP-adressen för den virtuella datorn.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Anpassat skripttillägg

Tillägget för anpassat skript kopierar det här skriptet till den virtuella datorn. Skriptet körs sedan för att installera och konfigurera en NGINX-webbserver. 

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

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
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Skapar den virtuella datorn. Det här kommandot anger också avbildning av virtuell dator som ska användas och administrativa autentiseringsuppgifter.  |
| [AZ vm öppna-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Skapar en grupp nätverkssäkerhetsregeln för att tillåta inkommande trafik. I det här exemplet används port 80 för HTTP-trafik. |
| [Azure vm-tillägget uppsättningen](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Lägger till och kör ett tillägg för virtuell dator till en virtuell dator. I det här exemplet används tillägget för anpassat skript för att installera NGINX.|
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
