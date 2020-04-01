---
title: Distribuera IPv6-program med dubbla staplar – grundläggande belastningsutjämning - CLI
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur distribuera ett IPv6 dual stack-program i Azure virtuellt nätverk med Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 396c37d4c8de6a890102e435c5ec6cc70b598638
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421025"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Distribuera ett IPv6-program med dubbla staplar med basic load balancer - CLI

Den här artikeln visar hur du distribuerar ett IPv4 + IPv6-program med grundläggande belastningsutjämnare med Azure CLI som innehåller ett virtuellt nätverk med dubbla staplar med ett undernät med dubbla staplar, en grundläggande belastningsutjämnare med dubbla (IPv4 + IPv6) frontendkonfigurationer, virtuella datorer med nätverkskort som har en dubbel IP-konfiguration, regler för säkerhetsgrupp med dubbla nätverk och dubbla offentliga IP-adresser.

Information om hur du distribuerar ett IPV4 + IPv6-program med standardbelastningsutjämning finns i [Distribuera ett IPv6-program med dubbla staplar med Standard belastningsutjämnare med Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).


Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du bestämmer dig för att installera och använda Azure CLI lokalt i stället kräver den här snabbstarten att du använder Azure CLI version 2.0.49 eller senare. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa det virtuella nätverket med dubbla staplar måste du skapa en resursgrupp med [az-gruppge](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *DsResourceGroup01* på *platsen eastus:*

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Skapa offentliga IP-adresser för IPv4 och IPv6 för belastningsutjämnare
För att komma åt dina IPv4- och IPv6-slutpunkter på Internet behöver du offentliga IP-adresser för IPv4 och IPv6 för belastningsutjämnaren. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). I följande exempel skapas IPv4- och IPv6-ip-adress med namnet *dsPublicIP_v4* och *dsPublicIP_v6* i resursgruppen *DsResourceGroup01:*

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Skapa offentliga IP-adresser för virtuella datorer

För att fjärråtkomst till dina virtuella datorer på internet behöver du IPv4-offentliga IP-adresser för de virtuella datorerna. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet konfigurerar du ip-adressen med dubbla frontend (IPv4 och IPv6) och backend-adresspoolen för belastningsutjämnaren och skapar sedan en grundläggande belastningsutjämnare.

### <a name="create-load-balancer"></a>Skapa en lastbalanserare

Skapa den grundläggande belastningsutjämnaren med [az-nätverks lb skapa](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) **namngivna dsLB** som innehåller en frontend pool med namnet **dsLbFrontEnd_v4**, en backend pool med namnet **dsLbBackEndPool_v4** som är associerad med IPv4 offentliga IP-adress **dsPublicIP_v4** som du skapade i föregående steg. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Skapa IPv6-frontend

Skapa en IPV6 frontend IP med [az nätverk lb frontend-ip skapa](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). I följande exempel skapas en ip-konfiguration med klientdel med namnet *dsLbFrontEnd_v6* och *dsPublicIP_v6-adressen* bifogas:

```azurecli
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurera IPv6-backend-adresspool

Skapa en IPv6-backend-adresspooler med [az network lb-adresspool skapa](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). I följande exempel skapas backend-adresspool med namnet *dsLbBackEndPool_v6* för att inkludera virtuella datorer med IPv6-nätverkskortskonfigurationer:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning
Skapa en hälsoavsökning med [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. 

Använd [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) för att skapa en regel för lastbalanseraren. I följande exempel skapas belastningsutjämnare regler med namnet dsLBrule_v4 och dsLBrule_v6 och balanserar trafik på *TCP-port* 80 till IP-konfigurationerna för IPv4 och IPv6:The following example creates load balancer rules named *dsLBrule_v4* and *dsLBrule_v6* and balances traffic on TCP port *80* to the IPv4 and IPv6 frontend IP configurations:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Skapa nätverksresurser
Innan du distribuerar vissa virtuella datorer måste du skapa stöd för nätverksresurser – tillgänglighetsuppsättning, nätverkssäkerhetsgrupp, virtuellt nätverk och virtuella nätverkskort. 
### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Om du vill förbättra appens tillgänglighet placerar du dina virtuella datorer i en tillgänglighetsuppsättning.

Skapa en tillgänglighetsuppsättning med [az vm-tillgänglighetsuppsättning skapa](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). I följande exempel skapas en tillgänglighetsuppsättning med namnet *dsAVset:*

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp för de regler som styr inkommande och utgående kommunikation i ditt virtuella nätverk.

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Skapa en regel för nätverkssäkerhetsgrupp för inkommande och utgående anslutningar

Skapa en regel för nätverkssäkerhetsgrupp för att tillåta RDP-anslutningar via port 3389, internetanslutning via port 80 och för utgående anslutningar med [az-nätverks nsg-regel skapa](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). I följande exempel skapas ett virtuellt nätverk med namnet *dsVNET* med undernät *dsSubNET_v4* och *dsSubNET_v6:*

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Skapa nätverkskort

Skapa virtuella nätverkskort för varje virtuell dator med [az-nätverksnic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). I följande exempel skapas ett virtuellt nätverkskort för varje virtuell dator. Varje nätverkskort har två IP-konfigurationer (1 IPv4 config, 1 IPv6 config). Du skapar IPV6-konfigurationen med [az-nätverk nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).

```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). I följande exempel skapas två virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns. 

Skapa *dsVM0* för virtuella datorer enligt följande:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Skapa *dsVM1* för virtuella datorer enligt följande:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa virtuellt IPv6-nätverk med dubbla stackar i Azure-portalen
Du kan visa det virtuella nätverket IPv6 med dubbla stackar i Azure-portalen på följande sätt:
1. Ange *dsVnet*i portalens sökfält .
2. När **myVirtualNetwork** visas i sökresultatet väljer du det. Detta startar **översiktssidan för** det virtuella nätverket med dubbla staplar med namnet *dsVnet*. Det virtuella nätverket med dubbla staplar visar de två nätverkskort med både IPv4- och IPv6-konfigurationer i det dubbla stackundernätet med namnet *dsSubnet*.

  ![IPv6 virtuellt nätverk med dubbla staplar i Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [az-gruppborttagning](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en grundläggande belastningsutjämnare med en IP-konfiguration med dubbla frontend (IPv4 och IPv6). Du har också skapat två virtuella datorer som inkluderade nätverkskort med dubbla IP-konfigurationer (IPV4 + IPv6) som lades till i slutbelastningshanterarens serverdelspool. Mer information om IPv6-stöd i virtuella Azure-nätverk finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)
