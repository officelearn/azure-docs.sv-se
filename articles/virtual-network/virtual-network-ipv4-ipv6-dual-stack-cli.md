---
title: Distribuera ett IPv6-dual stack-program i Azure-nätverk – CLI
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur distribuerar ett IPv6-dual stack-program i Azure-nätverk med Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131010"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Distribuera ett IPv6-dual stack-program i Azure-nätverk - CLI (förhandsversion)

Den här artikeln visar hur du distribuerar en dual stack (IPv4 + IPv6)-program i Azure som innehåller ett dual stack virtuellt nätverk med ett dual stack-undernät, en belastningsutjämnare med dubbla (IPv4 + IPv6) frontend konfigurationer, virtuella datorer med nätverkskort som har en dubbel IP-konfiguration dubbel reglerna för nätverkssäkerhetsgrupper och dubbla offentliga IP-adresser.

> [!Important]
> IPv6 dual stack för Azure Virtual Network är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda Azure CLI lokalt i stället den här snabbstarten måste du använda Azure CLI version 2.0.49 eller senare. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att använda IPv6 för Azure-nätverk funktionen, måste du konfigurera din prenumeration med hjälp av Azure PowerShell på följande sätt:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Det tar upp till 30 minuter för funktionsregistrering av att slutföra. Du kan kontrollera din registreringsstatus som genom att köra följande Azure CLI-kommando:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
När registreringen är klar kör du följande kommando:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa ditt virtuella nätverk i dual stack-, måste du skapa en resursgrupp med [az gruppen skapa](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myRGDualStack* i den *eastus* plats:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Skapa IPv4 och IPv6 offentliga IP-adresser för belastningsutjämnare
För att komma åt dina IPv4 och IPv6-slutpunkter på Internet, behöver du IPv4 och IPv6 offentliga IP-adresser för belastningsutjämnaren. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). I följande exempel skapas IPv4 och IPv6 offentlig IP-adress med namnet *dsPublicIP_v4* och *dsPublicIP_v6* i den *myRGDualStack* resursgrupp:

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

Om du vill fjärransluta till dina virtuella datorer på internet, behöver du IPv4 offentliga IP-adresser för de virtuella datorerna. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip).

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

I det här avsnittet konfigurerar dubbel frontend IP-adress (IPv4 och IPv6) och backend-adresspoolen för belastningsutjämnaren och sedan skapa en belastningsutjämnare.

### <a name="create-load-balancer"></a>Skapa en lastbalanserare

Skapa belastningsutjämnare med [az network lb skapa](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) med namnet **dsLB** som innehåller en klientdelspool med namnet **dsLbFrontEnd_v4**, en serverdelspool med namnet  **dsLbBackEndPool_v4** som är associerad med den offentliga IP-adressen för IPv4 **dsPublicIP_v4** som du skapade i föregående steg. 

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

### <a name="create-ipv6-frontend"></a>Skapa IPv6-klientdel

Skapa en IPV6 frontend-IP med [az network lb frontend-IP-skapa](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). I följande exempel skapas en frontend IP-konfiguration med namnet *dsLbFrontEnd_v6* och kopplar den *dsPublicIP_v6* adress:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurera IPv6-backend-adresspool

Skapa en IPv6-adress för backend-adresspooler med [az network lb adresspool skapa](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). I följande exempel skapas en backend adresspoolen med namnet *dsLbBackEndPool_v6* att inkludera virtuella datorer med IPv6-NIC-konfigurationer:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. 

Använd [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) för att skapa en regel för lastbalanseraren. I följande exempel skapar regler för belastningsutjämnaren med namnet *dsLBrule_v4* och *dsLBrule_v6* och trafiken utjämnas på *TCP* port *80* till de IPv4 och IPv6 frontend IP-konfigurationerna:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Skapa nätverksresurser
Innan du distribuerar några virtuella datorer, måste du skapa stödnätverksresurser - tillgänglighetsuppsättning, nätverkssäkerhetsgrupp, virtuellt nätverk och virtuella nätverkskort. 
### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Placera dina virtuella datorer i en tillgänglighetsuppsättning för att förbättra tillgängligheten för din app.

Skapa en tillgänglighetsuppsättning med [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). I följande exempel skapas en tillgänglighetsuppsättning med namnet *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp för regler som styr inkommande och utgående kommunikation i ditt virtuella nätverk.

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [skapa az network nsg](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Skapa en nätverkssäkerhetsgruppregel för inkommande och utgående anslutningar

Skapa en nätverkssäkerhetsgruppregel för att tillåta RDP-anslutningar via port 3389, internet-anslutning via port 80 och för utgående anslutningar med [az network nsg-regel skapar](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

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

Skapa ett virtuellt nätverk med kommandot [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). I följande exempel skapas ett virtuellt nätverk med namnet *dsVNET* med undernät *dsSubNET_v4* och *dsSubNET_v6*:

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

Skapa virtuella nätverkskort för varje virtuell dator med [az network nic skapa](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). I följande exempel skapas ett virtuellt nätverkskort för varje virtuell dator. Varje nätverkskort har två IP-konfigurationer (1 IPv4-konfiguration, 1 IPv6-konfiguration). Du skapar IPv6-konfigurationen med [az network nic ip-config skapa](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
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

Skapa de virtuella datorerna med [az vm skapa](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). I följande exempel skapas två virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns. 

Skapa virtuell dator *dsVM0* på följande sätt:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Skapa virtuell dator *dsVM1* på följande sätt:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa IPv6 dual stack virtuellt nätverk i Azure portal
Du kan visa det virtuella nätverket för IPv6-dual stack i Azure-portalen på följande sätt:
1. I portalens sökfältet anger *dsVnet*.
2. När **myVirtualNetwork** visas i sökresultatet väljer du det. Detta startar den **översikt** sidan av dual stack-nätverk med namnet *dsVnet*. Det virtuella nätverket dual stack visar två nätverkskort med både IPv4 och IPv6-konfigurationer finns i dual stack-undernät med namnet *dsSubnet*.

  ![IPv6 dual stack virtuellt nätverk i Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> IPv6 för Azure-nätverket är tillgängliga i Azure-portalen i skrivskyddat läge för den här förhandsversionen.


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en grundläggande belastningsutjämnare med en dubbel klientdelens IP-konfiguration (IPv4 och IPv6). Du skapade också en två virtuella datorer som ingår nätverkskort med dubbla IP-konfigurationer (IPV4 + IPv6) som har lagts till i backend-poolen för belastningsutjämnaren. Mer information om IPv6-stöd i Azure-nätverk finns [vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)