---
title: Skapa en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel för offentlig IP-adress med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel för offentlig IP-adress med hjälp av Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 1a430f5c6349741e5d04626158dc89d42169a15b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Skapa en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel med Azure CLI

Den här artikeln går igenom hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonredundant klientdel med en Standard offentliga IP-adress.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="register-for-availability-zones-preview"></a>Registrera dig för förhandsgranskning tillgänglighet zoner

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.17 eller högre.  Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

> [!NOTE]
> Tillgänglighet zoner i förhandsversionen och är redo för din utveckla och testa scenarier. Support är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Kontrollera att du har installerat senast [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och är inloggad på ett Azure-konto med [az inloggningen](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med följande kommando:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Skapa en offentlig IP-Standard

Skapa en offentlig IP-Standard med följande kommando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Skapa en belastningsutjämnare

Skapa en offentlig belastningen belastningsutjämnaren Standard med Standard offentliga IP-adress som du skapade i föregående steg med följande kommando:

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Skapa en LB-avsökning på port 80

Skapa en belastningsutjämnaren, hälsoavsökningen med följande kommando:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Skapa en regel för LB för port 80

Skapa en regel för belastningsutjämnare med följande kommando:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Nästa steg
- Lär dig hur [skapa en offentlig IP-adress i en zon för tillgänglighet](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



