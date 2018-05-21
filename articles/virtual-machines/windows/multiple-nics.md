---
title: Skapa och hantera virtuella Windows-datorer i Azure som använder flera nätverkskort | Microsoft Docs
description: Lär dig mer om att skapa och hantera en virtuell Windows-dator som har flera nätverkskort som är kopplad till den med hjälp av Azure PowerShell eller Resource Manager-mallar.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 776ae83990a7799102c69347196a72a68561ee6b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Skapa och hantera en virtuell Windows-dator som har flera nätverkskort
Virtuella datorer (VM) i Azure kan ha flera virtuella nätverkskort (NIC) kopplad. Ett vanligt scenario är att ha olika undernät för frontend och backend-anslutning eller ett nätverk som är dedikerad för en lösning för övervakning eller säkerhetskopiering. Den här artikeln beskrivs hur du skapar en virtuell dator som har flera nätverkskort som är kopplade till den. Du också lära dig hur du lägger till eller ta bort nätverkskort från en befintlig virtuell dator. Olika [VM-storlekar](sizes.md) stöder olika antal nätverkskort, så därför storlek den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar
Se till att du har den [senaste Azure PowerShell-versionen installerad och konfigurerad](/powershell/azure/overview).

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar *myResourceGroup*, *myVnet*, och *myVM*.


## <a name="create-a-vm-with-multiple-nics"></a>Skapa en virtuell dator med flera nätverkskort
Först skapa en resursgrupp. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *EastUs* plats:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Skapa virtuella nätverk och undernät
Ett vanligt scenario är för ett virtuellt nätverk har två eller flera undernät. Ett undernät kan vara för frontend-trafik, en för backend-trafik. För att ansluta till både undernät måste använda du flera nätverkskort på den virtuella datorn.

