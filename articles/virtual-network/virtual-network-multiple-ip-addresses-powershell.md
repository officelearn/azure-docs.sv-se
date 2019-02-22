---
title: Flera IP-adresser för Azure virtual machines – PowerShell | Microsoft Docs
description: Lär dig mer om att tilldela flera IP-adresser till en virtuell dator med hjälp av PowerShell. | Resource Manager
services: virtual-network
documentationcenter: na
author: jimdial
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: jdial;annahar
ms.openlocfilehash: f9ce9bc9272eb76c4162ab0abedd93e959385124
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649058"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Tilldela flera IP-adresser till virtuella datorer med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Den här artikeln beskriver hur du skapar en virtuell dator (VM) med Azure Resource Manager-distributionsmodellen med hjälp av PowerShell. Flera IP-adresser kan inte tilldelas till resurser som skapats via den klassiska distributionsmodellen. Mer information om Azures distributionsmodeller i [förstå distributionsmodeller](../resource-manager-deployment-model.md) artikeln.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Stegen nedan beskriver hur du skapar ett exempel VM med flera IP-adresser, som beskrivs i scenariot. Ändra variabelvärden som krävs för din implementering.

1. Öppna en PowerShell-kommandotolk och slutför de återstående stegen i det här avsnittet i en enda PowerShell-session. Om du inte redan har PowerShell installerad och konfigurerad kan du slutföra stegen i den [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) artikeln.
2. Logga in på ditt konto med den `Connect-AzAccount` kommando.
3. Ersätt *myResourceGroup* och *westus* med ett namn och plats. Skapa en resursgrupp. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Skapa ett virtuellt nätverk (VNet) och undernät i samma plats som resursgruppen:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Skapa en nätverkssäkerhetsgrupp (NSG) och en regel. Nätverkssäkerhetsgruppen skyddar den virtuella datorn med hjälp av inkommande och utgående regler. I detta fall skapas en regel för inkommande trafik för port 3389, som tillåter inkommande anslutningar till fjärrskrivbord.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definiera den primära IP-konfigurationen för nätverkskortet. Ändra 10.0.0.4 till en giltig adress i undernätet som du skapade, om du inte använder värdet som definierats tidigare. Innan du tilldelar en statisk IP-adress, rekommenderar vi att du först bekräfta att det inte är redan används. Ange kommandot `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Om adressen är tillgänglig, utdata returnerar *SANT*. Om den inte är tillgänglig utdata returnerar *FALSKT* och en lista med adresser som är tillgängliga. 

    I följande kommandon och **Ersätt < Ersätt-med-your-unika-name > med unikt DNS-namn du använder.** Namnet måste vara unikt över alla offentliga IP-adresser inom en Azure-region. Det här är en valfri parameter. Det kan tas bort om du vill ansluta till den virtuella datorn med offentliga IP-adress.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    När du tilldelar ett nätverkskort flera IP-konfigurationer måste ha tilldelats en konfiguration som den *-primära*.

    > [!NOTE]
    > Offentliga IP-adresser har en nominell avgift. Mer information om priser för IP-adress i [prissättning för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).

7. Definiera de sekundära IP-konfigurationerna för nätverkskortet. Du kan lägga till eller ta bort konfigurationer efter behov. Varje IP-konfiguration måste ha en privat IP-adress. Varje konfiguration kan du kan också ha en offentlig IP-adress.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Skapa nätverkskortet och associera den tre IP-konfigurationerna för:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Även om alla konfigurationer har tilldelats ett nätverkskort i den här artikeln, kan du tilldela flera IP-konfigurationer till varje nätverkskort som är kopplat till den virtuella datorn. Om du vill veta hur du skapar en virtuell dator med flera nätverkskort kan du läsa den [skapa en virtuell dator med flera nätverkskort](../virtual-machines/windows/multiple-nics.md) artikeln.

9. Skapa den virtuella datorn genom att ange följande kommandon:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Lägga till de privata IP-adresserna till VM-operativsystem genom att följa stegen för operativsystemet i den [Lägg till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till offentliga IP-adresser för operativsystemet.

## <a name="add"></a>Lägg till IP-adresser till en virtuell dator

Du kan lägga till privata och offentliga IP-adresser till Azure-nätverksgränssnitt genom att följa stegen nedan. Exemplen i avsnitten nedan förutsätter att du redan har en virtuell dator med de tre IP-konfigurationer som beskrivs i den [scenariot](#Scenario) i den här artikeln, men det inte krävs att du gör.

1. Öppna en PowerShell-kommandotolk och slutför de återstående stegen i det här avsnittet i en enda PowerShell-session. Om du inte redan har PowerShell installerad och konfigurerad kan du slutföra stegen i den [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) artikeln.
2. Ändra följande $Variables ”values” till namnet på det nätverkskort som du vill lägga till IP-adressen och resursgrupp och plats som nätverkskortet finns i:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Om du inte vet namnet på det nätverkskort som du vill ändra genom att ange följande kommandon, ändrar du värdena för variabler som tidigare:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Skapa en variabel och ange den till befintligt nätverkskort genom att skriva följande kommando:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. I följande kommandon och ändra *MyVNet* och *MySubnet* motsvarar namnen på VNet och undernät som nätverkskortet är anslutet till. Ange kommandon för att hämta objekt VNet och undernät som nätverkskortet är anslutet till:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Om du inte vet namnet på VNet eller undernätet som nätverkskortet är anslutet till, anger du följande kommando:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Utdata och Sök efter text som liknar följande Exempelutdata:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    I dessa utdata *MyVnet* är det virtuella nätverket och *MySubnet* är undernätet som nätverkskortet är anslutet till.

5. Utför stegen i något av följande avsnitt, baserat på dina krav:

   **Lägg till en privat IP-adress**

   Om du vill lägga till en privat IP-adress till ett nätverkskort, måste du skapa en IP-konfiguration. Följande kommando skapar en konfiguration med en statisk IP-adress 10.0.0.7. När du anger en statisk IP-adress, måste det vara en oanvänd adress för undernätet. Vi rekommenderar att du först testar adressen för att säkerställa att den är tillgänglig genom att ange den `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` kommando. Om IP-adressen är tillgänglig, utdata returnerar *SANT*. Om den inte är tillgänglig utdata returnerar *FALSKT*, och en lista med adresser som är tillgängliga.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Skapa så många konfigurationer som du behöver med unika konfigurationsnamn och privata IP-adresser (för konfigurationer med statiska IP-adresser).

   Lägga till den privata IP-adressen till VM-operativsystem genom att följa stegen för operativsystemet i den [Lägg till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln.

   **Lägg till en offentlig IP-adress**

   En offentlig IP-adress har lagts till genom att associera en offentlig IP-adressresurs till en ny IP-konfiguration eller en befintlig IP-konfiguration. Slutför stegen i ett av avsnitten som följer, som du behöver.

   > [!NOTE]
   > Offentliga IP-adresser har en nominell avgift. Mer information om priser för IP-adress i [prissättning för IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).
   >

   **Associera den offentliga IP-adressresursen till en ny IP-konfiguration**

   När du lägger till en offentlig IP-adress i en ny IP-konfiguration, du måste också lägga till en privat IP-adress, eftersom alla IP-konfigurationer måste ha en privat IP-adress. Du kan lägga till en befintlig offentlig IP-adressresurs eller skapa en ny. Om du vill skapa ett nytt lösenord, anger du följande kommando:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Att skapa en ny IP-konfiguration med en statisk privat IP-adress och den associerade *myPublicIp3* offentliga IP-adressen resurs, anger du följande kommando:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Associera den offentliga IP-adressresursen till en befintlig IP-konfiguration**

   En offentlig IP-adressresurs kan bara kopplas till en IP-konfiguration som inte redan har en associerad. Du kan avgöra om en IP-konfiguration har en associerad offentlig IP-adress genom att ange följande kommando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Du ser utdata som liknar följande:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Eftersom den **PublicIpAddress** kolumn för *IpConfig-3* är tomt används ingen offentlig IP-adressresurs är för närvarande är kopplad dit. Du kan lägga till en befintlig offentlig IP-adressresurs i IpConfig-3 eller ange följande kommando för att skapa en:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Ange följande kommando för att associera den offentliga IP-adressresursen till befintliga IP-konfigurationen med namnet *IpConfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Ange nätverkskort med den nya IP-konfigurationen genom att ange följande kommando:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Visa de privata IP-adresserna och offentliga IP-adressresurser som tilldelats till nätverkskortet genom att ange följande kommando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Lägga till den privata IP-adressen till VM-operativsystem genom att följa stegen för operativsystemet i den [Lägg till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till offentliga IP-adress för operativsystemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]