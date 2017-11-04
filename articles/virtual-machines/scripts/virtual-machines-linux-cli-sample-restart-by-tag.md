---
title: Azure CLI skriptexempel - omstart VMs | Microsoft Docs
description: 'Skriptexempel Azure CLI - omstart av virtuella datorer efter tagg och -ID: t'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: ea114f484c774573b7d219cff9102a7308af356e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="restart-vms"></a>Starta om virtuella datorer

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Det här exemplet visas ett par olika sätt att hämta vissa virtuella datorer och starta om dem.

Först startar om alla virtuella datorer i resursgruppen.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

Andra hämtar taggade virtuella datorer med hjälp av `az resouce list` och filtrerar till resurser som virtuella datorer och startar om dessa virtuella datorer.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Det här exemplet fungerar i ett Bash-gränssnitt. Alternativen på Azure CLI skriptkörning på Windows-klient kan se [kör Windows Azure CLI](../windows/cli-options.md).


## <a name="sample-script"></a>Exempelskript

Exemplet har tre skript.
Den första som etablerar de virtuella datorerna.
Den använder alternativet Nej vänta så att kommandot returnerar utan att vänta på varje virtuell dator som ska etableras.
Andra väntar på att de virtuella datorerna till helt etablerad.
Skriptet tredje startar om alla de virtuella datorerna som var etablerade och sedan bara taggade virtuella datorer.

### <a name="provision-the-vms"></a>Etablera virtuella datorer

Det här skriptet skapar en resursgrupp och skapar sedan tre virtuella datorer startas om.
Två av dem märks.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Vänta

Det här skriptet kontrollerar etablering statusen var 20 sekunder tills alla tre virtuella datorer är etablerad, eller en av dem inte kan etablera.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Starta om de virtuella datorerna

Det här skriptet startar om alla virtuella datorer i resursgruppen och startas sedan om bara taggade virtuella datorer.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Efter skriptexempel har körts användas följande kommando för att ta bort resursgrupper, virtuella datorer och alla relaterade resurser.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon för att skapa en resursgrupp, virtuella, tillgänglighetsuppsättning, belastningsutjämnare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Skapa AZ grupp](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ vm](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Skapar de virtuella datorerna.  |
| [AZ vm lista](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Används med `--query` att säkerställa att de virtuella datorerna etableras innan du startar om dem, och för att hämta ID för virtuella datorer starta om dem. |
| [lista över resurser AZ](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Används med `--query` att hämta ID: N för de virtuella datorerna med taggen. |
| [AZ vm-omstart](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Startar om de virtuella datorerna. |
| [ta bort grupp AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/overview).

Ytterligare virtuella CLI skriptexempel finns i den [virtuella Azure Linux-datorn dokumentationen](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
