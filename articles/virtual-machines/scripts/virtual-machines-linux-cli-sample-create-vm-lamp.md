---
title: Skript exempel för Azure CLI – distribuera LAMP-stacken i en belastningsutjämnad skalnings uppsättning för virtuella datorer
description: Använd ett tillägg för anpassat skript för att distribuera LAMP-stacken i en belastningsutjämnad skalningsuppsättning med virtuella datorer i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc
ms.date: 04/05/2017
ms.openlocfilehash: 471690c5c0f5ebb4cea111b85cab1fe0524a36cd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509731"
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

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az vmss create](/cli/azure/vmss) | Skapar en skalningsuppsättning med virtuella datorer |
| [az network lb rule create](/cli/azure/network/lb/rule) | Lägg till en belastningsutjämnad slutpunkt |
| [az vmss extension set](/cli/azure/vmss/extension) | Skapa det tillägg som kör det anpassade skriptet på distributionen av en virtuell dator |
| [az vmss update-instances](/cli/azure/vmss) | Kör det anpassade skriptet på de VM-instanser som distribuerades innan tillägget tillämpades på skalningsuppsättningen. |
| [az vmss scale](/cli/azure/vmss) | Skala upp skalningsuppsättningen genom att lägga till flera VM-instanser. Det anpassade skriptet körs på dem när de distribueras. |
| [az network public-ip list](/cli/azure/network/public-ip) | Hämta IP-adresserna för de virtuella datorer som skapats av exemplet. |
| [az network lb show](/cli/azure/network/lb) | Hämta klient- och serverdelsportarna som används av lastbalanseraren. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
