---
title: Flera IP-adresser för virtuella Azure-datorer – PowerShell | Microsoft-dokument
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med PowerShell. | Resurshanteraren
services: virtual-network
documentationcenter: na
author: KumudD
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
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: a8bd4e4779d94cfc22ac7726c9746fe755764033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279577"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Tilldela flera IP-adresser till virtuella datorer med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

I den här artikeln beskrivs hur du skapar en virtuell dator (VM) via Distributionsmodellen för Azure Resource Manager med PowerShell. Flera IP-adresser kan inte tilldelas resurser som skapats via den klassiska distributionsmodellen. Mer information om Azure-distributionsmodeller finns i artikeln [Förstå distributionsmodeller.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Stegen som följer förklarar hur du skapar ett exempel virtuell dator med flera IP-adresser, som beskrivs i scenariot. Ändra variabelvärden som krävs för implementeringen.

1. Öppna en PowerShell-kommandotolk och slutför de återstående stegen i det här avsnittet i en enda PowerShell-session. Om du inte redan har Installerat och konfigurerat PowerShell utför du stegen i artikeln Så här installerar och konfigurerar du [Azure PowerShell.](/powershell/azure/overview)
2. Logga in på `Connect-AzAccount` ditt konto med kommandot.
3. Ersätt *myResourceGroup* och *westus* med ett namn och en plats som du väljer. Skapa en resursgrupp. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Skapa ett virtuellt nätverk (VNet) och undernät på samma plats som resursgruppen:

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

5. Skapa en nätverkssäkerhetsgrupp (NSG) och en regel. NSG skyddar den virtuella datorn med hjälp av inkommande och utgående regler. I detta fall skapas en regel för inkommande trafik för port 3389, som tillåter inkommande anslutningar till fjärrskrivbord.

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

6. Definiera den primära IP-konfigurationen för nätverkskortet. Ändra 10.0.0.4 till en giltig adress i undernätet som du skapade, om du inte använde det värde som definierats tidigare. Innan du tilldelar en statisk IP-adress rekommenderar vi att du först bekräftar att den inte redan används. Ange kommandot `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Om adressen är tillgänglig returnerar utdata *True*. Om den inte är tillgänglig returnerar utdata *False* och en lista över adresser som är tillgängliga. 

    I följande kommandon **ersätter du \<ersätt-med-ditt-unikt namn> med det unika DNS-namn som ska användas.** Namnet måste vara unikt för alla offentliga IP-adresser inom en Azure-region. Detta är en valfri parameter. Den kan tas bort om du bara vill ansluta till den virtuella datorn med den offentliga IP-adressen.

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

    När du tilldelar flera IP-konfigurationer till ett nätverkskort måste en konfiguration tilldelas som *-Primär*.

    > [!NOTE]
    > Offentliga IP-adresser har en nominell avgift. Mer information om IP-adressprissättning finns på [prissidan för IP-adresser.](https://azure.microsoft.com/pricing/details/ip-addresses) Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Definiera de sekundära IP-konfigurationerna för nätverkskortet. Du kan lägga till eller ta bort konfigurationer efter behov. Varje IP-konfiguration måste ha tilldelats en privat IP-adress. Varje konfiguration kan eventuellt ha en offentlig IP-adress tilldelad.

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

8. Skapa nätverkskortet och associera de tre IP-konfigurationerna till det:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Även om alla konfigurationer har tilldelats ett nätverkskort i den här artikeln kan du tilldela flera IP-konfigurationer till varje nätverkskort som är kopplat till den virtuella datorn. Om du vill veta hur du skapar en virtuell dator med flera nätverkskort läser du artikeln [Skapa en virtuell dator med flera nätverkskort.](../virtual-machines/windows/multiple-nics.md)

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

10. Lägg till de privata IP-adresserna i operativsystemet VM genom att slutföra stegen för operativsystemet i avsnittet [Lägg till IP-adresser i ett vm-operativsystem](#os-config) i den här artikeln. Lägg inte till de offentliga IP-adresserna i operativsystemet.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Lägga till IP-adresser till en virtuell dator

Du kan lägga till privata och offentliga IP-adresser i Azure-nätverksgränssnittet genom att slutföra stegen som följer. Exemplen i följande avsnitt förutsätter att du redan har en virtuell dator med de tre IP-konfigurationerna som beskrivs i [scenariot](#scenario) i den här artikeln, men det krävs inte att du gör det.

1. Öppna en PowerShell-kommandotolk och slutför de återstående stegen i det här avsnittet i en enda PowerShell-session. Om du inte redan har Installerat och konfigurerat PowerShell utför du stegen i artikeln Så här installerar och konfigurerar du [Azure PowerShell.](/powershell/azure/overview)
2. Ändra "värden" för följande $Variables till namnet på det nätverkskort som du vill lägga till IP-adress till och resursgruppen och platsen som nätverkskortet finns i:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Om du inte känner till namnet på det nätverkskort som du vill ändra anger du följande kommandon och ändrar sedan värdena för de tidigare variablerna:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Skapa en variabel och ställ in den på det befintliga nätverkskortet genom att skriva följande kommando:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. I följande kommandon ändrar du *MyVNet* och *MySubnet* till namnen på det virtuella nätverk och undernätet som nätverkskortet är anslutet till. Ange de kommandon som ska hämtas på det virtuella nätverket och undernätsobjekt som nätverkskortet är anslutet till:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Om du inte känner till det virtuella nätverk eller det nätnamn som nätverkskortet är anslutet till anger du följande kommando:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Leta efter text som liknar följande exempelutdata i utdata:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    I den här utdata är *MyVnet* det virtuella nätverket och *MySubnet* är det undernät som nätverkskortet är anslutet till.

5. Gör stegen i något av följande avsnitt, baserat på dina krav:

   **Lägga till en privat IP-adress**

   Om du vill lägga till en privat IP-adress i ett nätverkskort måste du skapa en IP-konfiguration. Följande kommando skapar en konfiguration med en statisk IP-adress på 10.0.0.7. När du anger en statisk IP-adress måste den vara en oanvänd adress för undernätet. Vi rekommenderar att du först testar adressen för att säkerställa `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` att den är tillgänglig genom att ange kommandot. Om IP-adressen är tillgänglig returnerar utdata *True*. Om den inte är tillgänglig returnerar utdata *False*och en lista över adresser som är tillgängliga.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Skapa så många konfigurationer som du behöver, med hjälp av unika konfigurationsnamn och privata IP-adresser (för konfigurationer med statiska IP-adresser).

   Lägg till den privata IP-adressen i operativsystemet VM genom att slutföra stegen för operativsystemet i avsnittet [Lägg till IP-adresser i ett vm-operativsystem](#os-config) i den här artikeln.

   **Lägga till en offentlig IP-adress**

   En offentlig IP-adress läggs till genom att associera en offentlig IP-adressresurs till antingen en ny IP-konfiguration eller en befintlig IP-konfiguration. Gör stegen i ett av de avsnitt som följer, som du behöver.

   > [!NOTE]
   > Offentliga IP-adresser har en nominell avgift. Mer information om IP-adressprissättning finns på [prissidan för IP-adresser.](https://azure.microsoft.com/pricing/details/ip-addresses) Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **Associera den offentliga IP-adressresursen till en ny IP-konfiguration**

   När du lägger till en offentlig IP-adress i en ny IP-konfiguration måste du också lägga till en privat IP-adress, eftersom alla IP-konfigurationer måste ha en privat IP-adress. Du kan antingen lägga till en befintlig offentlig IP-adressresurs eller skapa en ny. Om du vill skapa en ny anger du följande kommando:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Om du vill skapa en ny IP-konfiguration med en statisk privat IP-adress och den associerade offentliga IP-adressresursen *myPublicIp3* anger du följande kommando:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Associera den offentliga IP-adressresursen till en befintlig IP-konfiguration**

   En offentlig IP-adressresurs kan bara associeras till en IP-konfiguration som inte redan har en associerad. Du kan avgöra om en IP-konfiguration har en associerad offentlig IP-adress genom att ange följande kommando:

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

   Eftersom kolumnen **PublicIpAddress** för *IpConfig-3* är tom är ingen offentlig IP-adressresurs för närvarande kopplad till den. Du kan lägga till en befintlig offentlig IP-adressresurs i IpConfig-3 eller ange följande kommando för att skapa en:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Ange följande kommando för att associera den offentliga IP-adressresursen till den befintliga *IP-konfigurationen ipConfig-3:*

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Ange nätverkskortet med den nya IP-konfigurationen genom att ange följande kommando:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Visa de privata IP-adresserna och de offentliga IP-adressresurser som tilldelats nätverkskortet genom att ange följande kommando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Lägg till den privata IP-adressen i operativsystemet VM genom att slutföra stegen för operativsystemet i avsnittet [Lägg till IP-adresser i ett vm-operativsystem](#os-config) i den här artikeln. Lägg inte till den offentliga IP-adressen i operativsystemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]