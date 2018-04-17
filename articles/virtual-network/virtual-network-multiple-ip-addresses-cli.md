---
title: Virtuell dator med flera IP-adresser med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med Azure-kommandoradsgränssnittet (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: jimdial
ms.openlocfilehash: a1e78f7fa892586385e1dbd186125630ee1fb307
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Tilldela flera IP-adresser till virtuella datorer med hjälp av Azure CLI

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Den här artikeln förklaras hur du skapar en virtuell dator (VM) via Azure Resource Manager-distributionsmodellen med hjälp av Azure CLI. Flera IP-adresser kan inte tilldelas till resurser som skapats via den klassiska distributionsmodellen. Om du vill veta mer om Azure distributionsmodeller kan läsa den [förstår distributionsmodellerna](../resource-manager-deployment-model.md) artikel.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

De steg som följer beskrivs hur du skapar en virtuell dator i exemplet med flera IP-adresser, som beskrivs i scenariot. Ändra variabelvärden i ”” och IP-adresstyper som krävs för din implementering. 

1. Installera den [Azure CLI 2.0](/cli/azure/install-az-cli2) om du inte redan har installerats.
2. Skapa en SSH offentlig och privat nyckel för Linux virtuella datorer genom att slutföra stegen i den [skapa en SSH offentlig och privat nyckel för Linux virtuella datorer](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Från en kommandotolk, logga in med kommandot `az login` och välj den prenumeration som du använder.
4. Skapa den virtuella datorn genom att köra skriptet som följer på en Linux- eller Mac-dator. Skriptet skapar en resursgrupp, ett virtuellt nätverk (VNet), ett nätverkskort med tre IP-konfigurationer och en virtuell dator med två nätverkskort anslutna. NIC, offentlig IP-adress, virtuella nätverk och nätverksresurser på Virtuella finnas på samma plats och prenumeration. Även om resurserna inte alla finns i samma resursgrupp kan i följande skript gör de.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP adresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

azure network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes rticle. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location estcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the utput returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Förutom att skapa en virtuell dator med ett nätverkskort med 3 IP-konfigurationer, skapar skriptet:

- En enda premium hanterade disken som standard, men du har andra alternativ för den typ av disk som du kan skapa. Läs den [skapa en Linux VM som använder Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikeln för information.
- Ett virtuellt nätverk med ett undernät och två offentliga IP-adresser. Du kan också använda *befintliga* virtuellt nätverk, undernät, nätverkskort eller resurserna för offentlig IP-adress. Information om hur du använder befintliga nätverksresurser i stället för att skapa ytterligare resurser, ange `az vm create -h`.

Offentliga IP-adresser har en låg kostnad. Mer information om priser för IP-adress i [IP-adress priser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).

När den virtuella datorn har skapats kan du ange den `az network nic show --name MyNic1 --resource-group myResourceGroup` kommando för att visa NIC-konfiguration. Ange den `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` att visa en lista över IP-konfigurationer som är kopplade till nätverkskortet.

Lägga till privata IP-adresser i Virtuella operativsystem genom att slutföra stegen för operativsystemet i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln.

## <a name="add"></a>Lägg till IP-adresser till en virtuell dator

Du kan lägga till ytterligare privata och offentliga IP-adresser till en befintlig Azure nätverksgränssnitt genom att slutföra de steg som följer. Exemplen bygger på den [scenariot](#Scenario) beskrivs i den här artikeln.

1. Öppna en kommandotolk och utföra stegen i det här avsnittet i en enda session. Om du inte redan har installerat och konfigurerat Azure-CLI kan du slutföra stegen i den [installationen Azure CLI 2.0](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel och logga in till din Azure-konto med den `az-login` kommando.

2. Utför stegen i följande avsnitt, baserat på dina krav:

    **Lägg till en privat IP-adress**
    
    Om du vill lägga till en privat IP-adress till ett nätverkskort måste du skapa en IP-konfiguration med hjälp av kommandot som följer. Den statiska IP-adressen måste vara en adress som inte används för undernätet.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Skapa så många konfigurationer som du vill använda unika konfigurationsnamn och privata IP-adresser (för konfigurationer med statiska IP-adresser).

    **Lägg till en offentlig IP-adress**
    
    En offentlig IP-adress har lagts till genom att associera den till en ny IP-konfiguration eller en befintlig IP-konfiguration. Slutför stegen i något av avsnitten som följer, som du behöver.

    Offentliga IP-adresser har en låg kostnad. Mer information om priser för IP-adress i [IP-adress priser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).

    - **Koppla resursen till en ny IP-konfiguration**
    
        När du lägger till en offentlig IP-adress i en ny IP-konfiguration måste du också lägga en privat IP-adress, eftersom alla IP-konfigurationer måste ha en privat IP-adress. Du kan lägga till en befintlig offentlig IP-adressresurs eller skapa en ny. Ange följande kommando för att skapa en ny:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Att skapa en ny IP-konfiguration med en statisk privat IP-adress och den associerade *myPublicIP3* offentlig IP adress resursen måste du ange följande kommando:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Koppla resursen till en befintlig IP-konfiguration** en offentlig IP-adressresurs kan bara vara kopplad till en IP-konfiguration som inte redan har en associerad. Du kan avgöra om en IP-konfiguration har en tillhörande offentliga IP-adress genom att ange följande kommando:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Returnerade utdata:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Eftersom den **PublicIpAddressId** för *IpConfig-3* är tomt i utdata, ingen offentlig IP-adressresurs är för närvarande associerad till den. Du kan lägga till en befintlig offentlig IP-adressresurs IpConfig-3 eller ange följande kommando för att skapa en:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Ange följande kommando för att associera den offentliga IP-adressresursen till befintliga IP-konfigurationen med namnet *IPConfig-3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Visa de privata IP-adresserna och offentliga IP-adressresurs ID som tilldelats till nätverkskortet genom att ange följande kommando:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Returnerade utdata: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Lägg till de privata IP-adresser som du har lagt till nätverkskortet på VM-operativsystemet genom att följa anvisningarna i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till de offentliga IP-adresserna till operativsystemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
