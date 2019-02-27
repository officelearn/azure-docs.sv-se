---
title: Skapa en Azure-dator med Accelererat nätverk | Microsoft Docs
description: Lär dig hur du skapar en Linux-dator med Accelererat nätverk.
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
ms.openlocfilehash: ae3d1fa6a921067e8c3b629206e47e1a1314adc1
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889247"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Skapa en Windows-dator med Accelererat nätverk

I den här självstudien får du lära dig hur du skapar en Windows virtuell dator (VM) med Accelererat nätverk. Om du vill skapa en Linux VM med Accelererat nätverk [skapa en Linux-VM med Accelererat nätverk](create-vm-accelerated-networking-cli.md). Accelererat nätverk gör det möjligt för enskild rot i/o-virtualisering (SR-IOV) till en virtuell dator, vilket avsevärt minskar tiden nätverkets prestanda. Den här sökvägen för högpresterande kringgår värden från datapath, vilket minskar latens och jitter CPU-belastningen för användning med de mest krävande arbetsbelastningarna på VM-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan accelererat nätverk:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Alla nätverkstrafiken och från den virtuella datorn måste passera värden och den virtuella växeln utan accelererat nätverk. Den virtuella växeln tillhandahåller alla tvingande, till exempel nätverkssäkerhetsgrupper, åtkomstkontrollistor, isolering och andra virtualiserade nätverkstjänster nätverkstrafik. Läs mer om virtuella växlar i [Hyper-V-nätverksvirtualisering och virtuella växeln](https://technet.microsoft.com/library/jj945275.aspx).

Med accelererat nätverk trafik anländer till den Virtuella datorns nätverksgränssnitt (NIC) och sedan vidarebefordras till den virtuella datorn. Alla nätverksprinciper som gäller för den virtuella växeln Avlastade nu och tillämpas i maskinvara. Tillämpa principen i maskinvara kan nätverkskort för att vidarebefordra trafik direkt till den virtuella datorn, vilket kringgår värden och den virtuella växeln, samtidigt som den principen som det tillämpas på värden.

Fördelarna med accelererat nätverk gäller endast för den virtuella datorn som den är aktiverad på. För bästa resultat är det perfekt för att aktivera den här funktionen på minst två virtuella datorer är anslutna till samma Azure Virtual Network (VNet). Vid kommunikation mellan virtuella nätverk eller ansluta lokala har den här funktionen minimal påverkan på Total svarstid.

## <a name="benefits"></a>Fördelar
* **Lägre latens / högre paket per sekund (pps):** Ta bort den virtuella växeln från datapath tar bort paket tid i värden för behandling av princip för och ökar antalet paket som kan bearbetas i den virtuella datorn.
* **Reducerat jitter:** Virtuell växel bearbetning beror på mängden principinformation som måste installeras och arbetsbelastningen processorkraft som klarar bearbetningen. Avlastning av principtillämpning till maskinvara tar bort den variationen genom att tillhandahålla paket direkt till den virtuella datorn, ta bort värden till VM-kommunikation och all programvara avbrott och kontext växlar.
* **Minskad CPU-belastning:** Kringgår den virtuella växeln på värden leder till färre CPU-belastningen för bearbetning av nätverkstrafik.

## <a name="limitations-and-constraints"></a>Begränsningar och villkor

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds direkt från Azure-galleriet:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Accelererat nätverk stöds i de flesta generella och beräkningsoptimerade instansstorlekar med minst 2 virtuella processorer.  Dessa alternativ som stöds är: D/DSv2 och F/Fs

På-instanser som har stöd för hypertrådning stöds Accelererat nätverk för VM-instanser med 4 eller fler virtuella processorer. Serier som stöds är: D/DSv3, E/ESv3, Fsv2 och Ms-/ Mms

Läs mer på VM-instanser, [Windows VM-storlekar](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regioner
Tillgängligt i alla offentliga Azure-regioner och Azure Government-molnet.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivering av Accelererat nätverk på en aktiv virtuell dator
En VM-storlek som stöds utan accelererat nätverk aktiverat kan bara ha funktionen aktiverad när den stoppas och frigörs.

### <a name="deployment-through-azure-resource-manager"></a>Distribution via Azure Resource Manager
Virtuella datorer (klassiska) kan inte distribueras med Accelererat nätverk.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Skapa en Windows VM med Azure Accelererat nätverk
## <a name="portal-creation"></a>Skapa Portal
Även om den här artikeln innehåller steg för att skapa en virtuell dator med accelererat nätverk med hjälp av Azure Powershell, du kan också [skapa en virtuell dator med accelererat nätverk med Azure portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du skapar en virtuell dator i portalen, i den **skapa en virtuell dator** bladet väljer du den **nätverk** fliken.  I den här fliken finns ett alternativ för **Accelerated networking**.  Om du har valt en [operativsystem som stöds](#supported-operating-systems) och [VM-storlek](#supported-vm-instances), det här alternativet kommer automatiskt att fylla i ”på”.  Om inte, den fylla i alternativet ”Off” för Accelererat nätverk och ge användaren en orsak till varför den inte är aktiveras.   
* *Obs!* Endast operativsystem som stöds kan aktiveras via portalen.  Om du använder en anpassad avbildning och din avbildning har stöd för Accelererat nätverk, skapar du en virtuell dator med CLI eller Powershell. 

När den virtuella datorn har skapats kan bekräfta du Accelererat nätverk är aktiverat genom att följa anvisningarna i Bekräfta att accelererat nätverk är aktiverat.

## <a name="powershell-creation"></a>Skapa en PowerShell
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installera [Azure PowerShell](/powershell/azure/install-az-ps) version 1.0.0 eller senare. Du hittar din nuvarande version genom att köra `Get-Module -ListAvailable Az`. Om du behöver installera eller uppgradera kan du installera den senaste versionen av Az-modul från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az). Logga in i en PowerShell-session till en Azure-konto med hjälp av [Connect AzAccount](/powershell/module/az.profile/connect-azaccount).

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen ingår *myResourceGroup*, *myNic*, och *myVM*.

Skapa en resursgrupp med [New AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *centralus* plats:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Börja med att skapa en undernätskonfiguration med [New AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). I följande exempel skapas ett undernät med namnet *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Skapa ett virtuellt nätverk med [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), med den *mySubnet* undernät.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa först en nätverkssäkerhetsgruppregel med [New AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

Skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) och tilldela den *Tillåt RDP alla* säkerhetsregel till den. Förutom den *Tillåt RDP alla* regeln för nätverkssäkerhetsgruppen innehåller flera standardregler. En standardregel inaktiverar all inkommande åtkomst från Internet, vilket är därför den *Tillåt RDP alla* regeln tilldelas till nätverkssäkerhetsgruppen så att du kan fjärransluta till den virtuella datorn när den har skapats.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associera nätverkssäkerhetsgruppen till den *mySubnet* undernätet med [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Regeln i nätverkssäkerhetsgruppen är effektiva för alla resurser som har distribuerats i undernätet.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverksgränssnitt med accelererat nätverk
Skapa en offentlig IP-adress med hjälp av [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). En offentlig IP-adress krävs inte om du inte planerar att få åtkomst till den virtuella datorn från Internet, men för att slutföra stegen i den här artikeln, det krävs.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Skapa ett nätverksgränssnitt med [New AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) med accelererat nätverk aktiverat och tilldela offentliga IP-adress till nätverksgränssnittet. I följande exempel skapas ett nätverksgränssnitt med namnet *myNic* i den *mySubnet* undernät för den *myVnet* virtuellt nätverk och tilldelar den *myPublicIp*  offentliga IP-adressen till den:

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

Ange dina autentiseringsuppgifter för virtuell dator den `$cred` variabeln med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Definiera först den virtuella datorn med [New AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Följande exempel definierar en virtuell dator med namnet *myVM* med en VM-storlek som har stöd för Accelererat nätverk (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

En lista över alla storlekar för Virtuella datorer och egenskaper finns i [Windows VM-storlekar](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Skapa resten av VM-konfiguration med [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) och [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). I följande exempel skapas en virtuell Windows Server 2016-dator:

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

Bifoga nätverksgränssnittet som du skapade tidigare med [Lägg till AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Skapa slutligen den virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Bekräfta drivrutinen har installerats i operativsystemet

När du skapar den virtuella datorn i Azure kan ansluta till den virtuella datorn och bekräfta att drivrutinen är installerat i Windows.

1. Öppna Azure från en webbläsare [portal](https://portal.azure.com) och logga in med ditt Azure-konto.
2. I rutan som innehåller texten *Sök efter resurser* högst upp på Azure-portalen, Skriv *myVm*. När **myVm** visas i sökresultaten klickar du på den. Om **skapa** visas under den **Connect** knapp, Azure inte ännu har skapat den virtuella datorn. Klicka på **Connect** i det övre vänstra hörnet av översikten endast efter att du inte längre se **skapa** under den **Connect** knappen.
3. Ange användarnamnet och lösenordet du angav i [skapa den virtuella datorn](#create-the-virtual-machine). Om du aldrig har anslutit till en Windows-dator i Azure, se [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Högerklicka på Windows Start-knappen och klicka på **Enhetshanteraren**. Expandera den **nätverkskort** noden. Bekräfta att den **Mellanox ConnectX-3 virtuella funktionen Ethernet-kort** visas som i följande bild:

    ![Enhetshanteraren](./media/create-vm-accelerated-networking/device-manager.png)

Accelererat nätverk har nu aktiverats för den virtuella datorn.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivera Accelererat nätverk på befintliga virtuella datorer
Om du har skapat en virtuell dator utan Accelererat nätverk, är det möjligt att aktivera den här funktionen på en befintlig virtuell dator.  Den virtuella datorn måste ha stöd för Accelererat nätverk genom att uppfylla följande krav som också beskrivs ovan:

* Den virtuella datorn måste vara en storlek som stöds för Accelererat nätverk
* Den virtuella datorn måste vara stöds Azure Gallery image (och kernel-version för Linux)
* Alla virtuella datorer i en tillgänglighetsuppsättning eller VMSS måste vara stoppad/frigjord innan du aktiverar Accelererat nätverk på en NIC

### <a name="individual-vms--vms-in-an-availability-set"></a>Ange enskilda virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning
Först stoppa/frigör den virtuella datorn eller om en Tillgänglighetsuppsättning, alla virtuella datorer i uppsättningen:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Viktigt,. Observera att om den virtuella datorn skapades individuellt, utan en tillgänglighetsuppsättning du bara behöva stoppa/frigör den enskilda virtuella datorn om du vill aktivera Accelererat nätverk.  Om den virtuella datorn skapades med en tillgänglighetsuppsättning måste alla virtuella datorer i tillgänglighetsuppsättningen är stoppad/frigjord innan du aktiverar Accelererat nätverk på något av nätverkskorten. 

Stoppat, aktivera Accelererat nätverk på nätverkskortet för den virtuella datorn:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Starta om virtuell dator eller, om i en tillgänglighetsuppsättning, alla virtuella datorer i uppsättningen, och bekräfta att Accelererat nätverk är aktiverat:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS skiljer sig men följer samma arbetsflöde.  Stoppa först de virtuella datorerna:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

När de virtuella datorerna har stoppats, uppdatera egenskapen Accelererat nätverk under nätverksgränssnittet:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Observera att en VMSS har du VM-uppgraderingar som gäller uppdateringar med hjälp av tre olika inställningar, automatiskt, löpande och manuellt.  I dessa anvisningar är principen inställd på automatisk så att VMSS tar över ändringarna direkt efter omstart.  Ange den till automatisk så att ändringarna omedelbart plockas upp:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Slutligen ska du starta om VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

När du startar om, vänta på att uppgraderingen ska slutföras, men när det har slutförts, VF visas inuti den virtuella datorn.  (Kontrollera att du använder en OS- och VM-storlek som stöds)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändra storlek på befintliga virtuella datorer med Accelererat nätverk

Virtuella datorer med Accelererat nätverk aktiverat kan bara ändras till virtuella datorer som har stöd för Accelererat nätverk.  

En virtuell dator med Accelererat nätverk aktiverat kan inte ändras till en VM-instans som inte har stöd för Accelererat nätverk med hjälp av storleksändring-åtgärd.  I stället att ändra storlek på en av dessa virtuella datorer:

* Stoppa/frigöra den virtuella datorn eller om du är i tillgänglighet set/VMSS, stoppa/Frigör alla virtuella datorer i uppsättningen/VMSS.
* Accelererat nätverk måste inaktiveras på nätverkskortet för den virtuella datorn eller om de finns i en tillgänglighet set/VMSS, alla virtuella datorer i uppsättningen/VMSS.
* VM-tillgänglighet set/VMSS kan flyttas till en ny storlek som inte har stöd för Accelererat nätverk och startas om när Accelererat nätverk är inaktiverat.
