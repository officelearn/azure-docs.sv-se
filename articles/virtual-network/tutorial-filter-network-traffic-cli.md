---
title: Filtrera nätverkstrafik – Azure CLI | Microsoft Docs
description: I den här artikeln får du lära dig hur du filtrerar nätverkstrafik till ett undernät med en nätverkssäkerhetsgrupp med hjälp av Azure CLI.
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
ms.openlocfilehash: 3252395c7a511a00e8da0a31139fce3b2763decb
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461849"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrera nätverkstrafik med en nätverkssäkerhetsgrupp med hjälp av Azure CLI

Du kan filtrera inkommande och utgående nätverkstrafik till och från ett undernät i ett virtuellt nätverk med en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehåller säkerhetsregler som filtrerar nätverkstrafik efter IP-adress, port och protokoll. Säkerhetsregler tillämpas på resurser som har distribuerats i ett undernät. I den här artikeln kan du se hur du:

* Skapa en nätverkssäkerhetsgrupp och säkerhetsregler
* Skapa ett virtuellt nätverk och associera en nätverkssäkerhetsgrupp med ett undernät
* Distribuera virtuella datorer (VM) i ett undernät
* Testa trafikfilter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp innehåller säkerhetsregler. Säkerhetsregler anger en källa och ett mål. Källor och mål kan vara programsäkerhetsgrupper.

### <a name="create-application-security-groups"></a>Skapa programsäkerhetsgrupper

Skapa först en resursgrupp för alla resurser som skapats i den här artikeln med [az gruppen skapa](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp på platsen *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa en programsäkerhetsgrupp med [az nätverket asg skapa](/cli/azure/network/asg#az_network_asg_create). En programsäkerhetsgrupp gör att du kan gruppera servrar med liknande portfiltreringskrav. I följande exempel skapas två programsäkerhetsgrupper.

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

Skapa en nätverkssäkerhetsgrupp med [az network nsg skapa](/cli/azure/network/nsg#az_network_nsg_create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Skapa säkerhetsregler

Skapa en säkerhetsregel med [az network nsg-regel skapar](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). I följande exempel skapas en regel som tillåter inkommande trafik från Internet till programsäkerhetsgruppen *myWebServers* via port 80 och 443:

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

I följande exempel skapas en regel som tillåter trafik inkommande från Internet till den *myMgmtServers* programsäkerhetsgruppen via port 22:

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

I den här artikeln SSH (port 22) exponeras mot internet för den *myAsgMgmtServers* VM. För produktionsmiljöer, i stället för att exponera port 22 till internet, rekommenderar vi att du ansluter till Azure-resurser som du vill hantera med hjälp av en [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [privata](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nätverksanslutning.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Lägg till ett undernät till ett virtuellt nätverk med [az network vnet-undernät skapa](/cli/azure/network/vnet/subnet). I följande exempel läggs ett undernät med namnet *mySubnet* till i det virtuella nätverket och nätverkssäkerhetsgruppen *myNsg* associeras med det:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera trafikfiltrering i ett senare steg. 

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator som fungerar som en webbserver. Den `--asgs myAsgWebServers` alternativet gör att Azure för att göra det nätverksgränssnitt som skapas för den virtuella datorn tillhör den *myAsgWebServers* programsäkerhetsgrupp.

Den `--nsg ""` alternativ anges för att förhindra att Azure skapar en standard för nätverkssäkerhetsgruppen för nätverksgränssnittet som Azure skapar när den virtuella datorn skapas. För att förenkla den här artikeln används ett lösenord. Nycklar används vanligtvis för produktionsdistributioner. Om du använder nycklar måste du också konfigurera SSH-agentvidarebefordran för de återstående stegen. Mer information finns i dokumentationen för SSH-klienten. Ersätt `<replace-with-your-password>` i följande kommando med ett lösenord som du väljer.

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

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats kan returneras utdata som liknar följande exempel: 

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

Anteckna **publicIpAddress**. Den här adressen används för att få åtkomst till den virtuella datorn från internet i ett senare steg.  Skapa en virtuell dator som fungerar som en hanteringsserver:

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

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats, notera den **publicIpAddress** i returnerade utdata. Den här adressen används för att få åtkomst till den virtuella datorn i nästa steg. Fortsätt inte med nästa steg förrän Azure har skapat den virtuella datorn.

## <a name="test-traffic-filters"></a>Testa trafikfilter

Använd kommandot nedan för att skapa en SSH-session med den *myVmMgmt* VM. Ersätt *<publicIpAddress>* med den offentliga IP-adressen för den virtuella datorn. I exemplet ovan, IP-adressen är *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

När du tillfrågas om ett lösenord, anger du lösenordet som du angav i [skapa virtuella datorer](#create-virtual-machines).

Anslutningen lyckas, eftersom port 22 tillåts inkommande från Internet till den *myAsgMgmtServers* programsäkerhetsgrupp som nätverksgränssnittet som är kopplat till den *myVmMgmt* virtuella datorn är i.

Använd följande kommando för att SSH till den *myVmWeb* virtuell dator från den *myVmMgmt* VM:

```bash 
ssh azureuser@myVmWeb
```

Anslutningen lyckas eftersom en standardsäkerhetsregel i varje nätverkssäkerhetsgrupp tillåter trafik via alla portar mellan alla IP-adresser i ett virtuellt nätverk. Det går inte att SSH till den *myVmWeb* virtuell dator från Internet eftersom säkerhetsregeln för den *myAsgWebServers* inte tillåter port 22 inkommande från Internet.

Använd följande kommandon för att installera nginx-webbserver på den *myVmWeb* VM:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Den *myVmWeb* VM är tillåten utgående till Internet för att hämta nginx eftersom en standardsäkerhetsregel som tillåter all utgående trafik till Internet. Avsluta den *myVmWeb* SSH-session, vilket lämnar du på den `username@myVmMgmt:~$` fråga för den *myVmMgmt* VM. Att hämta nginx välkomstskärmen från den *myVmWeb* virtuell dator, anger du följande kommando:

```bash
curl myVmWeb
```

Logga ut från den *myVmMgmt* VM. Bekräfta att du kan komma åt den *myVmWeb* webbserver från utanför Azure, ange `curl <publicIpAddress>` från din egen dator. Anslutningen lyckas, eftersom port 80 tillåts inkommande från Internet till den *myAsgWebServers* programsäkerhetsgrupp som nätverksgränssnittet som är kopplat till den *myVmWeb* virtuella datorn är i.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda [az group delete](/cli/azure/group#az_group_delete) att ta bort resursgruppen och alla resurser den innehåller.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du skapade en nätverkssäkerhetsgrupp och associerat den till ett virtuellt nätverksundernät. Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper](security-overview.md) och [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md).

Azure dirigerar som standard trafik mellan undernät. Du kan i stället välja att exempelvis dirigera trafik mellan undernät via en virtuell dator, som fungerar som en brandvägg. Läs hur genom att läsa [skapar en routningstabell](tutorial-create-route-table-cli.md).
