---
title: Skapa en Azure Private Link-tjänst med Azure CLI
description: Lär dig hur du skapar en Azure Private Link-tjänst med Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfffafaab2e2d4ef6b165ef03beb827342c94608
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96018060"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Skapa en privat länk-tjänst med Azure CLI
Den här artikeln visar hur du skapar en privat länk-tjänst i Azure med hjälp av Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver den senaste versionen av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-private-link-service"></a>Skapa en Private Link-tjänst
### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som ska vara värd för det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resurs grupp med namnet *myResourceGroup* på *westcentralus* -platsen:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). I det här exemplet skapas ett virtuellt standard nätverk med namnet *myVirtualNetwork* med ett undernät med namnet *undernät*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Skapa ett undernät
Skapa ett undernät för det virtuella nätverket med [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). I det här exemplet skapas ett undernät med namnet *mitt undernät* i det virtuella *myVirtualNetwork* -nätverket:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Skapa en intern Load Balancer 
Skapa en intern belastningsutjämnare med [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). I det här exemplet skapas en intern belastningsutjämnare med namnet *myILB* i resurs gruppen med namnet *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>skapa en hälsoavsökning för lastbalanseraren

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan ta emot nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från lastbalanseraren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel definierar klientdelens IP-konfiguration för inkommande trafik och serverdelens IP-pool för att ta emot trafiken, tillsammans med nödvändiga käll- och målportar. Skapa lastbalanseringsregeln *myHTTPRule* med [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest) så att du kan lyssna på port 80 i klientdelspoolen *myFrontEnd* och skicka lastbalanserad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

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

I det här exemplet ska vi inte skapa en virtuell dator. Du kan följa stegen i [snabb start: skapa en intern belastningsutjämnare för att belastningsutjämna virtuella datorer med Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) för att skapa två virtuella datorer som ska användas som backend-servrar för belastningsutjämnaren. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Inaktivera nätverks principer för privata länkar i undernät 
Private Link Service kräver en IP-adress från valfritt undernät i ett virtuellt nätverk. För närvarande stöder vi inte nätverks principer på de här IP-adresserna.  Därför måste vi inaktivera nätverks principerna på under nätet. Uppdatera under nätet för att inaktivera nätverks principer för privata länk tjänster med [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service-using-standard-load-balancer"></a>Skapa en privat länk-tjänst med hjälp av Standard Load Balancer 
 
Skapa en privat länk-tjänst med Standard Load Balancer klient delens IP-konfiguration med [AZ Network Private-Link-service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create). I det här exemplet skapas en privat länk tjänst med namnet *myPLS* med hjälp av standard Load Balancer med namnet *myLoadBalancer* i resurs gruppen med namnet *myResourceGroup*. 
 
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
När du har skapat det tar du del av det privata länk tjänst-ID: t. Du kommer att behöva det senare för att begära anslutning till den här tjänsten.  
 
I det här skedet har din privata länk tjänst skapats och är redo att ta emot trafiken. Observera att exemplet ovan bara visar hur du skapar en privat länk tjänst med Azure CLI.  Vi har inte konfigurerat backend-poolerna för belastningsutjämnare eller något program på backend-poolerna för att lyssna på trafiken. Om du vill se trafik flöden från slut punkt till slut punkt rekommenderar vi starkt att du konfigurerar ditt program bakom Standard Load Balancer.  
 
Härnäst visar vi hur du mappar den här tjänsten till en privat slut punkt i ett annat virtuellt nätverk med hjälp av Azure CLI. Återigen är exemplet begränsat till att skapa den privata slut punkten och ansluta till den privata länk tjänsten som skapats ovan med hjälp av Azure CLI. Dessutom kan du skapa virtuella datorer i det virtuella nätverket för att skicka/ta emot trafik till den privata slut punkten.        
 
## <a name="private-endpoints"></a>Privata slut punkter

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket 
Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). I det här exemplet skapas ett virtuellt nätverk med namnet  *myPEVNet*   i resurs gruppen med namnet *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Skapa under nätet 
Skapa ett undernät i ett virtuellt nätverk med [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). I det här exemplet skapas ett undernät med namnet  *mitt undernät*   i det virtuella nätverket med namnet *myPEVnet* i resurs gruppen med namnet *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Inaktivera nätverks principer för privata slut punkter på undernät 
Privat slut punkt kan skapas i valfritt undernät som du väljer i ett virtuellt nätverk. För närvarande stöder vi inte nätverks principer på privata slut punkter.  Därför måste vi inaktivera nätverks principerna på under nätet. Uppdatera under nätet för att inaktivera principer för privata nätverks slut punkter med [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Skapa privat slut punkt och Anslut till privat länk tjänst 
Skapa en privat slut punkt för att konsumera privat länk tjänst som skapats ovan i det virtuella nätverket:
  
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
Du kan hämta *privat anslutnings-resurs-ID* med en `az network private-link-service show` privat länk-tjänst. ID: t ser ut så här:   
/subscriptions/subID/resourceGroups/*ResourceGroupName*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Visa anslutningar för privata länk tjänster 
 
Se anslutnings begär Anden på din privata länk tjänst med [AZ Network Private-Link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Private Link service](private-link-service-overview.md)
