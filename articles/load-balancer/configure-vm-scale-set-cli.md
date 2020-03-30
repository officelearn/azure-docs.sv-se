---
title: Konfigurera skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer - Azure CLI
description: Lär dig hur du konfigurerar en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349740"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Konfigurera en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer med Azure CLI

I den här artikeln får du lära dig hur du konfigurerar en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer. 

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration.
- En befintlig standard sku belastningsutjämnare i prenumerationen där den virtuella datorn skala set kommer att distribueras.
- Ett Virtuellt Azure-nätverk för skalningsuppsättningen för den virtuella datorn.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att använda CLI lokalt kräver den här artikeln att du har en version av Azure CLI version 2.0.28 eller senare installerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI

Logga in på Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuera en skalningsuppsättning för virtuella datorer med befintlig belastningsutjämnare

Ersätt värdena inom parentes med namnen på resurserna i konfigurationen.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

I exemplet nedan distribueras en skalningsuppsättning för virtuella datorer med:

- Skala uppsättning för virtuell dator med namnet **myVMSS**
- Azure Load Balancer heter **myLoadBalancer**
- Backend-backend-pool med namnet **myBackendPool**
- Azure Virtual Network heter **myVnet**
- Undernät med namnet **mySubnet**
- Resursgrupp med namnet **myResourceGroup**
- Ubuntu Server-avbildning för skalningsuppsättningen för den virtuella datorn

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> När skalningsuppsättningen har skapats kan serverdaporten inte ändras för en belastningsutjämningsregel som används av en hälsoavsökning av belastningsutjämnaren. Om du vill ändra porten kan du ta bort hälsoavsökningen genom att uppdatera Azure-skalauppsättningen för virtuella datorer, uppdatera porten och sedan konfigurera hälsoavsökningen igen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer.  Mer information om skalningsuppsättningar och belastningsutjämnare för virtuella datorer finns i:

- [Vad är Azure Load Balancer?](load-balancer-overview.md)
- [Vad är VM-skalningsuppsättningar?](../virtual-machine-scale-sets/overview.md)
                                