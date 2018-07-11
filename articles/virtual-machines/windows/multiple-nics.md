---
title: Skapa och hantera Windows virtuella datorer i Azure som använder flera nätverkskort | Microsoft Docs
description: Lär dig hur du skapar och hanterar en Windows VM som har flera nätverkskort som är kopplade till den med hjälp av Azure PowerShell eller Resource Manager-mallar.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: d29676b107885350785ceb1c17eb3010cc0907d2
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928353"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Skapa och hantera en virtuell Windows-dator som har flera nätverkskort
Virtuella datorer (VM) i Azure kan ha flera virtuella nätverkskort (NIC) kopplade till sig. Ett vanligt scenario är att ha olika undernät för frontend och backend-anslutning eller ett nätverk som är dedikerad till en lösning för övervakning eller säkerhetskopiering. Den här artikeln beskriver hur du skapar en virtuell dator som har flera nätverkskort som är kopplade till den. Du också lära dig hur du lägger till eller ta bort nätverkskort från en befintlig virtuell dator. Olika [VM-storlekar](sizes.md) stöd för olika antal nätverkskort, storlek, så den virtuella datorn i enlighet med detta.

## <a name="prerequisites"></a>Förutsättningar
Se till att du har den [senaste Azure PowerShell-versionen installeras och konfigureras](/powershell/azure/overview).

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar *myResourceGroup*, *myVnet*, och *myVM*.


## <a name="create-a-vm-with-multiple-nics"></a>Skapa en virtuell dator med flera nätverkskort
Skapa först en resursgrupp. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *EastUs* plats:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Skapa virtuellt nätverk och undernät
Ett vanligt scenario är för ett virtuellt nätverk har två eller flera undernät. Ett undernät kan vara för frontend-trafik, en för backend-trafik. Om du vill ansluta till båda undernäten måste använda du sedan flera nätverkskort på den virtuella datorn.

