---
title: Skapa, ändra eller ta bort ett VNet TAP - Azure CLI
description: Lär dig hur du skapar, ändrar eller tar bort ett virtuellt nätverk TAP med Hjälp av Azure CLI.
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
ms.openlocfilehash: 56288a65dc9e5b12a12393965b9670e394146181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234950"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Arbeta med ett virtuellt nätverk TAP med Hjälp av Azure CLI

Med Azure-virtuellt nätverk TAP (Terminal Access Point) kan du kontinuerligt strömma din virtuella datornätverkstrafik till ett nätverkspaketinsamlare eller analysverktyg. Insamlaren eller analysverktyget tillhandahålls av en [virtuell nätverksinstallationspartner.](https://azure.microsoft.com/solutions/network-appliances/) En lista över partnerlösningar som har validerats för att fungera med det virtuella nätverket TAP finns i [partnerlösningar](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Skapa en virtuell TAP-resurs för nätverk

Läs [förutsättningarna](virtual-network-tap-overview.md#prerequisites) innan du skapar en virtuell TAP-resurs för nätverket. Du kan köra de kommandon som följer i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra AZURE command-line interface (CLI) från datorn. Azure Cloud Shell är ett kostnadsfritt interaktivt skal som inte kräver att du installerar Azure CLI på din dator. Du måste logga in på Azure med ett konto som har rätt [behörighet](virtual-network-tap-overview.md#permissions). Den här artikeln kräver Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). Virtuellt nätverk TAP är för närvarande tillgängligt som ett tillägg. För att installera tillägget `az extension add -n virtual-network-tap`måste du köra . Om du kör Azure CLI lokalt måste du `az login` också köra för att skapa en anslutning med Azure.

1. Hämta ID:t för din prenumeration till en variabel som används i ett senare steg:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Ange det prenumerations-ID som du ska använda för att skapa en virtuell TAP-resurs för nätverket.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Registrera prenumerations-ID:t som du ska använda för att skapa en virtuell TAP-resurs för nätverket. Om du får ett registreringsfel när du skapar en TAP-resurs kör du följande kommando:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Om målet för det virtuella nätverket TAP är nätverksgränssnittet på den virtuella nätverksinstallationen för insamlare eller analysverktyg -

   - Hämta IP-konfigurationen för nätverksvirtos nätverksgränssnitt till en variabel som används i ett senare steg. ID är slutpunkten som aggregerar TAP-trafiken. I följande exempel hämtas ID:t för *IPconfig1* IP-konfigurationen för ett nätverksgränssnitt med namnet *myNetworkInterface*i en resursgrupp med namnet *myResourceGroup:*

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Skapa det virtuella nätverket TAP i regionen Westcentralus azure med IP-konfigurationens ID som mål och en valfri portegenskap. Porten anger målporten på IP-konfiguration för nätverksgränssnitt där TAP-trafiken ska tas emot:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Om målet för det virtuella nätverket TAP är en azure intern belastningsutjämnare:
  
   - Hämta ip-konfigurationen för klientdelen för Azures interna belastningsutjämning till en variabel som används i ett senare steg. ID är slutpunkten som aggregerar TAP-trafiken. I följande exempel hämtas ID:t för *frontendipconfig1-klient-IP-konfigurationen* för en belastningsutjämnare med namnet *myInternalLoadBalancer*i en resursgrupp med namnet *myResourceGroup:*

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Skapa det virtuella nätverket TAP med hjälp av IP-konfigurationens frontend-konfiguration som mål och en valfri portegenskap. Porten anger målporten på IP-konfigurationen för klientdelen där TAP-trafiken ska tas emot:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Bekräfta skapandet av det virtuella nätverket TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Lägga till en TAP-konfiguration i ett nätverksgränssnitt

1. Hämta ID:t för en befintlig TAP-resurs för virtuellt nätverk. I följande exempel hämtas ett virtuellt nätverk TAP med namnet *myTap* i en resursgrupp med namnet *myResourceGroup:*

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Skapa en TAP-konfiguration i nätverksgränssnittet för den övervakade virtuella datorn. I följande exempel skapas en TAP-konfiguration för ett nätverksgränssnitt med namnet *myNetworkInterface:*

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Bekräfta skapandet av TAP-konfigurationen:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Ta bort TAP-konfigurationen i ett nätverksgränssnitt

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Lista TAPs för virtuella nätverk i en prenumeration

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Ta bort ett virtuellt nätverk TAP i en resursgrupp

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
