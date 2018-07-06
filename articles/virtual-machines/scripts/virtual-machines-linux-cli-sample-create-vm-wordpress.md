---
title: Skriptexempel för Azure CLI – Skapa en virtuell Linux-dator med WordPress | Microsoft Docs
description: Skriptexempel för Azure CLI – Skapa en virtuell Linux-dator med Wordpress
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3427f53293cb23ec4596c32456d5f156d7065b44
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096118"
---
# <a name="create-a-vm-with-wordpress"></a>Skapa en VM med WordPress

Det här skriptet skapar en virtuell dator och använder sedan tillägget för Azure VM-anpassat skript för att installera Wordpress. När skriptet har körts, du kan komma åt webbplatsen för WordPress-konfiguration på `http://<public IP of VM>/wordpress`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-wordpress-mysql/create-wordpress-mysql.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#az_vm_open_port) | Skapar en regel för nätverkssäkerhetsgrupp som tillåter inkommande trafik. I det här exemplet öppnas port 80 för HTTP-trafik. |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Lägg till tillägget för anpassat skript till den virtuella datorn, som anropar ett skript för att installera WordPress. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
