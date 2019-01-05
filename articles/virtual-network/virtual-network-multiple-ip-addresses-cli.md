---
title: Virtuell dator med flera IP-adresser med hjälp av Azure CLI
titlesuffix: Azure Virtual Network
description: Lär dig mer om att tilldela flera IP-adresser till en virtuell dator med kommandoradsgränssnittet (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: jimdial
ms.openlocfilehash: b383bd601335e8c828c4c734ad8015b650fdc9f3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54055123"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Tilldela flera IP-adresser till virtuella datorer med hjälp av Azure CLI

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Den här artikeln beskriver hur du skapar en virtuell dator (VM) med Azure Resource Manager-distributionsmodellen med hjälp av Azure CLI. Flera IP-adresser kan inte tilldelas till resurser som skapats via den klassiska distributionsmodellen. Mer information om Azures distributionsmodeller i [förstå distributionsmodeller](../resource-manager-deployment-model.md) artikeln.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Stegen nedan beskriver hur du skapar en exempel-dator med flera IP-adresser, som beskrivs i scenariot. Ändra variabelvärden i ”” och IP-adresstyper som krävs för din implementering. 

1. Installera den [Azure CLI](/cli/azure/install-azure-cli) om du inte redan har installerat.
2. Skapa ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer genom att följa stegen i den [skapa ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Från en kommandotolk, logga in med kommandot `az login` och välj den prenumeration som du använder.
4. Skapa den virtuella datorn genom att köra skriptet som följer på en Linux- eller Mac-dator. Skriptet skapar en resursgrupp, ett virtuellt nätverk (VNet), ett nätverkskort med tre IP-konfigurationer och en virtuell dator med två nätverkskort kopplat till den. NIC, offentlig IP-adress, virtuellt nätverk och VM-resurserna finnas i samma plats och prenumeration. Även om resurserna som inte har finnas i samma resursgrupp, i följande skript gör de.

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

az network nic ip-config create \
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

- En enda premium managed disk som standard, men du har andra alternativ för typ av disk som du kan skapa. Läs den [skapa en Linux-VM med Azure CLI](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nedan för information.
- Ett virtuellt nätverk med ett undernät och två offentliga IP-adresser. Du kan också använda *befintliga* virtuella nätverk, undernät, nätverkskort eller offentlig IP-adressresurser. Om du vill lära dig mer om att använda befintliga nätverksresurser i stället för skapa ytterligare resurser, ange `az vm create -h`.

Offentliga IP-adresser har en nominell avgift. Mer information om priser för IP-adress i [prissättning för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).

När den virtuella datorn har skapats kan du ange den `az network nic show --name MyNic1 --resource-group myResourceGroup` kommando för att visa konfigurationen av nätverkskort. Ange den `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` att visa en lista över IP-konfigurationer som är kopplad till nätverkskortet.

Lägga till de privata IP-adresserna till VM-operativsystem genom att följa stegen för operativsystemet i den [Lägg till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln.

## <a name="add"></a>Lägg till IP-adresser till en virtuell dator

Du kan lägga till ytterligare privata och offentliga IP-adresser till en befintlig Azure nätverksgränssnitt genom att följa stegen nedan. I exemplen som bygger på den [scenariot](#Scenario) som beskrivs i den här artikeln.

1. Öppna en kommandotolk och slutför de återstående stegen i det här avsnittet i en enda session. Om du inte redan har Azure CLI installerat och konfigurerat kan du slutföra stegen i den [installation av Azure CLI](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) artikeln och logga in på ditt Azure-konto med den `az-login` kommando.

2. Utför stegen i något av följande avsnitt, baserat på dina krav:

    **Lägg till en privat IP-adress**
    
    Du måste skapa en IP-konfiguration med hjälp av kommandot nedan för att lägga till en privat IP-adress till ett nätverkskort. Den statiska IP-adressen måste vara en adress som är oanvända för undernätet.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Skapa så många konfigurationer som du behöver med unika konfigurationsnamn och privata IP-adresser (för konfigurationer med statiska IP-adresser).

    **Lägg till en offentlig IP-adress**
    
    En offentlig IP-adress har lagts till genom att associera den till en ny IP-konfiguration eller en befintlig IP-konfiguration. Slutför stegen i ett av avsnitten som följer, som du behöver.

    Offentliga IP-adresser har en nominell avgift. Mer information om priser för IP-adress i [prissättning för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).

    - **Koppla resursen till en ny IP-konfiguration**
    
        När du lägger till en offentlig IP-adress i en ny IP-konfiguration, du måste också lägga till en privat IP-adress, eftersom alla IP-konfigurationer måste ha en privat IP-adress. Du kan lägga till en befintlig offentlig IP-adressresurs eller skapa en ny. Om du vill skapa ett nytt lösenord, anger du följande kommando:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Att skapa en ny IP-konfiguration med en statisk privat IP-adress och den associerade *myPublicIP3* offentliga IP-adressen resurs, anger du följande kommando:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Koppla resursen till en befintlig IP-konfiguration** en offentlig IP-adressresurs kan bara kopplas till en IP-konfiguration som inte redan har en associerad. Du kan avgöra om en IP-konfiguration har en associerad offentlig IP-adress genom att ange följande kommando:

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

        Eftersom den **PublicIpAddressId** kolumn för *IpConfig-3* är tomma i utdata, ingen offentlig IP-adressresurs är för närvarande är kopplad dit. Du kan lägga till en befintlig offentlig IP-adressresurs i IpConfig-3 eller ange följande kommando för att skapa en:

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

3. Visa de privata IP-adresserna och den offentliga IP-adressresursen ID: N som tilldelats till nätverkskortet genom att ange följande kommando:

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
    

4. Lägg till de privata IP-adresser som du lade till nätverkskortet till VM-operativsystem genom att följa anvisningarna i den [Lägg till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till offentliga IP-adresser för operativsystemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
