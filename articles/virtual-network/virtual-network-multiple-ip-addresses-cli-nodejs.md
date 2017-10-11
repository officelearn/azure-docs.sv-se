---
title: "Virtuell dator med flera IP-adresser med hjälp av Azure CLI 1.0 | Microsoft Docs"
description: "Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med Azure CLI 1.0 | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Tilldela flera IP-adresser till virtuella datorer med Azure CLI 1.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Den här artikeln förklaras hur du skapar en virtuell dator (VM) via Azure Resource Manager-distributionsmodellen med hjälp av Azure CLI 1.0. Flera IP-adresser kan inte tilldelas till resurser som skapats via den klassiska distributionsmodellen. Om du vill veta mer om Azure distributionsmodeller kan läsa den [förstår distributionsmodellerna](../resource-manager-deployment-model.md) artikel.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Du kan göra detta med hjälp av Azure CLI 1.0 (den här artikeln) eller [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md). De steg som följer beskrivs hur du skapar ett exempel VM med flera IP-adresser, enligt beskrivningen i scenariot. Ändra variabeln namn och IP-adresstyper som krävs för din implementering.

1. Installera och konfigurera Azure CLI 1.0 genom att följa stegen i den [installera och konfigurera Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikeln och logga in på ditt Azure-konto med den `azure-login` kommando.

2. Skapa en resursgrupp:
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. Skapa ett virtuellt nätverk:

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. Skapa ett undernät i det virtuella nätverket:

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. Skapa ett lagringskonto för den virtuella datorn. Innan du kör följande kommando ersätter *mittlagringskonto* med ett unikt namn. Namnet måste vara unikt i Azure:

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. Skapa IP-konfigurationer, ett nätverkskort och tilldela IP-konfigurationer till nätverkskortet. Du kan lägga till, ta bort eller ändra konfigurationer efter behov. Följande konfigurationer beskrivs i scenariot:

    **IPConfig-1**

    Ange de kommandon som följer för att skapa:

    - En offentlig IP-adressresurs med en statisk offentlig IP-adress
    - Ett nätverkskort tilldelas den offentliga IP-adress och en statisk privat IP-adress.
    
    Ersätt *mypublicdns* med ett namn som är unikt i Azure-plats.

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > Offentliga IP-adresser har en låg kostnad. Mer information om priser för IP-adress i [IP-adress priser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).

    **IPConfig-2**

     Ange följande kommandon för att skapa en ny offentlig IP-adressresurs och en ny IP-konfiguration med en statisk offentlig IP-adress och en statisk privat IP-adress:
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **IPConfig-3**

    Ange följande kommandon för att skapa en IP-konfiguration med en statisk privat IP-adress och ingen offentlig IP-adress:

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >Även om den här artikeln tilldelar alla IP-konfigurationer till ett enda nätverkskort, kan du också tilldela flera IP-konfigurationer till alla NIC på en virtuell dator. Läsa skapa en virtuell dator med flera nätverkskort artikeln om du vill veta hur du skapar en virtuell dator med flera nätverkskort.

7. Skapa en virtuell Linux-dator 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    Om du vill ändra Standard DS2 v2 VM-storlek, till exempel bara lägga till följande egenskap `--vm-size Standard_DS3_v2` till den `azure vm create` kommandot i steg 6.

8. Ange följande kommando för att visa nätverkskortet och de associera IP-konfigurationerna:

    ```azurecli
    azure network nic show --resource-group $RgName --name myNic1
    ```
9. Lägga till privata IP-adresser i Virtuella operativsystem genom att slutföra stegen för operativsystemet i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln.

## <a name="add"></a>Lägg till IP-adresser till en virtuell dator

Du kan lägga till ytterligare privata och offentliga IP-adresser till en befintlig NIC genom att slutföra de steg som följer. Exemplen bygger på den [scenariot](#Scenario) beskrivs i den här artikeln.

1. Öppna Azure CLI och utföra stegen i det här avsnittet i en enda CLI-session. Om du inte redan har installerat och konfigurerat Azure-CLI kan du slutföra stegen i den [installera och konfigurera Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel och logga in på ditt Azure-konto.

2. Utför stegen i följande avsnitt, baserat på dina krav:

    - **Lägg till en privat IP-adress**
    
        Om du vill lägga till en privat IP-adress till ett nätverkskort måste du skapa en IP-konfiguration med hjälp av kommandot nedan. Den statiska adressen måste vara en adress som inte används för undernätet.

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        Skapa så många konfigurationer som du vill använda unika konfigurationsnamn och privata IP-adresser (för konfigurationer med statiska IP-adresser).

    - **Lägg till en offentlig IP-adress**
    
        En offentlig IP-adress har lagts till genom att associera den till en ny IP-konfiguration eller en befintlig IP-konfiguration. Slutför stegen i något av avsnitten som följer, som du behöver.

        > [!NOTE]
        > Offentliga IP-adresser har en låg kostnad. Mer information om priser för IP-adress i [IP-adress priser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).
        >

        **Koppla resursen till en ny IP-konfiguration**
    
        När du lägger till en offentlig IP-adress i en ny IP-konfiguration måste du också lägga en privat IP-adress, eftersom alla IP-konfigurationer måste ha en privat IP-adress. Du kan lägga till en befintlig offentlig IP-adressresurs eller skapa en ny. Ange följande kommando för att skapa en ny:

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

        Att skapa en ny IP-konfiguration med en statisk privat IP-adress och den associerade *myPublicIP3* offentlig IP adress resursen måste du ange följande kommando:

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **Koppla resursen till en befintlig IP-konfiguration**

        En offentlig IP-adressresurs kan bara vara kopplad till en IP-konfiguration som inte redan har en associerad. Du kan avgöra om en IP-konfiguration har en tillhörande offentliga IP-adress genom att ange följande kommando:

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        Leta efter en rad som liknar det som följer för IPConfig 3 i den returnerade utdatan:

        ```         
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        Eftersom den **offentliga IP-Adressen** för *IpConfig-3* är tomt, inga offentliga IP-adressresurs är för närvarande associerad till den. Du kan lägga till en befintlig offentlig IP-adressresurs IpConfig-3 eller ange följande kommando för att skapa en:

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        Ange följande kommando för att associera den offentliga IP-adressresursen till befintliga IP-konfigurationen med namnet *IPConfig-3*:
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. Visa de privata IP-adresserna och de offentliga IP-adressresurser som tilldelats till nätverkskortet genom att ange följande kommando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      Returnerade utdata liknar följande:
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. Lägg till de privata IP-adresser som du har lagt till nätverkskortet på VM-operativsystemet genom att följa anvisningarna i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till de offentliga IP-adresserna till operativsystemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
