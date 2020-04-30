---
title: Skapa en virtuell Azure-dator med accelererat nätverk – Azure PowerShell
description: Lär dig hur du skapar en virtuell Linux-dator med accelererat nätverk.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 3807c1434e3758eafe299da7b30769b41d3ede87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106312"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Skapa en virtuell Windows-dator med accelererat nätverk med Azure PowerShell

I den här självstudien får du lära dig hur du skapar en virtuell Windows-dator (VM) med accelererat nätverk. Information om hur du skapar en virtuell Linux-dator med accelererat nätverk finns i [skapa en virtuell Linux-dator med accelererat nätverk](create-vm-accelerated-networking-cli.md). Accelererat nätverk möjliggör SR-IOV (Single root I/O Virtualization) till en virtuell dator, vilket avsevärt förbättrar nätverkets prestanda. Den här högpresterande sökvägen kringgår värden från Datapath, minskar svars tiden, skakningarna och CPU-användningen, för användning med de mest krävande nätverks belastningarna på VM-typer som stöds. Följande bild visar kommunikationen mellan två virtuella datorer med och utan accelererat nätverk:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Utan accelererat nätverk måste all nätverks trafik i och från den virtuella datorn passera värden och den virtuella växeln. Den virtuella växeln tillhandahåller all princip tillämpning, till exempel nätverks säkerhets grupper, åtkomst kontrol listor, isolering och andra virtualiserade nätverks tjänster till nätverks trafik. Läs mer om virtuella växlar i [Hyper-V-nätverksvirtualisering och virtuell växel](https://technet.microsoft.com/library/jj945275.aspx).

Med accelererat nätverk anländer nätverks trafiken till den virtuella datorns nätverks gränssnitt (NIC) och vidarebefordras sedan till den virtuella datorn. Alla nätverks principer som den virtuella växeln gäller avlastas nu och används i maskin vara. Genom att tillämpa principen i maskin vara kan NÄTVERKSKORTet vidarebefordra nätverks trafik direkt till den virtuella datorn, kringgå värden och den virtuella växeln, samtidigt som all princip som används i värden upprätthålls.

Fördelarna med accelererade nätverk gäller endast den virtuella dator som den är aktive rad på. För bästa resultat är det idealiskt att aktivera den här funktionen på minst två virtuella datorer som är anslutna till samma Azure-Virtual Network (VNet). När du kommunicerar via virtuella nätverk eller ansluter lokalt har den här funktionen minimal påverkan på den totala svars tiden.

## <a name="benefits"></a>Fördelar
* **Lägre latens/högre paket per sekund (PPS):** Att ta bort den virtuella växeln från Datapath tar bort de tid paket som ägnas åt värden för princip bearbetning och ökar antalet paket som kan bearbetas inuti den virtuella datorn.
* **Reducerade Darr:** Bearbetning av virtuella växlar beror på den mängd princip som måste tillämpas och arbets belastningen för den processor som utför bearbetningen. Genom att avlasta den tvingande principen till maskin varan tar du bort den variabiliteten genom att leverera paket direkt till den virtuella datorn, ta bort värden till kommunikation mellan virtuella datorer och alla program avbrott och kontext byten.
* **Minskad processor användning:** Om den virtuella växeln kringgås i värden går det snabbare att bearbeta nätverks trafiken.

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds i rutan från Azure-galleriet:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Virtuella dator instanser som stöds
Accelererat nätverk stöds i de flesta generella syftes-och beräknings optimerade instans storlekar med 2 eller fler virtuella processorer.  Dessa serier som stöds är: D/DSv2 och F/FS

På instanser som stöder hyperthreading stöds accelererat nätverk på VM-instanser med 4 eller fler virtuella processorer. Serien som stöds är: D/Dsv3, D/Dsv4, E/Esv3, EA/Easv4, Fsv2, Lsv2, MS/MMS och MS/Mmsv2.

Mer information om VM-instanser finns i [storlekar för virtuella Windows-datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regioner
Tillgängligt i alla offentliga Azure-regioner och Azure Government molnet.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivera accelererat nätverk på en virtuell dator som körs
En virtuell dator storlek som stöds utan accelererat nätverk kan bara aktivera funktionen när den stoppas och frigörs.

### <a name="deployment-through-azure-resource-manager"></a>Distribution via Azure Resource Manager
Virtuella datorer (klassisk) kan inte distribueras med accelererat nätverk.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Skapa en virtuell Windows-dator med Azure-accelererat nätverk
## <a name="portal-creation"></a>Skapa Portal
Även om den här artikeln innehåller steg för att skapa en virtuell dator med accelererat nätverk med Azure PowerShell, kan du också [skapa en virtuell dator med accelererat nätverk med hjälp av Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du skapar en virtuell dator i portalen väljer du fliken **nätverk** i bladet **skapa en virtuell dator** .  På den här fliken finns ett alternativ för **accelererat nätverk**.  Om du har valt ett [operativ system som stöds](#supported-operating-systems) och storleken på den [virtuella datorn](#supported-vm-instances), fylls det här alternativet i automatiskt på "på".  Om inte, kommer den att fylla i alternativet "av" för accelererat nätverk och ge användaren en anledning till varför det inte är aktiverat.   
* *Obs:* Endast operativ system som stöds kan aktive ras via portalen.  Om du använder en anpassad avbildning och din avbildning har stöd för accelererat nätverk skapar du en virtuell dator med CLI eller PowerShell. 

När den virtuella datorn har skapats kan du bekräfta att accelererat nätverk har Aktiver ATS genom att följa anvisningarna i kontrol lera att accelererat nätverk är aktiverat.

## <a name="powershell-creation"></a>PowerShell-skapande
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installera [Azure PowerShell](/powershell/azure/install-az-ps) version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az`för att hitta den version som är installerad. Om du behöver installera eller uppgradera installerar du den senaste versionen av AZ-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az). Logga in på ett Azure-konto med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)i en PowerShell-session.

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn som ingår *myResourceGroup*, *myNic*och *myVM*.

Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för den *centrala* platsen:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Börja med att skapa en under näts konfiguration med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). I följande exempel skapas ett undernät med namnet *subsubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Skapa ett virtuellt nätverk med [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), *med under nätet undernät.*

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa först en regel för nätverks säkerhets grupp med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Skapa en nätverks säkerhets grupp med [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) och tilldela den *tillåtelse-RDP-all* säkerhets regeln. Förutom regeln *Allow-RDP-all* , innehåller nätverks säkerhets gruppen flera standard regler. En standard regel inaktiverar all inkommande åtkomst från Internet, vilket är anledningen till att regeln *Allow-RDP-all* tilldelas nätverks säkerhets gruppen så att du kan fjärrans luta till den virtuella datorn när den har skapats.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Koppla nätverks säkerhets gruppen *till under nätet under nät* med [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Regeln i nätverks säkerhets gruppen är effektiv för alla resurser som distribueras i under nätet.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverks gränssnitt med accelererat nätverk
Skapa en offentlig IP-adress med hjälp av [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). En offentlig IP-adress krävs inte om du inte planerar åtkomst till den virtuella datorn från Internet, men du måste utföra stegen i den här artikeln.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Skapa ett nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) med accelererat nätverk aktiverat och tilldela den offentliga IP-adressen till nätverks gränssnittet. I följande exempel skapas ett nätverks gränssnitt med namnet *myNic* i *under nätet undernät för* det virtuella *myVnet* -nätverket och tilldelar den *myPublicIp* offentliga IP-adressen:

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Ange autentiseringsuppgifterna för din virtuella dator `$cred` till variabeln med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Definiera först den virtuella datorn med [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). I följande exempel definieras en virtuell dator med namnet *myVM* med en VM-storlek som har stöd för accelererat nätverk (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

En lista över alla storlekar och egenskaper för virtuella datorer finns i [storlekar för virtuella Windows-datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Skapa resten av din VM-konfiguration med [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) och [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). I följande exempel skapas en virtuell Windows Server 2016-dator:

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

Bifoga nätverks gränssnittet som du skapade tidigare med [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Skapa slutligen en virtuell dator med [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Bekräfta att driv rutinen är installerad i operativ systemet

När du har skapat den virtuella datorn i Azure ansluter du till den virtuella datorn och kontrollerar att driv rutinen är installerad i Windows.

1. Öppna Azure [Portal](https://portal.azure.com) i en webbläsare och logga in med ditt Azure-konto.
2. Skriv *myVm*i rutan som innehåller text *Sök resurserna* överst i Azure Portal. När **myVm** visas i Sök resultaten klickar du på det. Om du **skapar** visas under knappen **Anslut** har Azure inte skapat den virtuella datorn ännu. Klicka på **Anslut** i det övre vänstra hörnet av översikten när du inte längre ser att du har **skapat** under knappen **Anslut** .
3. Ange det användar namn och lösen ord som du angav i [skapa den virtuella datorn](#create-the-virtual-machine). Om du aldrig har anslutit till en virtuell Windows-dator i Azure, se [Anslut till virtuell dator](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Högerklicka på Start-knappen i Windows och klicka på **Enhetshanteraren**. Expandera noden **nätverkskort** . Kontrol lera att **Ethernet-kortet Mellanox ConnectX-3-funktionen** visas, som du ser i följande bild:

    ![Enhetshanteraren](./media/create-vm-accelerated-networking/device-manager.png)

Accelererat nätverk har nu Aktiver ATS för den virtuella datorn.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivera accelererat nätverk på befintliga virtuella datorer
Om du har skapat en virtuell dator utan accelererat nätverk är det möjligt att aktivera den här funktionen på en befintlig virtuell dator.  Den virtuella datorn måste ha stöd för accelererat nätverk genom att uppfylla följande krav som också beskrivs ovan:

* Den virtuella datorn måste ha en storlek som stöds för accelererat nätverk
* Den virtuella datorn måste vara en Azure Gallery-avbildning som stöds (och kernel-version för Linux)
* Alla virtuella datorer i en tillgänglighets uppsättning eller VMSS måste stoppas/frigöras innan du aktiverar accelererat nätverk på något nätverkskort

### <a name="individual-vms--vms-in-an-availability-set"></a>Enskilda virtuella datorer & virtuella datorer i en tillgänglighets uppsättning
Först stoppa/frigör den virtuella datorn eller, om det finns en tillgänglighets uppsättning, alla virtuella datorer i uppsättningen:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Viktigt, Observera att om din virtuella dator har skapats individuellt, utan en tillgänglighets uppsättning, behöver du bara stoppa/frigöra den enskilda virtuella datorn för att aktivera accelererat nätverk.  Om den virtuella datorn skapades med en tillgänglighets uppsättning måste alla virtuella datorer som finns i tillgänglighets uppsättningen stoppas/frigörs innan du aktiverar accelererat nätverk på något av nätverkskorten. 

När du har stoppat aktiverar du accelererat nätverk på den virtuella datorns nätverkskort:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Starta om den virtuella datorn eller, om den finns i en tillgänglighets uppsättning, alla virtuella datorer i uppsättningen och bekräfta att accelererat nätverk är aktiverat:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS skiljer sig något åt, men följer samma arbets flöde.  Stoppa först de virtuella datorerna:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

När de virtuella datorerna har stoppats uppdaterar du egenskapen för accelererat nätverk under nätverks gränssnittet:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Obs! en VMSS har uppgraderingar av virtuella datorer som tillämpar uppdateringar med tre olika inställningar, automatiska, rullande och manuella.  I dessa instruktioner är principen inställd på automatisk så att VMSS kommer att hämta ändringarna direkt efter omstarten.  Så här ställer du in det på automatiskt så att ändringarna omedelbart hämtas:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Starta slutligen om VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

När du har startat om väntar du tills uppgraderingarna har slutförts, men då visas VF i den virtuella datorn.  (Kontrol lera att du använder ett OS-och VM-storlek som stöds)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändra storlek på befintliga virtuella datorer med accelererat nätverk

Virtuella datorer med accelererat nätverk aktiverat kan bara ändras till virtuella datorer som har stöd för accelererat nätverk.  

Det går inte att ändra storlek på en virtuell dator med accelererat nätverk till en VM-instans som inte stöder accelererat nätverk med åtgärden ändra storlek.  I stället kan du ändra storlek på en av de virtuella datorerna:

* Stoppa/frigör den virtuella datorn eller om den finns i en tillgänglighets uppsättning/VMSS, stoppa/frigör alla virtuella datorer i uppsättningen/VMSS.
* Accelererat nätverk måste vara inaktiverat på NÄTVERKSKORTet för den virtuella datorn eller, om det finns en tillgänglighets uppsättning/VMSS, alla virtuella datorer i uppsättningen/VMSS.
* När det accelererade nätverket har inaktiverats kan VM/tillgänglighets uppsättningen/VMSS flyttas till en ny storlek som inte har stöd för accelererat nätverk och startas om.
