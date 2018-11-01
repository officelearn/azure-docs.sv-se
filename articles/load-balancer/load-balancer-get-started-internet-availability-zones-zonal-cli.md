---
title: Skapa en offentlig Load Balancer Standard med zonindelad klientdel med Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en offentlig Load Balancer Standard med zonindelad klientdel med Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 3a0fc37b8e2865163ae6c55813d145a568d796e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414503"
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-cli"></a>Skapa en offentlig Load Balancer Standard med zonindelad klientdel med Azure CLI

Den här artikeln visar hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonindelad klientdel. Har en zonindelad klientdel innebär det att alla inkommande eller utgående flöden hanteras av en enskild zon i en region. Du kan skapa en belastningsutjämnare med en zonindelad klientdel med hjälp av en zonindelad offentlig IP-adress i den frontend-konfigurationen. Information om hur tillgänglighetszoner fungerar med Standard Load Balancer finns i [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kontrollerar du att du har installerat senast [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och är inloggad på ett Azure-konto med [az-inloggning](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
> Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och VM-storlekar som du kan prova tillgänglighetszoner med finns i [översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med hjälp av följande kommando:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Skapa en offentlig IP-standardadress

Skapa en zonindelad offentlig IP-adress med följande kommando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

Skapa en offentlig Load Balancer Standard med den offentliga IP som du skapade i föregående steg med följande kommando:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Skapa en LB-avsökning på port 80

Skapa en hälsoavsökning för belastningsutjämnaren med följande kommando:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Skapa en LB-regel för port 80

Skapa en regel för belastningsutjämnaren med följande kommando:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).



