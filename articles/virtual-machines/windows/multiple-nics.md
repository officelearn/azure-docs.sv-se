---
title: Skapa och hantera virtuella Windows-datorer i Azure som använder flera nätverkskort
description: Lär dig hur du skapar och hanterar en Windows-virtuell dator som har flera nätverkskort kopplade till sig med hjälp av Azure PowerShell- eller Resource Manager-mallar.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 6651ae21694022be86d8db08737c609aed3df569
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870259"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Skapa och hantera en virtuell Windows-dator med flera nätverkskort
Virtuella datorer (VMs) i Azure kan ha flera nätverkskort (NIC) kopplade till dem. Ett vanligt scenario är att ha olika undernät för front-end och backend-anslutning. Du kan associera flera nätverkskort på en virtuell dator till flera undernät, men dessa undernät måste alla finnas i samma virtuella nätverk (vNet). I den här artikeln beskrivs hur du skapar en virtuell dator som har flera nätverkskort kopplade till sig. Du lär dig också hur du lägger till eller tar bort nätverkskort från en befintlig virtuell dator. Olika [vm-storlekar](sizes.md) stöder ett varierande antal nätverkskort, så storlek på den virtuella datorn därefter.

## <a name="prerequisites"></a>Krav

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn inkluderar *myResourceGroup,* *myVnet*och *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Skapa en virtuell dator med flera nätverkskort
Skapa först en resursgrupp. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Skapa virtuella nätverk och undernät
Ett vanligt scenario är att ett virtuellt nätverk ska ha två eller flera undernät. Ett undernät kan vara för frontend-trafik, det andra för backend-trafik. Om du vill ansluta till båda undernäten använder du sedan flera nätverkskort på den virtuella datorn.

