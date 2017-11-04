---
title: "Skapa en användardefinierad väg väg trafik via en virtuell nätverksenhet - PowerShell | Microsoft Docs"
description: "Lär dig hur du skapar en användardefinierad väg för att åsidosätta standardvärdet för Azures routning av Routning av nätverkstrafik via en virtuell nätverksenhet."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 9696a74ac02688f9004156f6f16b39b37756751d
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-user-defined-route---powershell"></a>Skapa en användardefinierad väg - PowerShell

I den här kursen lär du dig hur du skapar användardefinierade vägar för att vidarebefordra trafik mellan två [virtuellt nätverk](virtual-networks-overview.md) undernät via en virtuell nätverksenhet. En virtuell nätverksenhet är en virtuell dator som kör ett nätverksprogram, till exempel en brandvägg. Mer information om förkonfigurerade virtuella nätverksenheter som du kan distribuera i Azure-nätverk finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

När du skapar undernät i ett virtuellt nätverk, Azure skapar standard [systemvägar](virtual-networks-udr-overview.md#system-routes) som gör att resurser i alla undernät för att kommunicera med varandra, enligt följande bild:

![Standardvägar](./media/create-user-defined-route/default-routes.png)

I de här självstudierna skapar du ett virtuellt nätverk med offentliga och privata DMZ undernät, som visas i bilden nedan. Vanligtvis webbservrar kan distribueras till en offentlig undernät och ett program eller en databasserver kan distribueras till ett privat undernät. Du skapar en virtuell dator ska fungera som en virtuell nätverksenhet i DMZ-undernät och du kan också skapa en virtuell dator i varje undernät som kommunicerar via nätverket virtuell installation. All trafik mellan de offentliga och privata undernäten dirigeras via installationen som visas i följande bild:

![Användardefinierade vägar](./media/create-user-defined-route/user-defined-routes.png)

Den här artikeln innehåller steg för att skapa en användardefinierad väg genom distributionsmodell hanteraren för filserverresurser, som är den distributionsmodell som vi rekommenderar att du använder när du skapar användardefinierade vägar. Om du behöver skapa en användardefinierad väg (klassisk), se [skapa en användardefinierad väg (klassisk)](virtual-network-create-udr-classic-ps.md). Om du inte är bekant med Azures distributionsmodeller [förstå Azure distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Mer information om användardefinierade vägar finns [användardefinierade vägar översikt](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Skapa vägar och virtuell nätverksenhet

Du kan installera och konfigurera den senaste versionen av PowerShell [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) modul på din dator eller klicka på den **prova** knappen i någon av att köra skript i Azure Cloud Shell-skript. Moln-gränssnittet har installerat PowerShell AzureRM modulen.
 
1. **Nödvändiga**: skapa ett virtuellt nätverk med två undernät genom att slutföra stegen i [skapa ett virtuellt nätverk](#create-a-virtual-network).
2. Om du kör PowerShell från datorn, logga in på Azure med din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) med hjälp av den `login-azurermaccount` kommando. Om du använder molntjänster Shell, loggas du automatiskt. Molnet Shell kanske måste startas om för att ändra från Bash-gränssnitt för att skapa det virtuella nätverket som krävs.
3. Ange några variabler som används i hela stegen:

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. Skapa en DMZ undernät i befintliga virtuella nätverk:

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. Skapa en statisk offentlig IP-adress för den virtuella datorn för virtuell installation av nätverket.

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. Skapa NVA virtuell dator. En NVA kan vara en virtuell dator som kör Linux eller Windows-operativsystem. Kopiera skriptet för antingen operativsystemet för att skapa den virtuella datorn, och klistra in den i PowerShell-sessionen. Om att skapa en virtuell dator i Windows, klistrar in skriptet i en textredigerare, ändra ”värde” för variabeln $cred och sedan klistra in den ändrade i PowerShell-sessionen:

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. Standard Azure vägar trafik mellan alla undernät i ett virtuellt nätverk. Skapa en väg om du vill ändra Azures standard routning så som trafik från den *offentliga* undernät dirigeras till en NVA i stället för direkt till den *privata* undernät.

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. Skapa en routingtabell för den *offentliga* undernät.

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. Associera routningstabellen till offentliga undernätet. Associera en routingtabell till ett undernät gör att Azure för att dirigera all utgående trafik från undernätet enligt vägar i routningstabellen. En routingtabell kan vara kopplad till noll eller flera undernät, ett undernät kan ha noll eller en vägtabell associerad till den.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. Skapa en väg för trafik från den *privata* undernät så att den *offentliga* undernätet via NVA virtuella datorn.

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. Skapa routningstabellen för den *privata* undernät.

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. Associera routningstabellen till den *privata* undernät.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **Valfritt:** skapa en virtuell dator i de offentliga och privata undernät och validera kommunikation mellan de virtuella datorerna dirigeras via virtuell nätverksenhet genom att slutföra stegen i [Validera routning](#validate-routing).
15. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-resources).

## <a name="validate-routing"></a>Validera Routning

1. Om du inte redan gjort slutföra stegen i [skapa vägar och virtuell nätverksenhet](#create-routes-and-network-virtual-appliance).
2. Klicka på den **prova** i rutan nedan, vilket öppnar Shell för Azure-molnet. Om du uppmanas att logga in till Azure med hjälp av din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud-gränssnittet är ett kostnadsfritt bash-gränssnitt med Azure-kommandoradsgränssnittet förinstallerat. 

    Följande skript skapar två virtuella datorer i den *offentliga* undernät och en i den *privata* undernät. Skripten även aktivera IP-vidarebefordring för nätverksgränssnittet i operativsystemet på en NVA att aktivera operativsystemet att dirigera trafik via nätverksgränssnittet. Produktion NVA kontrollerar vanligtvis trafiken innan vidarebefordring, men i den här självstudiekursen enkla NVA dirigerar bara trafik utan att kontrollera den. 

    Klicka på den **kopiera** knappen i den **Linux** eller **Windows** skript som följer och klistra in skriptinnehållet i i en textredigerare. Ändra lösenordet för den *adminPassword* variabel och sedan klistra in skriptet i Azure Cloud-gränssnittet. Kör skriptet för operativsystemet som du valde när du skapade virtuell nätverksenhet i steg 7 i [skapa vägar och virtuell nätverksenhet](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Validera kommunikation mellan de virtuella datorerna i de offentliga och privata undernät. 

    - Öppna en [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) eller [fjärrskrivbord](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows)-anslutning till den offentliga IP-adressen för den *myVm offentliga* virtuella datorn.
    - Från en kommandotolk på den *myVm offentliga* virtuell dator, ange `ping myVm-Private`. Du får svar eftersom en NVA dirigerar trafik från allmänheten privata undernät.
    - Från den *myVm offentliga* virtuell dator som kör en spårningsrutt mellan de virtuella datorerna i de offentliga och privata undernät. Ange rätt kommando som följer, beroende på vilket operativsystem du har installerat i de virtuella datorerna i de offentliga och privata undernät:
        - **Windows**: Kör från en kommandotolk i `tracert myvm-private` kommando.
        - **Ubuntu**: kör den `tracepath myvm-private` kommando.
      Trafik som passerar genom 10.0.2.4 (NVA) innan det nådde 10.0.1.4 (virtuell dator i privata undernät). 
    - Slutföra de föregående stegen genom att ansluta till den *myVm privata* virtuella datorn och pinga den *myVm offentliga* virtuella datorn. Spåra väg visar kommunikation reser via 10.0.2.4 innan det nådde 10.0.0.4 (virtuell dator i offentliga undernät).
    - **Du kan också**: använda nästa hopp-funktionen för Azure Nätverksbevakaren för att validera nästa hopp mellan två virtuella datorer i Azure. Innan du använder Nätverksbevakaren, måste du först [skapa en instans av Azure Nätverksbevakaren](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för den region som du vill använda den i. I den här kursen används region oss Öst. När du har aktiverat en Nätverksbevakaren-instans för regionen, ange följande kommando för att se nästa hopp-information mellan de virtuella datorerna i de offentliga och privata undernät:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       Returnerar resultatet *10.0.2.4* som den **nextHopIpAddress** och *VirtualAppliance* som den **nextHopType**.

> [!NOTE]
> För att illustrera begreppen i den här självstudiekursen offentliga IP-adresser tilldelas virtuella datorer i offentligt och privata undernät och alla nätverksåtkomst port är aktiverat i Azure för både virtuella datorer. När du skapar virtuella datorer för produktion, du kan inte tilldela offentliga IP-adresser till dem och filtrera nätverkstrafiken till undernätet för privata genom att distribuera en virtuell nätverksenhet framför det. eller genom att tilldela en nätverkssäkerhetsgrupp i undernät nätverksgränssnitt, eller båda. Läs mer om nätverkssäkerhetsgrupper i [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Den här kursen kräver ett befintligt virtuellt nätverk med två undernät. Klicka på den **prova** i rutan nedan för att snabbt skapa ett virtuellt nätverk. Klicka på den **prova** knappen öppnas den [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Även om molnet Shell Kör PowerShell eller ett Bash-gränssnitt i det här avsnittet, används Bash-gränssnitt för att skapa virtuella nätverk. Bash-gränssnitt har Azure-kommandoradsgränssnittet installerad. Om du uppmanas av molnet gränssnittet loggar du in på Azure med din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p). För att skapa det virtuella nätverket används i den här kursen, klickar du på den **kopiera** knappen i följande ruta och sedan klistra in skriptet i Azure Cloud Shell:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Läs mer om hur du använder portalen, PowerShell eller en Azure Resource Manager-mall för att skapa ett virtuellt nätverk i [skapa ett virtuellt nätverk](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Ta bort resurser

När du är klar med den här kursen kan du vill ta bort de resurser som du har skapat, så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen. I PowerShell, anger du följande kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

- Skapa en [högtillgänglig virtuell nätverksenhet](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverksenheter har ofta flera nätverksgränssnitt och IP-adresser som tilldelats. Lär dig hur du [lägga till nätverksgränssnitt i en befintlig virtuell dator](virtual-network-network-interface-vm.md#vm-add-nic) och [lägga till IP-adresser till en befintlig nätverksgränssnittet](virtual-network-network-interface-addresses.md#add-ip-addresses). Även om alla storlekar för virtuella datorer kan ha minst två nätverkskort som är kopplade till dem, stöder storlek på varje virtuell dator maximalt antal nätverksgränssnitt. Information om hur många nätverkskort varje virtuell dator har stöd för storleken finns [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) storlekar för virtuella datorer. 
- Skapa en användardefinierad väg för att tvinga tunnel trafik lokalt via en [plats-till-plats VPN-anslutning](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
