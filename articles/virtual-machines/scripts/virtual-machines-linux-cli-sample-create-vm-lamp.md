---
title: Exempelskript för Azure CLI – Distribuera LAMP-stacken i en belastningsutjämnad skalningsuppsättning med virtuella datorer | Microsoft Docs
description: Använd ett tillägg för anpassat skript för att distribuera LAMP-stacken i en belastningsutjämnad skalningsuppsättning med virtuella datorer i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 1764fc15a888fbe15cc14b990721240d1baf3c40
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29851674"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Distribuera LAMP-stacken i en belastningsutjämnad skalningsuppsättning med virtuella datorer

Det här exemplet skapar en skalningsuppsättning med virtuella datorer och använder ett tillägg som kör ett anpassat skript för att distribuera LAMP-stacken på alla virtuella datorer i skalningsuppsättningen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>Anslut

Använd den här koden för att se hur du ansluter till dina virtuella datorer och din skalningsuppsättning.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen med virtuella datorer samt alla relaterade resurser.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, belastningsutjämnare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Skapar en skalningsuppsättning med virtuella datorer |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Lägg till en belastningsutjämnad slutpunkt |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#az_vmss_extension_set) | Skapa det tillägg som kör det anpassade skriptet på distributionen av en virtuell dator |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#az_vmss_update_instances) | Kör det anpassade skriptet på de VM-instanser som distribuerades innan tillägget tillämpades på skalningsuppsättningen. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) | Skala upp skalningsuppsättningen genom att lägga till flera VM-instanser. Det anpassade skriptet körs på dem när de distribueras. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list) | Hämta IP-adresserna för de virtuella datorer som skapats av exemplet. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_show) | Hämta klient- och serverdelsportarna som används av belastningsutjämnaren. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
