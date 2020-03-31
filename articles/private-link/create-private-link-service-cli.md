---
title: Skapa en Azure Private Link-tjänst med Azure CLI
description: Lär dig hur du skapar en Azure Private Link-tjänst med Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350245"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Skapa en privat länktjänst med Azure CLI
Den här artikeln visar hur du skapar en private link-tjänst i Azure med Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du bestämmer dig för att installera och använda Azure CLI lokalt i stället kräver den här snabbstarten att du använder den senaste Azure CLI-versionen. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.
## <a name="create-a-private-link-service"></a>Skapa en Private Link-tjänst
### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som ska vara värd för det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resursgrupp med namnet *myResourceGroup* på *platsen för westcentralus:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). I det här exemplet skapas ett virtuellt standardnätverk med namnet *myVirtualNetwork* med ett undernät med namnet *mySubnet:*

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Skapa ett undernät
Skapa ett undernät för det virtuella nätverket med [az-nätverk vnet undernät skapa](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). I det här exemplet skapas ett undernät med namnet *mySubnet* i det virtuella *nätverket myVirtualNetwork:*

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Skapa en intern belastningsutjämnare 
Skapa en intern belastningsutjämnare med [az-nätverk lb create](/cli/azure/network/lb#az-network-lb-create). I det här exemplet skapas en intern belastningsutjämnare med namnet *myILB* i resursgruppen *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>skapa en hälsoavsökning för lastbalanseraren

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan ta emot nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från lastbalanseraren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökning med [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel definierar klientdelens IP-konfiguration för inkommande trafik och serverdelens IP-pool för att ta emot trafiken, tillsammans med nödvändiga käll- och målportar. Skapa lastbalanseringsregeln *myHTTPRule* med [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) så att du kan lyssna på port 80 i klientdelspoolen *myFrontEnd* och skicka lastbalanserad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet täcker vi inte skapandet av virtuella datorer. Du kan följa stegen i [Skapa en intern belastningsutjämnare för att ladda balansera virtuella datorer med Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) för att skapa två virtuella datorer som ska användas som serverningsservrar för belastningsutjämnaren. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Inaktivera principer för tjänsten Privat länk i undernätet 
Private Link-tjänsten kräver en IP från valfri undernät i ett virtuellt nätverk. För närvarande stöder vi inte nätverksprinciper för dessa IPs.  Därför måste vi inaktivera nätverksprinciperna i undernätet. Uppdatera undernätet för att inaktivera principer för private link-tjänstnätverk med [uppdatering av az-nätverksnätsnät](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Skapa en Private Link-tjänst  
 
Skapa en privat länktjänst med standardbelastningsutjämnings-IP-konfiguration med [az-nätverk för privatlänktjänst.](/cli/azure/network/private-link-service#az-network-private-link-service-create) I det här exemplet skapas en private link-tjänst med namnet *myPLS* med standardbelastningsutjämning med namnet *myLoadBalancer* i resursgruppen *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
När du har skapat det privata länktjänst-ID:t. Du behöver det senare för att begära anslutning till den här tjänsten.  
 
I det här skedet har tjänsten Private Link skapats och är redo att ta emot trafiken. Observera att ovanstående exempel är bara att demonstrera skapa Private Link-tjänst med Azure CLI.  Vi har inte konfigurerat serverdpoolerna för belastningsutjämnarhanteraren eller något program på serverdapoolerna för att lyssna på trafiken. Om du vill se trafikflöden från slutna till slutna händer rekommenderar du starkt att konfigurera programmet bakom standardbelastningsutjämningen.  
 
Därefter visar vi hur du mappar den här tjänsten till en privat slutpunkt i olika virtuella nätverk med Azure CLI. Återigen är exemplet begränsat till att skapa den privata slutpunkten och ansluta till private link-tjänsten som skapats ovan med Azure CLI. Dessutom kan du skapa virtuella datorer i det virtuella nätverket för att skicka/ta emot trafik till den privata slutpunkten.        
 
## <a name="private-endpoints"></a>Privata slutpunkter

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket 
Skapa ett virtuellt nätverk med [az nätverk vnet skapa](/cli/azure/network/vnet#az-network-vnet-create). I det här exemplet skapas ett virtuellt nätverk med namnet *myPEVNet* i resursgruppen *myResourcegroup:* 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Skapa undernätet 
Skapa ett undernät i virtuellt nätverk med [az nätverk vnet undernät skapa](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). I det här exemplet skapas ett undernät med namnet *mySubnet* i det virtuella nätverket *myPEVnet* i resursgruppen *myResourcegroup:* 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Inaktivera principer för privata slutpunktsnätverk i undernätet 
Privat slutpunkt kan skapas i valfritt undernät i ett virtuellt nätverk. För närvarande stöder vi inte nätverksprinciper på privata slutpunkter.  Därför måste vi inaktivera nätverksprinciperna i undernätet. Uppdatera undernätet för att inaktivera principer för privata slutpunktsnätverk med [uppdatering av az-nätverksnätsnät](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Skapa privat slutpunkt och ansluta till privat länktjänst 
Skapa en privat slutpunkt för att använda tjänsten Private Link som skapats ovan i det virtuella nätverket:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Du kan hämta *private-connection-resource-id* med `az network private-link-service show` på Private Link-tjänsten. ID:et kommer att se ut:   
/subscriptions/subID/resourceGroups/resourcegroupname /providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** *resourcegroupname* 
 
## <a name="show-private-link-service-connections"></a>Visa tjänstanslutningar för private link 
 
Visa anslutningsförfrågningar på din Private [Link-tjänst med az-nätverket private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Private Link-tjänsten](private-link-service-overview.md)
 
