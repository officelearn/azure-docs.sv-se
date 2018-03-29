---
title: Skapa en VM-skalningsuppsättning med Azure CLI 2.0 | Microsoft Docs
description: Lär dig hur du snabbt skapar en VM-skalningsuppsättning med Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 8794ea7d998293e7ea88ba780f67ef8a021f2298
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2018
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Skapa en VM-skalningsuppsättning med Azure CLI 2.0
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt, eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. I den här artikeln skapar du en VM-skalningsuppsättning med Azure CLI 2.0. Du kan också skapa en skalningsuppsättning med [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) eller [Azure Portal](virtual-machine-scale-sets-create-portal.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Innan du kan skapa en skalningsuppsättning skapar du en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss#az_vmss_create). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som genererar SSH-nycklar om det inte redan finns:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="install-nginx-webserver"></a>Installera NGINX-webbserver
Testa din skalningsuppsättning genom att använda tillägget för anpassat skript för att hämta och köra ett skript som installerar NGINX på VM-instanserna. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Mer information finns i [översikten över tillägget för anpassat skript](../virtual-machines/linux/extensions-customscript.md).

Tillämpa tillägget för anpassat skript som installerar NGINX på följande sätt:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Tillåt webbtrafik
Skapa en regel för belastningsutjämnare med [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) för att tillåta trafik till webbservern. I följande exempel skapas en regel med namnet *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>Testa din webbserver
Om du vill testa webbservern hämtar du den offentliga IP-adressen för belastningsutjämnaren med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). I följande exempel hämtas IP-adressen för *myScaleSetLBPublicIP* som skapas som en del av skalningsuppsättningen:

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Skriv den offentliga IP-adressen för belastningsutjämnaren i en webbläsare. Belastningsutjämnaren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Standardwebbsida i NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Rensa resurser
När resurserna inte behövs längre kan du använda [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nästa steg
I den här artikeln skapade du en grundläggande skalningsuppsättning och använde tillägget för anpassat skript för att installera en grundläggande NGINX-webbserver på VM-instanserna. Du kan få bättre skalbarhet och automatisering genom att utöka skalningsuppsättningarna med följande instruktionsartiklar:

- [Distribuera din app på VM-skalningsuppsättningar](virtual-machine-scale-sets-deploy-app.md)
- Skala automatiskt med [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) eller [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Använda automatiska uppgraderingar av operativsystemet för VM-instanser i skalningsuppsättningar](virtual-machine-scale-sets-automatic-upgrade.md)