1. Definiera två virtuella nätverksundernät med [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). I följande exempel definieras undernäten för *mySubnetFrontEnd* och *mySubnetBackEnd:*

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Skapa ditt virtuella nätverk och undernät med [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet:*

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Skapa flera nätverkskort
Skapa två nätverkskort med [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Koppla ett nätverkskort till frontend-undernätet och ett nätverkskort i backend-undernätet. I följande exempel skapas nätverkskort med namnet *myNic1* och *myNic2:*

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Vanligtvis skapar du också en [nätverkssäkerhetsgrupp](../../virtual-network/security-overview.md) för att filtrera nätverkstrafik till den virtuella datorn och en [belastningsutjämnare](../../load-balancer/load-balancer-overview.md) för att distribuera trafik över flera virtuella datorer.

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn
Börja nu bygga din VM-konfiguration. Varje vm-storlek har en gräns för det totala antalet nätverkskort som du kan lägga till i en virtuell dator. Mer information finns i [Storlekarna för Windows VM](sizes.md).

1. Ställ in autentiseringsuppgifterna för den virtuella datorn på `$cred` följande sätt:

    ```powershell
    $cred = Get-Credential
    ```

2. Definiera din virtuella dator med [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). I följande exempel definieras en virtuell dator med namnet *myVM* och en vm-storlek som stöder mer än två nätverkskort (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Skapa resten av vm-konfigurationen med [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) och [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). I följande exempel skapas en virtuell dator med Windows Server 2016:

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Bifoga de två nätverkskort som du tidigare skapade med [Add-AzVMNetworkInterface:](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Skapa din virtuella dator med [New-AzVM:](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Lägg till vägar för sekundära nätverkskort i operativsystemet genom att slutföra stegen i [Konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Lägga till ett nätverkskort i en befintlig virtuell dator
Om du vill lägga till ett virtuellt nätverkskort till en befintlig virtuell dator, du frigöra den virtuella datorn, lägga till det virtuella nätverkskortet och sedan starta den virtuella datorn. Olika [vm-storlekar](sizes.md) stöder ett varierande antal nätverkskort, så storlek på den virtuella datorn därefter. Om det behövs kan du [ändra storlek på en virtuell dator](resize-vm.md).

1. Deallocate den virtuella datorn med [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). I följande exempel frigörs den virtuella datorn med namnet *myVM* i *myResourceGroup:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Hämta den befintliga konfigurationen av den virtuella datorn med [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). I följande exempel får du information om den virtuella datorn som heter *myVM* i *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. I följande exempel skapas ett virtuellt nätverkskort med [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) med namnet *myNic3* som är kopplat till *mySubnetBackEnd*. Det virtuella nätverkskortet kopplas sedan till den virtuella datorn som heter *myVM* i *myResourceGroup* med [Add-AzVMNetworkInterface:](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface)

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Primära virtuella nätverkskort
    En av nätverkskorten på en virtuell dator med flera NIC-enheter måste vara primär. Om en av de befintliga virtuella nätverkskorten på den virtuella datorn redan är inställd som primär kan du hoppa över det här steget. I följande exempel förutsätts att två virtuella nätverkskort nu finns på en`[0]`virtuell dator och du vill lägga till det första nätverkskortet ( ) som primärt:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Starta den virtuella datorn med [Start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Lägg till vägar för sekundära nätverkskort i operativsystemet genom att slutföra stegen i [Konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Ta bort ett nätverkskort från en befintlig virtuell dator
Om du vill ta bort ett virtuellt nätverkskort från en befintlig virtuell dator, du frigöra den virtuella datorn, ta bort det virtuella nätverkskortet och sedan starta den virtuella datorn.

1. Deallocate den virtuella datorn med [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). I följande exempel frigörs den virtuella datorn med namnet *myVM* i *myResourceGroup:*

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Hämta den befintliga konfigurationen av den virtuella datorn med [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). I följande exempel får du information om den virtuella datorn som heter *myVM* i *myResourceGroup:*

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Få information om nätverkskortet ta bort med [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). Följande exempel hämtar information om *myNic3:*

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Ta bort nätverkskortet med [Remove-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) och uppdatera sedan den virtuella datorn med [Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). I följande exempel tas *myNic3* bort enligt föregående `$nicId` steg:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Starta den virtuella datorn med [Start-AzVm:](https://docs.microsoft.com/powershell/module/az.compute/start-azvm)

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Skapa flera nätverkskort med mallar
Azure Resource Manager-mallar är ett sätt att skapa flera instanser av en resurs under distributionen, till exempel skapa flera nätverkskort. Resource Manager-mallar använder deklarativa JSON-filer för att definiera din miljö. Mer information finns i [översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md). Du kan använda *kopia* för att ange hur många instanser som ska skapas:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Mer information finns i [skapa flera instanser med hjälp av *kopiera*](../../resource-group-create-multiple.md). 

Du kan `copyIndex()` också använda för att lägga till ett nummer i ett resursnamn. Du kan sedan skapa *myNic1,* *MyNic2* och så vidare. Följande kod visar ett exempel på att lägga till indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa ett komplett exempel på [hur du skapar flera nätverkskort med hjälp av Resource Manager-mallar](../../virtual-network/template-samples.md).

Lägg till vägar för sekundära nätverkskort i operativsystemet genom att slutföra stegen i [Konfigurera operativsystemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gästoperativ för flera nätverkskort

Azure tilldelar en standardgateway till det första (primära) nätverksgränssnittet som är kopplat till den virtuella datorn. Azure tilldelar inte en standardgateway till ytterligare (sekundära) nätverksgränssnitt som är kopplade till en virtuell dator. Du kan därför som standard inte kommunicera med resurser utanför det undernät som är ett sekundärt nätverksgränssnitt befinner sig i. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät, även om stegen för att möjliggöra kommunikation är olika för olika operativsystem.

1. Från en Kommandotolk `route print` i Windows kör du kommandot, som returnerar utdata som liknar följande utdata för en virtuell dator med två anslutna nätverksgränssnitt:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    I det här exemplet är **Microsoft Hyper-V-nätverkskort #4** (gränssnitt 7) det sekundära nätverksgränssnittet som inte har tilldelats en standardgateway.

2. Kör `ipconfig` kommandot kommandot från en kommandotolk för att se vilken IP-adress som har tilldelats det sekundära nätverksgränssnittet. I det här exemplet tilldelas 192.168.2.4 till gränssnitt 7. Ingen standardgatewayadress returneras för det sekundära nätverksgränssnittet.

3. Om du vill dirigera all trafik som är avsedd för adresser utanför undernätet i det sekundära nätverksgränssnittet till gatewayen för undernätet kör du följande kommando:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Gateway-adressen för undernätet är den första IP-adressen (som slutar på 0,1) i det adressintervall som definierats för undernätet. Om du inte vill dirigera all trafik utanför undernätet kan du lägga till enskilda rutter till specifika mål i stället. Om du till exempel bara vill dirigera trafik från det sekundära nätverksgränssnittet till 192.168.3.0-nätverket anger du kommandot:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Om du till exempel vill bekräfta lyckad kommunikation med en resurs i nätverket 192.168.3.0 anger du följande kommando till ping 192.168.3.4 med gränssnitt 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Du kan behöva öppna ICMP via Windows-brandväggen för den enhet som du pingar med följande kommando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Om du vill bekräfta att den tillagda vägen finns i flödestabellen `route print` anger du kommandot som returnerar utdata som liknar följande text:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Rutten som visas med *192.168.1.1* under **Gateway**är den väg som finns där som standard för det primära nätverksgränssnittet. Rutten med *192.168.2.1* under **Gateway**är den väg du har lagt till.

## <a name="next-steps"></a>Nästa steg
Granska [Storlekarna för Windows VM](sizes.md) när du försöker skapa en virtuell dator med flera nätverkskort. Var uppmärksam på det maximala antalet nätverkskort som varje vm-storlek stöder. 


