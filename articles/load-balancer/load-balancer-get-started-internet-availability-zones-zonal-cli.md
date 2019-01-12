---
title: Skapa en belastningsutjämnare med zonindelad frontend - Azure CLI
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar en Standardbelastningsutjämnare med zonindelad klientdel med Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: c9926b78955a193f750c1e5ca3ff7461b4192df5
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247019"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Skapa en Standardbelastningsutjämnare med zonindelad klientdel med Azure CLI

Den här artikeln visar hur du skapar en offentlig [Standardbelastningsutjämnare](https://aka.ms/azureloadbalancerstandard) med en zonindelad klientdel. Har en zonindelad klientdel innebär det att alla inkommande eller utgående flöden hanteras av en enskild zon i en region. Du kan skapa en belastningsutjämnare med en zonindelad klientdel med hjälp av en zonindelad offentlig IP-adress i den frontend-konfigurationen. Information om hur tillgänglighetszoner fungerar med Standard Load Balancer finns i [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md). 

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

Skapa en offentlig Standard Load Balancer med den offentliga IP som du skapade i föregående steg med följande kommando:

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