1. Definiera två undernät för virtuellt nätverk med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). I följande exempel definieras undernät för *mySubnetFrontEnd* och *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Skapa virtuella nätverk och undernät med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet*:

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Skapa flera nätverkskort
Skapa två nätverkskort med [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Ansluta ett nätverkskort frontend-undernät och ett nätverkskort till backend-undernät. I följande exempel skapas nätverkskort med namnet *myNic1* och *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Vanligtvis du också skapa en [nätverkssäkerhetsgruppen](../../virtual-network/security-overview.md) att filtrera nätverkstrafik till den virtuella datorn och en [belastningsutjämnare](../../load-balancer/load-balancer-overview.md) distribuerar trafik över flera virtuella datorer.

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn
Starta nu att skapa VM-konfiguration. Varje VM-storlek har en gräns för det totala antalet nätverkskort som du kan lägga till en virtuell dator. Mer information finns i [Windows VM-storlekar](sizes.md).

1. Ange autentiseringsuppgifterna för ditt VM den `$cred` variabeln på följande sätt:

    ```powershell
    $cred = Get-Credential
    ```

2. Definiera den virtuella datorn med [nya AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). I följande exempel definieras en virtuell dator med namnet *myVM* och använder en VM-storlek som har stöd för fler än två nätverkskort (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Skapa resten av VM-konfiguration med [Set AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) och [Set AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). I följande exempel skapas en Windows Server 2016 VM:

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Koppla de två nätverkskorten som du skapade tidigare med [Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Skapa den virtuella datorn med [nya AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Lägga till vägar för sekundära nätverkskort operativsystem genom att slutföra stegen i [konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Lägga till ett nätverkskort till en befintlig virtuell dator
Om du vill lägga till ett virtuellt nätverkskort till en befintlig virtuell dator måste du frigöra den virtuella datorn, Lägg till det virtuella nätverkskortet och sedan starta den virtuella datorn. Olika [VM-storlekar](sizes.md) stöder olika antal nätverkskort, så därför storlek den virtuella datorn. Om det behövs kan du [ändra storlek på en virtuell dator](resize-vm.md).

1. Frigör den virtuella datorn med [stoppa AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). I följande exempel tar bort den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Hämta den befintliga konfigurationen av den virtuella datorn med [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). I följande exempel hämtar information för den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. I följande exempel skapas ett virtuellt nätverkskort med [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) med namnet *myNic3* som är kopplad till *mySubnetBackEnd*. Det virtuella nätverkskortet kopplas sedan till den virtuella datorn med namnet *myVM* i *myResourceGroup* med [Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Primära virtuella nätverkskort
    Ett nätverkskort på en VM med flera nätverkskort måste vara primär. Om något av de befintliga virtuella nätverkskorten på den virtuella datorn redan har angetts som primärt, kan du hoppa över det här steget. I följande exempel förutsätter att det finns nu två virtuella nätverkskort på en virtuell dator och du vill lägga till det första nätverkskortet (`[0]`) som den primära servern:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Starta den virtuella datorn med [Start AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Lägga till vägar för sekundära nätverkskort operativsystem genom att slutföra stegen i [konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Ta bort ett nätverkskort från en befintlig virtuell dator
Om du vill ta bort ett virtuellt nätverkskort från en befintlig virtuell dator du frigör den virtuella datorn, ta bort det virtuella nätverkskortet och starta den virtuella datorn.

1. Frigör den virtuella datorn med [stoppa AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). I följande exempel tar bort den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Hämta den befintliga konfigurationen av den virtuella datorn med [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). I följande exempel hämtar information för den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Hämta information om NIC-remove med [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). I följande exempel hämtar information *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Ta bort nätverkskortet med [ta bort AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) och uppdatera den virtuella datorn med [uppdatering AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). I följande exempel tar bort *myNic3* som erhålls av `$nicId` i föregående steg:

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Starta den virtuella datorn med [Start AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Skapa flera nätverkskort med mallar
Mallar för Azure Resource Manager tillhandahåller ett sätt att skapa flera instanser av en resurs under distributionen, till exempel skapa flera nätverkskort. Resource Manager-mallar använda deklarativa JSON-filer för att definiera din miljö. Mer information finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Du kan använda *kopiera* kan du ange antalet instanser som du vill skapa:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Mer information finns i [skapa flera instanser med *kopiera*](../../resource-group-create-multiple.md). 

Du kan också använda `copyIndex()` att lägga till ett tal till ett resursnamn. Du kan sedan skapa *myNic1*, *MyNic2* och så vidare. Följande kod visar ett exempel på att lägga till indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa en komplett exempel på [skapa flera nätverkskort med Resource Manager-mallar](../../virtual-network/template-samples.md).

Lägga till vägar för sekundära nätverkskort operativsystem genom att slutföra stegen i [konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gästoperativsystemet för flera nätverkskort

Azure tilldelar en standard-gateway till det första (primära) nätverksgränssnittet kopplade till den virtuella datorn. Azure tilldelar inte en standardgateway till ytterligare (sekundära) nätverksgränssnitt som är kopplade till en virtuell dator. Du kan därför som standard inte kommunicera med resurser utanför det undernät som är ett sekundärt nätverksgränssnitt befinner sig i. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät, även om stegen för att aktivera kommunikation är olika för olika operativsystem.

1. Kör från en kommandotolk i `route print` kommando som returnerar utdata som liknar följande utdata för en virtuell dator med två anslutna nätverksgränssnitten:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    I det här exemplet **Microsoft Hyper-V Network Adapter #4** (gränssnitt 7) är sekundärt nätverksgränssnitt som inte har en standard-gateway som har tilldelats.

2. Kör från en kommandotolk i `ipconfig` kommandot för att se vilken IP-adress har tilldelats det sekundära nätverksgränssnittet. I det här exemplet tilldelas 192.168.2.4 gränssnittet 7. Ingen standard gateway-adress returneras för det sekundära nätverksgränssnittet.

3. Kör följande kommando för att vidarebefordra all trafik för adresser utanför undernätet i sekundära nätverksgränssnitt och gateway för undernätet:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Gateway-adressen för undernätet är den första IP-adress (som slutar i.1) i adressintervall som definierats för undernätet. Om du inte vill att vidarebefordra all trafik utanför undernätet, kan du lägga till enskilda vägar till specifika mål i stället. Om du vill dirigera trafik från det sekundära nätverksgränssnittet till 192.168.3.0 till exempel nätverk, du anger kommandot:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. För att bekräfta lyckade kommunikation med en resurs på 192.168.3.0 nätverk, exempelvis ange följande kommando för att pinga 192.168.3.4 gränssnittet 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Du kan behöva öppna ICMP via Windows-brandväggen på den enhet som du pinga med följande kommando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. För att bekräfta tillagda vägen är i routningstabellen, ange den `route print` kommando som returnerar utdata som liknar följande:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Vägen som visas i listan med *192.168.1.1* under **Gateway**, är det flöde som är det som standard för det primära nätverksgränssnittet. Vägen med *192.168.2.1* under **Gateway**, är flödet som du har lagt till.

## <a name="next-steps"></a>Nästa steg
Granska [Windows VM-storlekar](sizes.md) när du försöker skapa en virtuell dator som har flera nätverkskort. Ta hänsyn till det maximala antalet nätverkskort som har stöd för varje VM-storlek. 


