---
title: Skapa en virtuell dator med en statisk offentlig IP-adress – Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av kommandoradsgränssnittet (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
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
ms.openlocfilehash: bd44971162a79e53b731c5c89316f14e8bb0a1a6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651967"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klassisk)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver Resource Manager-distributionsmodellen, som Microsoft rekommenderar för de flesta nya distributioner i stället för den klassiska distributionsmodellen.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Skapa den virtuella datorn

Värdena i ”” för variabler i stegen nedan för att skapa resurser med inställningar från scenariot. Ändra värdena för din miljö.

1. Installera den [Azure CLI 2.0](/cli/azure/install-az-cli2) om du inte redan har installerat.
2. Skapa ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer genom att följa stegen i den [skapa ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Från en kommandotolk, logga in med kommandot `az login`.
4. Skapa den virtuella datorn genom att köra skriptet som följer på en Linux- eller Mac-dator. Azure offentlig IP-adress, virtuellt nätverk, nätverksgränssnitt och Virtuella datorresurserna finnas på samma plats. Även om resurserna som inte har finnas i samma resursgrupp, i följande skript gör de.

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

Förutom att skapa en virtuell dator, skapar skriptet:
- En enda premium managed disk som standard, men du har andra alternativ för typ av disk som du kan skapa. Läs den [skapa en Linux-VM med Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nedan för information.
- Virtuellt nätverk, undernät, nätverkskort och offentliga IP-adress-resurser. Du kan också använda *befintliga* virtuella nätverk, undernät, nätverkskort eller offentlig IP-adressresurser. Om du vill lära dig mer om att använda befintliga nätverksresurser i stället för skapa ytterligare resurser, ange `az vm create -h`.

## <a name = "validate"></a>Verifiera skapa en virtuell dator och offentlig IP-adress

1. Ange kommandot `az resource list --resouce-group IaaSStory --output table` att se en lista över resurser som skapas av skriptet. Det bör finnas fem resurser i den returnerade utdatan: gränssnitt, disk, offentlig IP-adress, virtuellt nätverk och en virtuell dator.
2. Ange kommandot `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. Returnerade utdata och anteckna värdet för **IpAddress** och att värdet för **PublicIpAllocationMethod** är *statiska*.
3. Ta bort <> innan du kör följande kommando och Ersätt *användarnamn* med namnet du använde för den **användarnamn** variabel i skriptet och Ersätt *ipAddress*med den **ipAddress** från föregående steg. Kör följande kommando för att ansluta till den virtuella datorn: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Ta bort den virtuella datorn och associerade resurser

Vi rekommenderar att du tar bort de resurser som skapades i den här övningen om du inte använder dem i produktion. Virtuell dator, offentlig IP-adress och diskresurser avgifter, så länge de är etablerade. Utför följande steg för att ta bort de resurser som skapades under den här övningen:

1. Om du vill visa resurserna i resursgruppen, kör den `az resource list --resource-group IaaSStory` kommando.
2. Bekräfta att det finns inga resurser i resursgruppen, än de resurser som skapats av skriptet i den här artikeln. 
3. Om du vill ta bort alla resurser som skapats i den här övningen kör den `az group delete -n IaaSStory` kommando. Kommandot tar bort resursgruppen och alla resurser som den innehåller.
 
## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser i operativsystemet

Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem. Vi rekommenderar att du inte statiskt tilldelar privat IP-adress som tilldelats virtuella Azure-datorer i operativsystemet på en virtuell dator, om inte behövs, t.ex när [tilldela flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-cli.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Läs mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar.

## <a name="next-steps"></a>Nästa steg

All nätverkstrafik kan flöda till och från den virtuella datorn skapas i den här artikeln. Du kan definiera inkommande och utgående säkerhetsregler inom en nätverkssäkerhetsgrupp som begränsar trafiken kan flöda till och från ett nätverksgränssnitt, undernätet eller båda. Mer information om nätverkssäkerhetsgrupper finns [översikt över Network security group](security-overview.md).