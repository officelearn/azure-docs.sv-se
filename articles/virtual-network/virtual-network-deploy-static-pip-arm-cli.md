---
title: Skapa en virtuell dator med en statisk offentlig IP-adress - Azure CLI | Microsoft Docs
description: "Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure-kommandoradsgränssnittet (CLI)."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c50f685745a645b5fbe383a5fe4726faa0e36345
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [Mall](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (klassisk)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln täcker distributionsmodell hanteraren för filserverresurser, som Microsoft rekommenderar för de flesta nya distributioner i stället för den klassiska distributionsmodellen.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Skapa den virtuella datorn

Värdena i ”” för variabler i de steg som följer skapa resurser med inställningar för scenariot. Ändra värdena för din miljö.

1. Installera den [Azure CLI 2.0](/cli/azure/install-az-cli2) om du inte redan har installerats.
2. Skapa en SSH offentlig och privat nyckel för Linux virtuella datorer genom att slutföra stegen i den [skapa en SSH offentlig och privat nyckel för Linux virtuella datorer](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Från en kommandotolk, logga in med kommandot `az login`.
4. Skapa den virtuella datorn genom att köra skriptet som följer på en Linux- eller Mac-dator. Azure offentlig IP-adress, virtuella nätverk, nätverksgränssnitt och Virtuella resurser finnas på samma plats. Även om resurserna inte alla finns i samma resursgrupp kan i följande skript gör de.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
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
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

Förutom att skapa en virtuell dator skapar skriptet:
- En enda premium hanterade disken som standard, men du har andra alternativ för den typ av disk som du kan skapa. Läs den [skapa en Linux VM som använder Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikeln för information.
- Virtuella nätverk, undernät, nätverkskort och offentliga IP-adress-resurser. Du kan också använda *befintliga* virtuellt nätverk, undernät, nätverkskort eller resurserna för offentlig IP-adress. Information om hur du använder befintliga nätverksresurser i stället för att skapa ytterligare resurser, ange `az vm create -h`.

## <a name = "validate"></a>Validera skapa Virtuella och den offentliga IP-adress

1. Ange kommandot `az resource list --resouce-group IaaSStory --output table` att se en lista över de resurser som skapats av skriptet. Det bör finnas fem resurser i returnerade utdata: network interface, disk, offentlig IP-adress, virtuella nätverk och en virtuell dator.
2. Ange kommandot `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. Returnerade utdata och anteckna värdet för **IpAddress** och att värdet för **PublicIpAllocationMethod** är *statiska*.
3. Ta bort <> innan du kör följande kommando ersätter *användarnamn* med namn som du använde för den **användarnamn** variabeln i skript och ersätter *IP-adress* med den **IP-adress** från föregående steg. Kör följande kommando för att ansluta till den virtuella datorn: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Ta bort den virtuella datorn och associerade resurser

Vi rekommenderar att du tar bort de resurser som skapades i den här övningen om du inte använder dem i produktion. VM, offentlig IP-adress och diskresurser avgifter, så länge som de har etablerats. Om du vill ta bort de resurser som skapades under den här övningen, gör du följande:

1. Om du vill visa resurserna i resursgruppen kör den `az resource list --resource-group IaaSStory` kommando.
2. Bekräfta att det finns inga resurser i resursgrupp än de resurser som skapades av skriptet i den här artikeln. 
3. Om du vill ta bort alla resurser som har skapats i den här övningen kör den `az group delete -n IaaSStory` kommando. Kommandot tar bort resursgruppen och alla resurser som den innehåller.

## <a name="next-steps"></a>Nästa steg

All nätverkstrafik kan flöda till och från den virtuella datorn skapas i den här artikeln. Du kan definiera inkommande och utgående regler inom en NSG som begränsar trafiken kan flöda till och från nätverksgränssnittet undernätet eller båda. Mer information om NSG: er i [NSG översikt](virtual-networks-nsg.md) artikel.