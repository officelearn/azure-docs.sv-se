---
title: Flera IP-adresser för virtuella datorer i Azure - PowerShell | Microsoft Docs
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med hjälp av PowerShell | Resource Manager.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
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
ms.openlocfilehash: bfc8f432e868c03d101ca7bfbc6b59f6e98ce29b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Tilldela flera IP-adresser till virtuella datorer med hjälp av PowerShell

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Den här artikeln förklaras hur du skapar en virtuell dator (VM) via Azure Resource Manager-distributionsmodellen med hjälp av PowerShell. Flera IP-adresser kan inte tilldelas till resurser som skapats via den klassiska distributionsmodellen. Om du vill veta mer om Azure distributionsmodeller kan läsa den [förstår distributionsmodellerna](../resource-manager-deployment-model.md) artikel.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

De steg som följer beskrivs hur du skapar ett exempel VM med flera IP-adresser, enligt beskrivningen i scenariot. Ändra variabelvärden som krävs för din implementering.

1. Öppna ett PowerShell-Kommandotolken och utföra stegen i det här avsnittet i en enda PowerShell-session. Om du inte redan har PowerShell installerats och konfigurerats kan du slutföra stegen i den [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) artikel.
2. Logga in på ditt konto med den `login-azurermaccount` kommando.
3. Ersätt *myResourceGroup* och *westus* med ett namn och plats. Skapa en resursgrupp. En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

    ```powershell
    $RgName   = "MyResourceGroup"
    $Location = "westus"

    New-AzureRmResourceGroup `
    -Name $RgName `
    -Location $Location
    ```

4. Skapa ett virtuellt nätverk (VNet) och undernät i samma plats som resursgruppen:

    ```powershell
    
    # Create a subnet configuration
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name MySubnet `
    -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $VNet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyVNet `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $subnetConfig

    # Get the subnet object
    $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
    ```

5. Skapa en nätverkssäkerhetsgrupp (NSG) och en regel. NSG: N skyddar den virtuella datorn med hjälp av regler för inkommande och utgående. I detta fall skapas en regel för inkommande trafik för port 3389, som tillåter inkommande anslutningar till fjärrskrivbord.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzureRmNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definiera primära IP-konfiguration för nätverkskortet. Ändra 10.0.0.4 till en giltig adress i undernätet som du skapade om du inte använde värdet som definierats tidigare. Innan du tilldelar en statisk IP-adress, rekommenderas det att du först bekräfta att den inte är redan används. Ange kommandot `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Om adressen är tillgänglig, returnerar utdata *SANT*. Om den inte är tillgänglig, returnerar utdata *FALSKT* och en lista med adresser som är tillgängliga. 

    I följande kommandon och **Ersätt < Ersätt-med-your-unikt-name > med unikt DNS-namn.** Namnet måste vara unikt inom alla offentliga IP-adresser inom en Azure-region. Det här är en valfri parameter. Det kan tas bort om du vill ansluta till den virtuella datorn med hjälp av den offentliga IP-adressen.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    När du tilldelar en NIC flera IP-konfigurationer, det måste tilldelas en konfiguration som den *-primära*.

    > [!NOTE]
    > Offentliga IP-adresser har en låg kostnad. Mer information om priser för IP-adress i [IP-adress priser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).

7. Definiera de sekundära IP-konfigurationerna för nätverkskortet. Du kan lägga till eller ta bort konfigurationer efter behov. Varje IP-adresskonfigurationen måste ha en privat IP-adress. Varje konfiguration kan du ha en offentlig IP-adress.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Skapa NIC och koppla tre IP-konfigurationer till den:

    ```powershell
    
    $NIC = New-AzureRmNetworkInterface `
    -Name MyNIC `
    -ResourceGroupName $RgName `
    -Location $Location `
    -NetworkSecurityGroupId $NSG.Id `
    -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```

    >[!NOTE]
    >Även om alla konfigurationer har tilldelats ett nätverkskort i den här artikeln, kan du tilldela flera IP-konfigurationer för varje nätverkskort som är anslutet till den virtuella datorn. Information om hur du skapar en virtuell dator med flera nätverkskort, läsa den [skapa en virtuell dator med flera nätverkskort](../virtual-machines/windows/multiple-nics.md) artikel.

9. Skapa den virtuella datorn genom att ange följande kommandon:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a sername and password for the VM you're reating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzureRmVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzureRmVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Lägga till privata IP-adresser i Virtuella operativsystem genom att slutföra stegen för operativsystemet i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till de offentliga IP-adresserna till operativsystemet.

## <a name="add"></a>Lägg till IP-adresser till en virtuell dator