1. Definiera två undernät för virtuella nätverk med [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Följande exempel definierar undernäten för *mySubnetFrontEnd* och *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Skapa ditt virtuella nätverk och undernät med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet*:

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Skapa flera nätverkskort
Skapa två nätverkskort med [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Ansluta ett nätverkskort till klientdelsundernätet och ett nätverkskort till backend-undernät. I följande exempel skapas nätverkskort med namnet *myNic1* och *myNic2*:

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

Vanligtvis kan du också skapa en [nätverkssäkerhetsgrupp](../../virtual-network/security-overview.md) att filtrera nätverkstrafik till den virtuella datorn och en [belastningsutjämnare](../../load-balancer/load-balancer-overview.md) att distribuera trafik över flera virtuella datorer.

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn
Nu börja skapa din VM-konfiguration. Varje VM-storlek har en gräns för det totala antalet nätverkskort som du kan lägga till en virtuell dator. Mer information finns i [Windows VM-storlekar](sizes.md).

1. Ange dina autentiseringsuppgifter för virtuell dator den `$cred` variabeln enligt följande:

    ```powershell
    $cred = Get-Credential
    ```

2. Definiera den virtuella datorn med [nya AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Följande exempel definierar en virtuell dator med namnet *myVM* och använder en VM-storlek som har stöd för fler än två nätverkskort (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Skapa resten av VM-konfiguration med [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) och [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). I följande exempel skapas en virtuell Windows Server 2016-dator:

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

4. Ansluta de två nätverkskort som du skapade tidigare med [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Lägg till vägar för sekundära nätverkskort i operativsystem genom att följa stegen i [konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Lägg till ett nätverkskort i en befintlig virtuell dator
Om du vill lägga till ett virtuellt nätverkskort i en befintlig virtuell dator du frigör den virtuella datorn, Lägg till det virtuella nätverkskortet och sedan starta den virtuella datorn. Olika [VM-storlekar](sizes.md) stöd för olika antal nätverkskort, storlek, så den virtuella datorn i enlighet med detta. Om det behövs kan du [ändra storlek på en virtuell dator](resize-vm.md).

1. Frigör den virtuella datorn med [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). I följande exempel bort den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Hämta den befintliga konfigurationen av den virtuella datorn med [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). I följande exempel hämtar information för den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. I följande exempel skapas ett virtuellt nätverkskort med [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) med namnet *myNic3* som är kopplad till *mySubnetBackEnd*. Det virtuella nätverkskortet kopplas sedan till den virtuella datorn med namnet *myVM* i *myResourceGroup* med [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

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
    Ett nätverkskort på en virtuell multi-NIC-dator måste vara primära. Om en av de befintliga virtuella nätverkskorten på den virtuella datorn har redan angetts som primär, kan du hoppa över det här steget. I följande exempel förutsätter att det finns nu två virtuella nätverkskort på en virtuell dator och du vill lägga till det första nätverkskortet (`[0]`) som primär:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Starta den virtuella datorn med [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Lägg till vägar för sekundära nätverkskort i operativsystem genom att följa stegen i [konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Ta bort ett nätverkskort från en befintlig virtuell dator
Om du vill ta bort ett virtuellt nätverkskort från en befintlig virtuell dator måste du frigör den virtuella datorn, ta bort det virtuella nätverkskortet och starta den virtuella datorn.

1. Frigör den virtuella datorn med [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). I följande exempel bort den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Hämta den befintliga konfigurationen av den virtuella datorn med [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). I följande exempel hämtar information för den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Få information om NIC remove med [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). I följande exempel hämtar information *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Ta bort nätverkskort med [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) och sedan uppdatera den virtuella datorn med [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). I följande exempel tar bort *myNic3* som erhålls av `$nicId` i föregående steg:

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Starta den virtuella datorn med [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Skapa flera nätverkskort med mallar
Azure Resource Manager-mallar är ett sätt att skapa flera instanser av en resurs under distributionen, till exempel skapa flera nätverkskort. Resource Manager-mallar använda deklarativa JSON-filer för att definiera din miljö. Mer information finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Du kan använda *kopia* kan du ange antalet instanser för att skapa:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Mer information finns i [skapa flera instanser med *kopia*](../../resource-group-create-multiple.md). 

Du kan också använda `copyIndex()` att lägga till ett tal till ett resursnamn. Du kan sedan skapa *myNic1*, *MyNic2* och så vidare. Följande kod visar ett exempel på att lägga till indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa en komplett exempel på [skapar flera nätverkskort med hjälp av Resource Manager-mallar](../../virtual-network/template-samples.md).

Lägg till vägar för sekundära nätverkskort i operativsystem genom att följa stegen i [konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gästoperativsystem för flera nätverkskort

Azure tilldelar en standard-gateway till den första (primära) nätverksgränssnitt kopplat till den virtuella datorn. Azure tilldelar inte en standardgateway till ytterligare (sekundära) nätverksgränssnitt som är kopplade till en virtuell dator. Du kan därför som standard inte kommunicera med resurser utanför det undernät som är ett sekundärt nätverksgränssnitt befinner sig i. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät, även om hur du aktiverar kommunikation är olika för olika operativsystem.

1. Från en Windows-kommandotolk, kör den `route print` kommandot, som returnerar utdata som liknar följande utdata för en virtuell dator med två anslutna nätverksgränssnitt:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    I det här exemplet **Microsoft Hyper-V Network Adapter 4** (7-gränssnitt) är den sekundära nätverksgränssnitt som inte har en standard-gateway som är tilldelade till den.

2. Från Kommandotolken kör du den `ipconfig` kommando för att se vilken IP-adress tilldelas till det sekundära nätverksgränssnittet. I det här exemplet tilldelas 192.168.2.4 gränssnittet 7. Inga standardgatewayadressen returneras för det sekundära nätverksgränssnittet.

3. Kör följande kommando för att dirigera all trafik till adresser utanför undernätet för det sekundära nätverksgränssnittet till gatewayen för undernätet:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Gateway-adressen för undernätet är den första IP-adressen (som slutar på.1) i det adressintervall som definierats för undernätet. Om du inte vill att dirigera all trafik utanför undernätet, kan du lägga till enskilda vägar till specifika mål i stället. Exempel: Om du bara vill dirigera trafik från det sekundära nätverksgränssnittet till 192.168.3.0 nätverk, du har angivit kommandot:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. För att bekräfta lyckad kommunikation med en resurs på 192.168.3.0 Ange nätverk, till exempel följande kommando för att pinga 192.168.3.4 gränssnittet 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Du kan behöva öppna ICMP via Windows-brandväggen på den enhet som du pinga med följande kommando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Om du vill kontrollera vägen har lagts till i routningstabellen, ange den `route print` kommandot, som returnerar utdata som liknar följande text:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Vägen som visas i listan med *192.168.1.1* under **Gateway**, är den väg som det är som standard för det primära nätverksgränssnittet. Vägen med *192.168.2.1* under **Gateway**, finns den väg som du har lagt till.

## <a name="next-steps"></a>Nästa steg
Granska [Windows VM-storlekar](sizes.md) när du försöker skapa en virtuell dator som har flera nätverkskort. Ta hänsyn till det maximala antalet nätverkskort som har stöd för varje VM-storlek. 


