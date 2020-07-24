---
title: Skriptexempel för Azure CLI – Skapa en virtuell Windows Server 2016-dator med DSC
description: Skriptexempel för Azure CLI – Skapa en virtuell Windows Server 2016-dator med DSC
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
ms.custom: mvc
ms.openlocfilehash: 9e4333891d701e1c505e5f45f9012057aa07d808
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088656"
---
# <a name="create-a-vm-with-iis-using-dsc"></a>Skapa en virtuell dator med IIS med hjälp av DSC

Det här skriptet skapar en virtuell dator och använder sedan det anpassade DSC-skripttillägget för virtuella Azure-datorer till att installera och konfigurera IIS. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-windows-iis-using-dsc/create-windows-iis-using-dsc.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az vm create](/cli/azure/vm) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az vm extension set](/cli/azure/vm) | Lägger till ett anpassat skripttillägg på den virtuella datorn som sedan anropar ett skript som installerar IIS. |
| [az vm open-port](/cli/azure/vm) | Skapar en regel för nätverkssäkerhetsgrupp som tillåter inkommande trafik. I det här exemplet öppnas port 80 för HTTP-trafik. |
| [az group delete](/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