Du kan lägga till privata och offentliga IP-adresser till Azure nätverksgränssnittet genom att slutföra de steg som följer. Exemplen i avsnitten nedan förutsätter att du redan har en virtuell dator med de tre IP-konfigurationerna som beskrivs i den [scenariot](#Scenario) i den här artikeln, men det är inte nödvändigt att du gör.

1. Öppna ett PowerShell-Kommandotolken och utföra stegen i det här avsnittet i en enda PowerShell-session. Om du inte redan har PowerShell installerats och konfigurerats kan du slutföra stegen i den [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) artikel.
2. Ändra följande $Variables ”värdena” till namnet på nätverkskortet som du vill lägga till IP-adressen och den resursgrupp och plats nätverkskortet finns i:

    ```powershell
    $NicName  = "MyNIC"
    $RgName   = "MyResourceGroup"
    $Location = "westus"
    ```

    Om du inte vet namnet på nätverkskortet som du vill ändra, ange följande kommandon, ändra värdena för variabler som tidigare:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. Skapa en variabel och ange den till det befintliga nätverkskortet genom att skriva följande kommando:

    ```powershell
    $MyNIC = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName
    ```
4. I följande kommandon och ändra *MyVNet* och *MySubnet* till namnen på VNet och undernät som nätverkskortet är anslutet till. Ange kommandon för att hämta objekt VNet och undernät som nätverkskortet är anslutet till:

    ```powershell
    $MyVNet = Get-AzureRMVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
    $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
    ```
    Om du inte vet namnet på VNet eller undernät nätverkskortet är anslutet till, skriver du följande kommando:
    ```powershell
    $MyNIC.IpConfigurations
    ```
    Leta efter texten som liknar följande exempel utdata i utdata:
    
    ```
    "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
    ```
    I det här resultatet *MyVnet* är VNet och *MySubnet* är nätverkskortet är anslutet till undernätet.

5. Utför stegen i följande avsnitt, baserat på dina krav:

    **Lägg till en privat IP-adress**

    Om du vill lägga till en privat IP-adress till ett nätverkskort måste du skapa en IP-konfiguration. Följande kommando skapar en konfiguration med en statisk IP-adress 10.0.0.7. När du anger en statisk IP-adress, måste den vara en oanvända adress för undernätet. Vi rekommenderar att du först testar adress för att säkerställa att den är tillgänglig genom att ange den `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` kommando. Om IP-adress är tillgänglig utdata returnerar *SANT*. Om den inte är tillgänglig, returnerar utdata *FALSKT*, och en lista med adresser som är tillgängliga.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    Skapa så många konfigurationer som du vill använda unika konfigurationsnamn och privata IP-adresser (för konfigurationer med statiska IP-adresser).

    Lägga till den privata IP-adressen i VM-operativsystem genom att slutföra stegen för operativsystemet i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln.

    **Lägg till en offentlig IP-adress**

    En offentlig IP-adress har lagts till genom att associera en offentlig IP-adressresurs till en ny IP-konfiguration eller en befintlig IP-konfiguration. Slutför stegen i något av avsnitten som följer, som du behöver.

    > [!NOTE]
    > Offentliga IP-adresser har en låg kostnad. Mer information om priser för IP-adress i [IP-adress priser](https://azure.microsoft.com/pricing/details/ip-addresses) sidan. Det finns en gräns för antalet offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits).
    >

    - **Koppla den offentliga IP-adressresursen till en ny IP-konfiguration**
    
        När du lägger till en offentlig IP-adress i en ny IP-konfiguration måste du också lägga en privat IP-adress, eftersom alla IP-konfigurationer måste ha en privat IP-adress. Du kan lägga till en befintlig offentlig IP-adressresurs eller skapa en ny. Ange följande kommando för att skapa en ny:
    
        ```powershell
        $myPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "myPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location `
        -AllocationMethod Static
        ```

        Att skapa en ny IP-konfiguration med en statisk privat IP-adress och den associerade *myPublicIp3* offentlig IP adress resursen måste du ange följande kommando:

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig `
        -Name IPConfig-4 `
        -NetworkInterface $myNIC `
        -Subnet $Subnet `
        -PrivateIpAddress 10.0.0.7 `
        -PublicIpAddress $myPublicIp3
        ```

    - **Koppla den offentliga IP-adressresursen till en befintlig IP-konfiguration**

        En offentlig IP-adressresurs kan bara vara kopplad till en IP-konfiguration som inte redan har en associerad. Du kan avgöra om en IP-konfiguration har en tillhörande offentliga IP-adress genom att ange följande kommando:

        ```powershell
        $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
        ```

        Du kan se utdata som liknar följande:

        ```     
        Name       PrivateIpAddress PublicIpAddress                                           Primary
        
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False
        ```

        Eftersom den **PublicIpAddress** för *IpConfig-3* är tomt, inga offentliga IP-adressresurs är för närvarande associerad till den. Du kan lägga till en befintlig offentlig IP-adressresurs IpConfig-3 eller ange följande kommando för att skapa en:

        ```powershell
        $MyPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "MyPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location -AllocationMethod Static
        ```

        Ange följande kommando för att associera den offentliga IP-adressresursen till befintliga IP-konfigurationen med namnet *IpConfig-3*:
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig `
        -Name IpConfig-3 `
        -NetworkInterface $mynic `
        -Subnet $Subnet `
        -PublicIpAddress $myPublicIp3
        ```

6. Ange nätverkskort med den nya IP-konfigurationen genom att ange följande kommando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $MyNIC
    ```

7. Visa de privata IP-adresserna och de offentliga IP-adressresurser som tilldelats till nätverkskortet genom att ange följande kommando:

    ```powershell   
    $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. Lägga till den privata IP-adressen i VM-operativsystem genom att slutföra stegen för operativsystemet i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till den offentliga IP-adressen för operativsystemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
