---
title: Filtrera nätverkstrafik - Azure CLI | Microsoft Docs
description: I den här artikeln får lära du att filtrera trafik till ett undernät med en nätverkssäkerhetsgrupp med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: dfda95d2340d7dd57ac7b4d7ed7b0665c8e9294e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrera nätverkstrafik med en nätverkssäkerhetsgrupp med hjälp av Azure CLI

Du kan filtrera trafik inkommande och utgående från ett undernät för virtuellt nätverk med en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehålla säkerhetsregler som filtrera nätverkstrafik av IP-adress, port och protokoll. Säkerhetsregler som tillämpas på resurser har distribuerats i ett undernät. I den här artikeln får du lära dig hur du:

* Skapa en nätverkssäkerhet regler för gruppen och säkerhet
* Skapa ett virtuellt nätverk och associera en säkerhetsgrupp för nätverk till ett undernät
* Distribuera virtuella datorer (VM) i ett undernät
* Testa trafikfilter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i den här artikeln kräver att du använder Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp innehåller säkerhetsregler. Säkerhetsregler Ange källa och mål. Källor och mål kan vara program säkerhetsgrupper.

### <a name="create-application-security-groups"></a>Skapa säkerhetsgrupper för programmet

Skapa först en resursgrupp för alla resurser som skapats i denna artikel med [az gruppen skapa](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp i den *eastus* plats: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa en säkerhetsgrupp för program med [az nätverket asg skapa](/cli/azure/network/asg#az_network_asg_create). En säkerhetsgrupp för programmet kan du till gruppen servrar med liknande portfiltrering krav. I följande exempel skapar två säkerhetsgrupper för programmet.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [az nätverket nsg skapa](/cli/azure/network/nsg#az_network_nsg_create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Skapa säkerhetsregler

Skapa en anslutningssäkerhetsregel med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). I följande exempel skapas en regel som tillåter trafik inkommande trafik från internet till den *myWebServers* programgruppen för säkerhet via portarna 80 och 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

I följande exempel skapas en regel som tillåter trafik inkommande trafik från Internet till den *myMgmtServers* programgruppen för säkerhet via port 22:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

I den här artikeln SSH (port 22) är exponerad mot internet för den *myAsgMgmtServers* VM. För produktionsmiljöer, i stället för att exponera port 22 till internet, rekommenderas att du ansluter till Azure-resurser som du vill hantera med hjälp av en [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [privata](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nätverksanslutning.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas en virtuell med namnet *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Lägg till ett undernät till ett virtuellt nätverk med [az undernät för virtuellt nätverk skapa](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). I följande exempel läggs ett undernät med namnet *mySubnet* till virtuella nätverk och associerar den *myNsg* nätverkssäkerhetsgruppen till den:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera trafik filtrering i ett senare steg. 

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator som fungerar som en webbserver. Den `--asgs myAsgWebServers` alternativet gör att Azure för att göra det nätverksgränssnitt som skapas för den virtuella datorn tillhör den *myAsgWebServers* säkerhetsgrupp för programmet.

Den `--nsg ""` alternativet anges för att förhindra att Azure skapar en standard nätverkssäkerhetsgruppen för nätverksgränssnittet Azure skapar när den virtuella datorn skapas. Ett lösenord används för att förenkla den här artikeln. Nycklar används vanligtvis för produktionsdistributioner. Om du använder nycklar måste du också konfigurera SSH-agentvidarebefordran för stegen. Mer information finns i dokumentationen för SSH-klienten. Ersätt `<replace-with-your-password>` i följande kommando med ett lösenord som du väljer.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Det tar några minuter att skapa den virtuella datorn. Utdata som liknar följande exempel returneras när den virtuella datorn skapas: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anteckna den **publicIpAddress**. Den här adressen används för åtkomst till den virtuella datorn från internet i ett senare steg.  Skapa en virtuell dator som fungerar som en hanteringsserver:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats, notera den **publicIpAddress** i returnerade utdata. Den här adressen används för åtkomst till den virtuella datorn i nästa steg. Inte fortsätta med nästa steg förrän Azure har skapat den virtuella datorn.

## <a name="test-traffic-filters"></a>Testa trafikfilter

Använd kommandot nedan om du vill skapa en SSH-session med den *myVmMgmt* VM. Ersätt *<publicIpAddress>* med offentliga IP-adressen för den virtuella datorn. I exemplet ovan, IP-adressen är *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

När du uppmanas att ange ett lösenord, ange lösenordet du angav i [skapa virtuella datorer](#create-virtual-machines).

Anslutningen lyckas, eftersom port 22 tillåts inkommande från Internet till den *myAsgMgmtServers* programmet säkerhetsgrupp som nätverksgränssnittet som är kopplade till den *myVmMgmt* virtuella datorn.

Använd följande kommando för att SSH till den *myVmWeb* virtuell dator från den *myVmMgmt* VM:

```bash 
ssh azureuser@myVmWeb
```

Anslutningen eftersom en standardsäkerhetsregel inom varje nätverkssäkerhetsgruppen tillåter trafik över alla portar mellan alla IP-adresser inom ett virtuellt nätverk. Det går inte att SSH till den *myVmWeb* VM från Internet eftersom säkerheten regel för den *myAsgWebServers* tillåter inte att port 22 inkommande från Internet.

Använd följande kommandon för att installera den nginx-servern på den *myVmWeb* VM:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Den *myVmWeb* VM tillåts utgående till Internet för att hämta nginx eftersom en standardsäkerhetsregel tillåter all utgående trafik till Internet. Avsluta den *myVmWeb* SSH-session, vilket lämnar du på den `username@myVmMgmt:~$` fråga i den *myVmMgmt* VM. Att hämta nginx välkomstskärmen från den *myVmWeb* VM, anger du följande kommando:

```bash
curl myVmWeb
```

Logga ut från den *myVmMgmt* VM. Bekräfta att du har åtkomst till den *myVmWeb* webbserver från utanför Azure, ange `curl <publicIpAddress>` från din dator. Anslutningen lyckas, eftersom port 80 tillåts inkommande från Internet till den *myAsgWebServers* programmet säkerhetsgrupp som nätverksgränssnittet som är kopplade till den *myVmWeb* virtuella datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [ta bort grupp az](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln, skapas en nätverkssäkerhetsgrupp och som är kopplad till ett undernät för virtuellt nätverk. Läs mer om nätverkssäkerhetsgrupper i [nätverk Säkerhetsöversikt grupp](security-overview.md) och [hantera en nätverkssäkerhetsgrupp](virtual-network-manage-nsg-arm-ps.md).

Azure vägar trafik mellan undernät som standard. Du kan i stället välja att vidarebefordra trafik mellan undernät via en virtuell dator som fungerar som en brandvägg, till exempel. Mer information finns i avsnittet [skapa en routingtabell](tutorial-create-route-table-cli.md).