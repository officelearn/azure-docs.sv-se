---
title: Filtrera nätverkstrafik - Azure CLI | Microsoft-dokument
description: I den här artikeln får du lära dig hur du filtrerar nätverkstrafik till ett undernät, med en nätverkssäkerhetsgrupp, med hjälp av Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 72c8b4d57b5064af34665cff1386179e62324938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235083"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrera nätverkstrafik med en nätverkssäkerhetsgrupp med Hjälp av Azure CLI

Du kan filtrera inkommande och utgående nätverkstrafik till och från ett undernät i ett virtuellt nätverk med en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehåller säkerhetsregler som filtrerar nätverkstrafik efter IP-adress, port och protokoll. Säkerhetsregler tillämpas på resurser som har distribuerats i ett undernät. I den här artikeln kan du se hur du:

* Skapa en nätverkssäkerhetsgrupp och säkerhetsregler
* Skapa ett virtuellt nätverk och associera en nätverkssäkerhetsgrupp med ett undernät
* Distribuera virtuella datorer (VM) i ett undernät
* Testa trafikfilter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp innehåller säkerhetsregler. Säkerhetsregler anger en källa och ett mål. Källor och mål kan vara programsäkerhetsgrupper.

### <a name="create-application-security-groups"></a>Skapa programsäkerhetsgrupper

Skapa först en resursgrupp för alla resurser som skapas i den här artikeln med [az-grupp skapa](/cli/azure/group). I följande exempel skapas en resursgrupp på platsen *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Skapa en programsäkerhetsgrupp med [az-nätverksasg create](/cli/azure/network/asg). En programsäkerhetsgrupp gör att du kan gruppera servrar med liknande portfiltreringskrav. I följande exempel skapas två programsäkerhetsgrupper.

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

Skapa en nätverkssäkerhetsgrupp med [az network nsg create](/cli/azure/network/nsg). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Skapa säkerhetsregler

Skapa en säkerhetsregel med [az-nätverks nsg-regel skapa](/cli/azure/network/nsg/rule). I följande exempel skapas en regel som tillåter inkommande trafik från Internet till programsäkerhetsgruppen *myWebServers* via port 80 och 443:

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

I följande exempel skapas en regel som tillåter trafik som är inkommande från Internet till *myMgmtServers-programskyddsgruppen* över port 22:

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

I den här artikeln är SSH (port 22) utsatt för Internet för *myAsgMgmtServers* VM. För produktionsmiljöer, i stället för att exponera port 22 till internet, rekommenderar vi att du ansluter till Azure-resurser som du vill hantera med hjälp av en [VPN-](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [privat](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nätverksanslutning.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Lägg till ett undernät i ett virtuellt nätverk med [az-nätverksnätsundernät skapa](/cli/azure/network/vnet/subnet). I följande exempel läggs ett undernät med namnet *mySubnet* till i det virtuella nätverket och nätverkssäkerhetsgruppen *myNsg* associeras med det:

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

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator som fungerar som en webbserver. Alternativet `--asgs myAsgWebServers` gör att Azure gör det nätverksgränssnitt som skapas för den virtuella datorn till medlem i *myAsgWebServers-programsäkerhetsgruppen.*

Alternativet `--nsg ""` anges för att förhindra att Azure skapar en standardnätverkssäkerhetsgrupp för nätverksgränssnittet som Azure skapar när den skapar den virtuella datorn. För att effektivisera den här artikeln används ett lösenord. Nycklar används vanligtvis i produktionsdistributioner. Om du använder nycklar måste du också konfigurera SSH-agentens vidarebefordran för de återstående stegen. Mer information finns i dokumentationen för din SSH-klient. Ersätt `<replace-with-your-password>` i följande kommando med ett lösenord som du väljer.

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

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats returneras utdata som liknar följande exempel: 

```output
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

Anteckna **publicIpAddress**. Den här adressen används för att komma åt den virtuella datorn från Internet i ett senare steg.  Skapa en virtuell dator som fungerar som en hanteringsserver:

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

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats noterar du **publicIpAddress** i den returnerade utdata. Den här adressen används för att komma åt den virtuella datorn i nästa steg. Fortsätt inte med nästa steg förrän Azure har skapat den virtuella datorn.

## <a name="test-traffic-filters"></a>Testa trafikfilter

Använd kommandot som följer för att skapa en SSH-session med *den virtuella datorn myVmMgmt.* Ersätt * \<publicIpAddress>* med den offentliga IP-adressen för den virtuella datorn. I exemplet ovan är IP-adressen *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

När du uppmanas att ange ett lösenord anger du lösenordet som du angav i [Skapa virtuella datorer](#create-virtual-machines).

Anslutningen lyckas, eftersom port 22 tillåts inkommande från Internet till *myAsgMgmtServers-programsäkerhetsgruppen* som nätverksgränssnittet som är anslutet till *den virtuella datorn myVmMgmt* finns i.

Använd följande kommando till SSH till *virtuella virtuella datorer myVmWeb* från den *virtuella datorn myVmMgmt:*

```bash 
ssh azureuser@myVmWeb
```

Anslutningen lyckas eftersom en standardsäkerhetsregel i varje nätverkssäkerhetsgrupp tillåter trafik via alla portar mellan alla IP-adresser i ett virtuellt nätverk. Du kan inte SSH till *myVmWeb* VM från Internet eftersom säkerhetsregeln för *myAsgWebServers* inte tillåter port 22 inkommande från Internet.

Använd följande kommandon för att installera nginx-webbservern på *den virtuella datorn för myVmWeb:*

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Den *virtuella datorn myVmWeb* tillåts utgående till Internet för att hämta nginx eftersom en standardsäkerhetsregel tillåter all utgående trafik till Internet. Avsluta *myVmWeb* SSH-sessionen, som `username@myVmMgmt:~$` lämnar dig på uppmaning av *myVmMgmt* VM. Om du vill hämta välkomstskärmen för nginx från *den virtuella datorn myVmWeb* anger du följande kommando:

```bash
curl myVmWeb
```

Utloggning av *myVmMgmt* VM. Ange från din egen dator för att bekräfta att du `curl <publicIpAddress>` kan komma åt *myVmWeb-webbservern* utanför Azure. Anslutningen lyckas, eftersom port 80 tillåts inkommande från Internet till *myAsgWebServers-programsäkerhetsgruppen* som nätverksgränssnittet som är anslutet till *myVmWeb-virtuella* datorn finns i.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs använder du [az-gruppborttagning](/cli/azure/group) för att ta bort resursgruppen och alla resurser som den innehåller.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en nätverkssäkerhetsgrupp och associerade den till ett virtuellt nätverksundernät. Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper](security-overview.md) och [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md).

Azure dirigerar som standard trafik mellan undernät. Du kan i stället välja att exempelvis dirigera trafik mellan undernät via en virtuell dator, som fungerar som en brandvägg. Mer information finns i [Skapa en rutttabell](tutorial-create-route-table-cli.md).
