---
title: Skapa en belastningsutjämnare med zonredundant frontend - Azure CLI
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar en offentlig Standard Load Balancer med zonredundant offentlig IP-adress klientdel med Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: b0d7c61ea7469b68c3cd6859745cd0892a565b95
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247477"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-cli"></a>Skapa en Standardbelastningsutjämnare med zonredundant klientdel med Azure CLI

Den här artikeln visar hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonredundant klientdel som använder en offentlig IP-Standard-adress.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kontrollerar du att du har installerat senast [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och är inloggad på ett Azure-konto med [az-inloggning](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och VM-storlekar som du kan prova tillgänglighetszoner med finns i [översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med hjälp av följande kommando:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Skapa en offentlig IP-Standard

Skapa en offentlig IP-Standard med följande kommando:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

Skapa en offentlig Load Balancer Standard med den offentliga IP som du skapade i föregående steg med följande kommando:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Skapa en LB-avsökning på port 80

Skapa en hälsoavsökning för belastningsutjämnaren med följande kommando:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Skapa en LB-regel för port 80

Skapa en regel för belastningsutjämnaren med följande kommando:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).



