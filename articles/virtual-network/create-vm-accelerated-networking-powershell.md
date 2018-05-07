---
title: Skapa en virtuell Azure-dator med snabbare nätverk | Microsoft Docs
description: Lär dig hur du skapar en virtuell Linux-dator med snabbare nätverk.
services: virtual-network
documentationcenter: ''
author: jdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: 6d7e41b2b631fcecefd835a10e9b91fd9bb3f17d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Skapa en Windows-dator med snabbare nätverk

Lär dig hur du skapar en Windows-dator (VM) med snabbare nätverk i den här självstudiekursen. Om du vill skapa en Linux VM med snabbare nätverk finns [skapa ett Linux VM med snabbare nätverk](create-vm-accelerated-networking-cli.md). Snabbare nätverksfunktioner kan single-root I/O virtualization (SR-IOV) till en virtuell dator, vilket avsevärt minskar tiden dess nätverksprestanda. Den här sökvägen för högpresterande kringgår värden från datapath, vilket minskar latens och jitter CPU-belastningen för användning med de mest krävande nätverksbelastning på VM-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan snabbare nätverksfunktioner:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Utan snabbare nätverk måste alla nätverkstrafiken till och från den virtuella datorn passerar värden och den virtuella växeln. Den virtuella växeln tillhandahåller alla tvingande, till exempel nätverkssäkerhetsgrupper, åtkomstkontrollistor, isolering och andra virtualiserade nätverkstjänster för nätverkstrafik. Mer information om virtuella växlar på [Hyper-V-nätverksvirtualisering och den virtuella växeln](https://technet.microsoft.com/library/jj945275.aspx) artikel.

Med snabbare nätverksfunktioner trafik anländer till den Virtuella datorns nätverksgränssnitt (NIC) och sedan vidarebefordras till den virtuella datorn. Alla principer för nätverk som gäller för den virtuella växeln avläst nu och tillämpas i maskinvara. Tillämpa principen i den maskinvara som gör det möjligt för nätverkskort för att vidarebefordra trafik direkt till den virtuella datorn, vilket kringgår värden och den virtuella växeln samtidigt som den principen tillämpades på värden.

Fördelarna med snabbare nätverksfunktioner gäller endast för den virtuella datorn som den är aktiverad på. Det är perfekt att aktivera funktionen på minst två virtuella datorer anslutna till samma Azure virtuella nätverk (VNet) för bästa resultat. Vid kommunikation över Vnet eller anslutande lokalt har funktionen minimal inverkan på övergripande svarstiden.

## <a name="benefits"></a>Fördelar
* **Lägre latens / högre paket per sekund (pps):** att ta bort den virtuella växeln från datapath tar bort paket tid i värden för behandling av princip för och ökar antalet paket som kan bearbetas inuti den virtuella datorn.
* **Minskar jitter:** virtuella växeln bearbetning beror på mängden principinformation som måste tillämpas och arbetsbelastningen processorkraft som utför bearbetning. Avlastning tvingande principer till maskinvaran som tar bort den variationen genom att leverera paket direkt till den virtuella datorn, ta bort värden till programvara avbrott och kontext växlar för VM-kommunikation och alla.
* **Minskas CPU-användning:** kringgår den virtuella växeln på värden leder till färre CPU-belastningen för bearbetning av nätverkstrafik.

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Out of box från Azure-galleriet stöds följande distributioner: 
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter** 

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Snabbare nätverksfunktioner stöds på mest generella och beräknings-optimerad instans storlekar med vCPUs 2 eller högre.  Dessa serier som stöds är: D/DSv2 och F/Fs

På instanser som har stöd för hypertrådar stöds snabbare nätverk för VM-instanser med 4 eller fler vCPUs. Stöds serien är: D/DSv3, E/ESv3, Fsv2 och Ms-/ Mms

Mer information om VM-instanser finns [Windows VM-storlekar](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regioner
Tillgänglig i alla offentliga Azure-regioner och Azure Government-molnet.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivera snabbare nätverk på en aktiv virtuell dator
En VM-storlek som stöds utan snabbare nätverksfunktioner som är aktiverad kan bara ha funktionen aktiverad när den stoppas och frigöra.

### <a name="deployment-through-azure-resource-manager"></a>Distribution via Azure Resource Manager
Virtuella datorer (klassisk) kan inte distribueras med snabbare nätverk.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Skapa en Windows VM med Azure snabbare nätverksfunktioner

Även om den här artikeln innehåller steg för att skapa en virtuell dator med snabbare nätverk med hjälp av Azure PowerShell, kan du också [skapa en virtuell dator med snabbare nätverk med Azure-portalen](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du skapar en virtuell dator i portalen under **inställningar**väljer **aktiverad**under **snabbare nätverk**. Att aktivera snabbare nätverksfunktioner inte visas i portalen om du har valt en [operativsystem som stöds](#supported-operating-systems) och [VM-storlek](#supported-vm-instances). När den virtuella datorn skapas, måste du slutföra anvisningarna i [bekräfta drivrutinen är installerad i operativsystemet](#confirm-the-driver-is-installed-in-the-operating-system).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Installera [Azure PowerShell](/powershell/azure/install-azurerm-ps) version 5.1.1 eller senare. Du hittar din nuvarande installerade versionen genom att köra `Get-Module -ListAvailable AzureRM`. Om du behöver installera eller uppgradera, installera den senaste versionen av AzureRM-modul från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureRM). Logga in i PowerShell-session till en Azure-konto med hjälp av [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn ingår *myResourceGroup*, *myNic*, och *myVM*.

Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *centralus* plats:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Skapa först en konfiguration av undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). I följande exempel skapas ett undernät med namnet *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork), med den *mySubnet* undernät.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Först skapar du en grupp nätverkssäkerhetsregeln med [ny AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
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

Skapa en nätverkssäkerhetsgrupp med [ny AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) och tilldela den *Tillåt RDP alla* säkerhetsregeln till den. Förutom den *Tillåt RDP alla* regeln för nätverkssäkerhetsgruppen innehåller flera standardregler. En standardregel inaktiverar all inkommande åtkomst från Internet, och därför den *Tillåt RDP alla* regeln har tilldelats nätverkssäkerhetsgruppen så att du kan fjärransluta till den virtuella datorn när den har skapats.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associera nätverkssäkerhetsgruppen till den *mySubnet* undernät med [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). Regeln i nätverkssäkerhetsgruppen börjar gälla för alla resurser som har distribuerats i undernätet.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverksgränssnitt med snabbare nätverksfunktioner
Skapa en offentlig IP-adress med [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). En offentlig IP-adress krävs inte om du inte åtkomst till den virtuella datorn från Internet, men för att slutföra stegen i den här artikeln, det är obligatoriskt.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Skapa ett nätverksgränssnitt med [ny AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) med snabbare nätverk som är aktiverad och tilldela den offentliga IP-adressen till nätverksgränssnittet. I följande exempel skapas ett nätverksgränssnitt med namnet *myNic* i den *mySubnet* undernätet för den *myVnet* virtuella nätverk och tilldelar den *myPublicIp*  offentliga IP-adressen till den:

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Ange autentiseringsuppgifterna för ditt VM den `$cred` variabeln med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Först definierar du den virtuella datorn med [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). I följande exempel definieras en virtuell dator med namnet *myVM* med en VM-storlek som har stöd för snabbare nätverk (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

En lista över alla VM-storlekar och egenskaper, se [Windows VM-storlekar](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Skapa resten av VM-konfiguration med [Set AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) och [Set AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). I följande exempel skapas en Windows Server 2016 VM:

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

Bifoga nätverksgränssnittet som du skapade tidigare med [Lägg till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Skapa slutligen den virtuella datorn med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Bekräfta drivrutinen är installerad i operativsystemet

När du skapar den virtuella datorn i Azure kan ansluta till den virtuella datorn och bekräfta att drivrutinen är installerat i Windows.

1. Öppna en webbläsare på Azure [portal](https://portal.azure.com) och logga in med ditt Azure-konto.
2. I rutan som innehåller texten *söka resurser* längst upp i Azure-portalen, Skriv *myVm*. När **myVm** visas i sökresultaten klickar du på den. Om **skapa** visas under den **Anslut** knappen Azure inte ännu har skapar den virtuella datorn. Klicka på **Anslut** i det övre vänstra hörnet i översikten när du inte längre se **skapa** under den **Anslut** knappen.
3. Ange användarnamnet och lösenordet du angav i [skapa den virtuella datorn](#create-the-virtual-machine). Om du aldrig har anslutit till en Windows-dator i Azure, se [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Högerklicka på Start och klicka på **Enhetshanteraren**. Expandera den **nätverkskort** nod. Bekräfta att den **Mellanox ConnectX 3 virtuella funktionen Ethernet-nätverkskort** visas som i följande bild:

    ![Enhetshanteraren](./media/create-vm-accelerated-networking/device-manager.png)

Snabbare nätverksfunktioner har nu aktiverats för den virtuella datorn.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivera snabbare nätverk på befintliga virtuella datorer
Om du har skapat en virtuell dator utan snabbare nätverk, är det möjligt att aktivera den här funktionen på en befintlig virtuell dator.  Den virtuella datorn måste ha stöd för snabbare nätverk genom att uppfylla följande krav som också beskrivs ovan:

* Den virtuella datorn måste ha en storlek som stöds för snabbare nätverk
* Den virtuella datorn måste vara en Azure-galleriet bildtyper (och kernel-version för Linux)
* Alla virtuella datorer i en tillgänglighetsuppsättning eller VMSS måste vara stoppad/frigjorts innan du aktiverar snabbare nätverk på ett nätverkskort

### <a name="individual-vms--vms-in-an-availability-set"></a>Ange enskilda virtuella datorer och virtuella datorer i en tillgänglighetsgrupp
Först stoppa/frigöra den virtuella datorn, eller om en Tillgänglighetsuppsättning, alla virtuella datorer i uppsättningen:

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Viktiga, ange Tänk på att om den virtuella datorn skapades individuellt, utan en tillgänglighetsuppsättning du bara behöver stoppa/frigöra den enskilda VM för att möjliggöra snabbare nätverk.  Om den virtuella datorn har skapats i en tillgänglighetsuppsättning, måste alla virtuella datorer i tillgänglighetsuppsättningen ska stoppas/frigjorts innan du aktiverar snabbare nätverk på något av nätverkskorten. 

När stoppats kan du aktivera snabbare nätverk på nätverkskortet på den virtuella datorn:

```azurepowershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRmNetworkInterface
```

Starta om din Virtuella eller, om i en Tillgänglighetsuppsättning, alla virtuella datorer i uppsättningen och kontrollera att snabbare nätverk har aktiverats: 

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS är något annorlunda men följer samma arbetsflöde.  Först stoppa virtuella datorer:

```azurepowershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Uppdatera egenskapen snabbare nätverk under nätverksgränssnittet när de virtuella datorerna har stoppats:

```azurepowershell
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Kontrollera har anteckningen, en VMSS VM-uppgraderingar som tillämpa uppdateringar med hjälp av tre olika inställningar automatiskt, rullande och manuellt.  I de här anvisningarna är policyn inställd på automatisk så att VMSS ska hämta upp ändringarna direkt efter omstart.  Ange det till automatiskt så att ändringarna är omedelbart tas upp: 

```azurecli
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Slutligen ska du starta om VMSS:

```azurecli
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

När du starta om, vänta tills uppgraderingar till slut, men när de har slutförts, visas VF inuti den virtuella datorn.  (Kontrollera att du använder en stöds OS och VM-storlek)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändra storlek på befintliga virtuella datorer med snabbare nätverk

Virtuella datorer med snabbare nätverk aktiverat kan endast ändras till virtuella datorer som har stöd för snabbare nätverk.  

En virtuell dator med snabbare nätverk aktiverad kan inte ändras till en VM-instans som inte har stöd för snabbare nätverk med hjälp av åtgärden Ändra storlek.  I stället att ändra storlek på en av dessa virtuella datorer: 

* Stoppa/Deallocate den virtuella datorn eller om i tillgänglighet set/VMSS, stoppa/Frigör alla virtuella datorer i uppsättningen/VMSS.
* Snabbare nätverksfunktioner måste inaktiveras på nätverkskort för virtuell dator eller om i en tillgänglighet set/VMSS, alla virtuella datorer i uppsättningen/VMSS.
* När snabbare nätverk är inaktiverat flyttas VM/tillgänglighet set/VMSS till en ny storlek som inte har stöd för snabbare nätverk och startas om.  



