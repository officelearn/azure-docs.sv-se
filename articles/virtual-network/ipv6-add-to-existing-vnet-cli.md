---
title: Lägga till IPv6 i ett IPv4-program i virtuella Azure-nätverk - Azure CLI
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar IPv6-adresser till ett befintligt program i Azure virtuellt nätverk med Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 5dc231febc2e9b605b9e7f603f5d036b8a2c62eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240764"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>Lägga till IPv6 i ett IPv4-program i virtuella Azure-nätverk – Azure CLI (förhandsversion)

Den här artikeln visar hur du lägger till IPv6-adresser i ett program som använder IPv4-offentlig IP-adress i ett virtuellt Azure-nätverk för en standardbelastningsutjämning med Azure CLI. Uppgraderingen på plats innehåller ett virtuellt nätverk och undernät, en standardbelastningsutjämning med IPv4 + IPV6-frontendkonfigurationer, virtuella datorer med nätverkskort som har en IPv4 + IPv6-konfiguration, nätverkssäkerhetsgrupp och offentliga IP-adresser.

> [!Important]
> IPv6-stöd för Azure Virtual Network är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i stället, måste du köra Azure CLI version 2.0.28 eller senare i den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="prerequisites"></a>Krav

### <a name="register-the-service"></a>Registrera tjänsten

Innan du distribuerar ett program med dubbla staplar i Azure måste du konfigurera prenumerationen för den här förhandsversionen med hjälp av följande Azure CLI:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Det tar upp till 30 minuter innan funktionsregistreringen är klar. Du kan kontrollera din registreringsstatus genom att köra följande Azure CLI-kommando:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

När registreringen är klar kör du följande kommando:

```azurecli
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Skapa en Standard Load Balancer
Den här artikeln förutsätter att du har distribuerat en standardbelastningsutjämning enligt beskrivningen i [Snabbstart: Skapa en standardbelastningsutjämnare - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>Skapa IPv6-adresser

Skapa offentlig IPv6-adress med [med az network public-ip create](/cli/azure/network/public-ip) för din Standard Load Balancer. I följande exempel skapas en offentlig IP-adress iPv6 med namnet *PublicIP_v6* i resursgruppen *myResourceGroupSLB:*

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurera frontend för IPv6-belastningsutjämnare

COnfigure belastningsutjämnaren med den nya IPv6 [IP-adressen med az network lb frontend-ip skapa](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) enligt följande:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurera IPv6-serverdpool för belastningsutjämnare

Skapa serverda poolen för nätverkskort med IPv6-adresser med [az network lb-adresspool skapa](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) enligt följande:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurera IPv6-belastningsutjämningsregler

Skapa IPv6 belastningsutjämna regler med [az nätverk lb regel skapa](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create).

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Lägga till IPv6-adressintervall

Lägg till IPv6-adressintervall i det virtuella nätverket och undernätet som är värd för belastningsutjämnaren enligt följande:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Lägga till IPv6-konfiguration i nätverkskort

Konfigurera nätverkskort för virtuella datorer med en IPv6-adress med [az-nätverks-nic ip-config skapa](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) enligt följande:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa virtuellt IPv6-nätverk med dubbla stackar i Azure-portalen
Du kan visa det virtuella nätverket IPv6 med dubbla stackar i Azure-portalen på följande sätt:
1. I portalens sökfält anger du *myVnet*.
2. När **myVnet** visas i sökresultaten markerar du det. Detta startar **översiktssidan för** det virtuella nätverket med dubbla stacker som heter *myVNet*. Det virtuella nätverket med dubbla staplar visar de tre nätverkskort med både IPv4- och IPv6-konfigurationer i det dubbla stackundernätet med namnet *mySubnet*.

  ![IPv6 virtuellt nätverk med dubbla staplar i Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Det virtuella nätverket IPv6 för Azure är tillgängligt i Azure-portalen i skrivskyddad för den här förhandsversionen.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du uppdaterat en befintlig standardbelastningsutjämning med en IP-konfiguration för IPv4-klientdel till en konfiguration med dubbla stackar (IPv4 och IPv6). Du har också lagt till IPv6-konfigurationer i nätverkskorten för de virtuella datorerna i serverdapoolen. Mer information om IPv6-stöd i virtuella Azure-nätverk finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)
