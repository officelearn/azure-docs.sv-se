---
title: Skapa, ändra eller ta bort en virtuell nätverks-TAP - Azure CLI | Microsoft Docs
description: Lär dig mer om att skapa, ändra eller ta bort en virtuell nätverks-TAP som använder Azure CLI.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 3d95a9ea555cceda82530eb5c487eeb993c1a678
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188568"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Arbeta med en virtuell nätverks-TAP som använder Azure CLI

Azure-nätverk TRYCK (Terminal åtkomstpunkt) kan du kontinuerligt stream dina VM-nätverkstrafik till en insamlare eller analytics verktyg för nätverkspaket. Verktyget insamlare eller analytics tillhandahålls av en [virtuell nätverksinstallation](https://azure.microsoft.com/solutions/network-appliances/) partner. En lista över partnerlösningar som godkänts för att fungera med virtuella nätverks-TAP finns i [partnerlösningar](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Skapa ett virtuellt nätverk trycker du på resursen

Läs [krav](virtual-network-tap-overview.md#prerequisites) innan du skapar ett virtuellt nätverk trycker du på resursen. Du kan köra kommandon i den [Azure Cloud Shell](https://shell.azure.com/bash), eller genom att köra Azures kommandoradsgränssnitt (CLI) från datorn. Azure Cloud Shell är ett interaktivt gränssnitt, som inte kräver att du installerar Azure CLI på datorn. Du måste logga in på Azure med ett konto som har rätt [behörigheter](virtual-network-tap-overview.md#permissions). Den här artikeln kräver Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Det finns för närvarande virtuella nätverks-TAP som ett tillägg. Att installera tillägget måste du köra `az extension add -n virtual-network-tap`. Om du kör Azure CLI lokalt måste du också behöva köra `az login` att skapa en anslutning till Azure.

1. Hämta ID för prenumerationen i en variabel som används i ett senare steg:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Ange prenumerations-id som du använder för att skapa ett virtuellt nätverk trycker du på resursen.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Omregistrera prenumerations-ID som du använder för att skapa ett virtuellt nätverk trycker du på resursen. Om du får felet registrering när du skapar en resurs för TRYCK, kör du följande kommando:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Om målet för det virtuella nätverket TRYCK är nätverksgränssnittet på den virtuella nätverksinstallationen för insamlare eller analysverktyg-

   - Hämta IP-konfigurationen för den virtuella nätverksinstallationens nätverksgränssnitt i en variabel som används i ett senare steg. ID: T är den slutpunkt som ska aggregera trycker du på trafiken. I följande exempel hämtar ID för den *ipconfig1* IP-konfiguration för ett nätverksgränssnitt med namnet *myNetworkInterface*, i en resursgrupp med namnet *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Skapa det virtuella nätverket-TAP i västra azure-region med ID: T för IP-konfiguration som mål och en valfri port-egenskap. Porten anger målporten på där trafiken TRYCK tas emot IP-konfiguration av nätverksgränssnitt:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Om målet för det virtuella nätverket TRYCK är en azure intern belastningsutjämnare:
  
   - Hämta frontend IP-konfigurationen av Azures interna belastningsutjämnare i en variabel som används i ett senare steg. ID: T är den slutpunkt som ska aggregera trycker du på trafiken. I följande exempel hämtar ID för den *frontendipconfig1* frontend IP-konfiguration för en belastningsutjämnare med namnet *myInternalLoadBalancer*, i en resursgrupp med namnet  *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - Skapa det virtuella nätverket TRYCK med ID: T för klientdelens IP-konfiguration som mål och en valfri port-egenskap. Porten anger målporten på klientdelen IP-konfiguration där trafiken TRYCK tas emot:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Bekräfta skapandet av det virtuella nätverket-TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Lägga till en TRYCK-konfiguration för ett nätverksgränssnitt

1. Hämta ID för ett befintligt virtuellt nätverk trycker du på resursen. I följande exempel hämtas ett virtuellt nätverk med namnet TRYCK *myTap* i en resursgrupp med namnet *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Skapa en TRYCK-konfiguration för gränssnitt på den övervakade virtuella datorn. I följande exempel skapas en TRYCK konfiguration för ett nätverksgränssnitt med namnet *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Bekräfta skapandet av TRYCK konfigurationen:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Ta bort konfigurationen av TRYCK på ett nätverksgränssnitt

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Virtuellt nätverk i listan trycker i en prenumeration

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Ta bort en virtuell nätverks-TAP i en resursgrupp

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
