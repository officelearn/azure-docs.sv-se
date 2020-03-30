---
title: VIRTUELL med flera IP-adresser med Azure CLI
titlesuffix: Azure Virtual Network
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med hjälp av Azure command-line interface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: 144f30463adb3dfbce1717e06548baccc8286f8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240228"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Tilldela flera IP-adresser till virtuella datorer med Azure CLI

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

I den här artikeln beskrivs hur du skapar en virtuell dator (VM) via Azure Resource Manager-distributionsmodellen med hjälp av Azure CLI. Flera IP-adresser kan inte tilldelas resurser som skapats via den klassiska distributionsmodellen. Mer information om Azure-distributionsmodeller finns i artikeln [Förstå distributionsmodeller.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Stegen som följer förklarar hur du skapar en virtuell exempeldator med flera IP-adresser, enligt beskrivningen i scenariot. Ändra variabla värden i "" och IP-adresstyper, efter behov, för din implementering. 

1. Installera [Azure CLI](/cli/azure/install-azure-cli) om du inte redan har den installerad.
2. Skapa ett SSH-offentligt och privat nyckelpar för virtuella Linux-datorer genom att slutföra stegen i [det offentliga och privata nyckelparet Skapa en SSH för virtuella Linux-datorer](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Logga in med kommandot `az login` från ett kommandoskal och välj den prenumeration du använder.
4. Skapa den virtuella datorn genom att köra skriptet som följer på en Linux- eller Mac-dator. Skriptet skapar en resursgrupp, ett virtuellt nätverk (VNet), ett nätverkskort med tre IP-konfigurationer och en virtuell dator med de två nätverkskorten kopplade till det. Nätverkskortet, den offentliga IP-adressen, det virtuella nätverket och vm-resurserna måste alla finnas på samma plats och prenumeration. Även om resurserna inte alla behöver finnas i samma resursgrupp, i följande skript de gör.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
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
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
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

- En enda premiumhanterad disk som standard, men du har andra alternativ för den disktyp du kan skapa. Mer information finns i artikeln [Skapa en virtuell Linux-dator med hjälp av](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure CLI-artikeln.
- Ett virtuellt nätverk med ett undernät och två offentliga IP-adresser. Du kan också använda *befintliga* virtuella nätverks-, undernäts-, nätverkskorts- eller offentliga IP-adressresurser. Om du vill lära dig hur du använder `az vm create -h`befintliga nätverksresurser i stället för att skapa ytterligare resurser anger du .

Offentliga IP-adresser har en nominell avgift. Mer information om IP-adressprissättning finns på [prissidan för IP-adresser.](https://azure.microsoft.com/pricing/details/ip-addresses) Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

När den virtuella datorn `az network nic show --name MyNic1 --resource-group myResourceGroup` har skapats anger du kommandot för att visa nätverkskortets konfiguration. Ange `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` om du vill visa en lista över IP-konfigurationer som är associerade till nätverkskortet.

Lägg till de privata IP-adresserna i operativsystemet VM genom att slutföra stegen för operativsystemet i avsnittet [Lägg till IP-adresser i ett vm-operativsystem](#os-config) i den här artikeln.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Lägga till IP-adresser till en virtuell dator

Du kan lägga till ytterligare privata och offentliga IP-adresser i ett befintligt Azure-nätverksgränssnitt genom att slutföra stegen som följer. Exemplen bygger på [det scenario](#scenario) som beskrivs i den här artikeln.

1. Öppna ett kommandoskal och slutför de återstående stegen i det här avsnittet i en enda session. Om du inte redan har Azure CLI installerat och konfigurerat utför du stegen i azure `az-login` [CLI-installationsartikeln](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) och loggar in på ditt Azure-konto med kommandot.

2. Gör stegen i något av följande avsnitt, baserat på dina krav:

    **Lägga till en privat IP-adress**
    
    Om du vill lägga till en privat IP-adress i ett nätverkskort måste du skapa en IP-konfiguration med hjälp av det kommando som följer. Den statiska IP-adressen måste vara en oanvänd adress för undernätet.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Skapa så många konfigurationer som du behöver, med hjälp av unika konfigurationsnamn och privata IP-adresser (för konfigurationer med statiska IP-adresser).

    **Lägga till en offentlig IP-adress**
    
    En offentlig IP-adress läggs till genom att associera den till antingen en ny IP-konfiguration eller en befintlig IP-konfiguration. Gör stegen i ett av de avsnitt som följer, som du behöver.

    Offentliga IP-adresser har en nominell avgift. Mer information om IP-adressprissättning finns på [prissidan för IP-adresser.](https://azure.microsoft.com/pricing/details/ip-addresses) Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

    - **Associera resursen till en ny IP-konfiguration**
    
        När du lägger till en offentlig IP-adress i en ny IP-konfiguration måste du också lägga till en privat IP-adress, eftersom alla IP-konfigurationer måste ha en privat IP-adress. Du kan antingen lägga till en befintlig offentlig IP-adressresurs eller skapa en ny. Om du vill skapa en ny anger du följande kommando:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Om du vill skapa en ny IP-konfiguration med en statisk privat IP-adress och den associerade offentliga IP-adressresursen *myPublicIP3* anger du följande kommando:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Associera resursen till en befintlig IP-konfiguration** En offentlig IP-adressresurs kan bara associeras till en IP-konfiguration som inte redan har en associerad. Du kan avgöra om en IP-konfiguration har en associerad offentlig IP-adress genom att ange följande kommando:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Returnerad utdata:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Eftersom kolumnen **PublicIpAddressId** för *IpConfig-3* är tom i utdata är ingen offentlig IP-adressresurs för närvarande kopplad till den. Du kan lägga till en befintlig offentlig IP-adressresurs i IpConfig-3 eller ange följande kommando för att skapa en:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Ange följande kommando för att associera den offentliga IP-adressresursen till den befintliga IP-konfigurationen MED namnet *IPConfig-3:*
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Visa de privata IP-adresserna och de offentliga IP-adressresurs-ID:erna som tilldelats nätverkskortet genom att ange följande kommando:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Returnerad utdata: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Lägg till de privata IP-adresser som du har lagt till i nätverkskortet i operativsystemet Vm genom att följa instruktionerna i avsnittet [Lägg till IP-adresser i ett vm-operativsystem](#os-config) i den här artikeln. Lägg inte till de offentliga IP-adresserna i operativsystemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
