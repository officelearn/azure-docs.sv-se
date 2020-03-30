---
title: Skapa en virtuell Azure-dator med accelererat nätverk - Azure PowerShell
description: Lär dig hur du skapar en virtuell Linux-dator med accelerated networking.
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
ms.openlocfilehash: 16837782af2f08e27363091dc21587a100194cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245062"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Skapa en virtuell Windows-dator med accelererat nätverk med Azure PowerShell

I den här självstudien får du lära dig hur du skapar en virtuell Dator (VM) med Accelererat nätverk. Om du vill skapa en virtuell Linux-dator med accelererat nätverk finns i [Skapa en virtuell Linux-dator med accelererat nätverk](create-vm-accelerated-networking-cli.md). Accelererad nätverk möjliggör enkel rot-I/O-virtualisering (SR-IOV) till en virtuell dator, vilket avsevärt förbättrar dess nätverksprestanda. Den här banan med hög prestanda kringgår värden från datasökvägen, vilket minskar svarstiden, jitter- och CPU-användningen, för användning med de mest krävande nätverksarbetsbelastningarna på vm-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan accelererat nätverkande:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Utan accelererade nätverk måste all nätverkstrafik in och ut ur den virtuella datorn passera värden och den virtuella växeln. Den virtuella växeln innehåller alla principefterlevnad, till exempel nätverkssäkerhetsgrupper, åtkomstkontrolllistor, isolering och andra virtuella nätverkstjänster till nätverkstrafik. Mer information om virtuella växlar finns i [Hyper-V-nätverksvirtualisering och virtuell växel](https://technet.microsoft.com/library/jj945275.aspx).

Med accelererad nätverksanslutning anländer nätverkstrafik till den virtuella datorns nätverksgränssnitt (NIC) och vidarebefordras sedan till den virtuella datorn. Alla nätverksprinciper som den virtuella växeln gäller är nu avinläst och tillämpas i maskinvara. Genom att tillämpa principen i maskinvaran kan nätverkskortet vidarebefordra nätverkstrafik direkt till den virtuella datorn, förbi värden och den virtuella växeln, samtidigt som alla principer som tillämpas i värden bibehålls.

Fördelarna med accelererade nätverk gäller endast för den virtuella datorn som den är aktiverad på. För bästa resultat är det idealiskt att aktivera den här funktionen på minst två virtuella datorer som är anslutna till samma Virtuella Azure-nätverk (VNet). När du kommunicerar över virtuella nätverk eller ansluter lokalt har den här funktionen minimal inverkan på den totala svarstiden.

## <a name="benefits"></a>Fördelar
* **Lägre latens / Högre paket per sekund (pps):** Om du tar bort den virtuella växeln från datasökvägen tas tidspaketen i värden för principbearbetning bort och antalet paket som kan bearbetas inuti den virtuella datorn ökar.
* **Minskad jitter:** Virtuell växelbearbetning beror på hur mycket princip som måste tillämpas och arbetsbelastningen för processorn som utför bearbetningen. Avlastning av principövernsättningen till maskinvaran tar bort den variabiliteten genom att leverera paket direkt till den virtuella datorn, ta bort värden till VM-kommunikation och alla programvaruavbrott och kontextväxlar.
* **Minskad CPU-användning:** Att kringgå den virtuella växeln i värden leder till mindre CPU-användning för bearbetning av nätverkstrafik.

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

### <a name="supported-operating-systems"></a>Operativsystem som stöds
Följande distributioner stöds direkt från Azure Gallery:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>VM-instanser som stöds
Accelererad nätverk stöds på de flesta allmänna ändamål och beräkningsoptimerade instansstorlekar med 2 eller fler virtuella processorer.  Dessa serier som stöds är: D/DSv2 och F/Fs

På instanser som stöder hypertrådning stöds accelererad nätverk på VM-instanser med 4 eller fler virtuella processorer. Serien stöds är: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms och Ms/Mmsv2.

Mer information om VM-instanser finns i [Storlekarna för Virtuella datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)i Windows.

### <a name="regions"></a>Regioner
Tillgänglig i alla offentliga Azure-regioner och Azure Government Cloud.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivera accelererat nätverk på en virtuell dator som körs
En vm-storlek som stöds utan att accelererade nätverk är aktiverat kan bara ha funktionen aktiverad när den stoppas och frigörs.

### <a name="deployment-through-azure-resource-manager"></a>Distribution via Azure Resource Manager
Virtuella datorer (klassiska) kan inte distribueras med accelerated networking.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Skapa en Virtuell Windows-dator med Azure Accelerated Networking
## <a name="portal-creation"></a>Skapa portal
Även om den här artikeln innehåller steg för att skapa en virtuell dator med accelererade nätverk med Azure Powershell, kan du också [skapa en virtuell dator med accelererade nätverk med Azure-portalen](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). När du skapar en virtuell dator i portalen väljer du fliken **Nätverk** i bladet **Skapa en virtuell dator.**  På den här fliken finns det ett alternativ för **Accelererat nätverk .**  Om du har valt ett operativsystem och [vm-storlek](#supported-vm-instances) [som stöds](#supported-operating-systems) fylls det här alternativet automatiskt i till "På".  Om inte, kommer det att fylla "Off" alternativet för accelererade nätverk och ge användaren en anledning till varför det inte är aktiverat.   
* *Anm.:* Endast operativsystem som stöds kan aktiveras via portalen.  Om du använder en anpassad avbildning och avbildningen stöder Accelerated Networking skapar du den virtuella datorn med CLI eller Powershell. 

När den virtuella datorn har skapats kan du bekräfta att accelererat nätverk har aktiverats genom att följa instruktionerna i bekräfta att accelererade nätverk är aktiverat.

## <a name="powershell-creation"></a>Skapa Powershell
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installera [Azure PowerShell](/powershell/azure/install-az-ps) version 1.0.0 eller senare. Om du vill hitta `Get-Module -ListAvailable Az`den installerade versionen kör du . Om du behöver installera eller uppgradera installerar du den senaste versionen av Az-modulen från [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). I en PowerShell-session loggar du in på ett [Azure-konto med Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn inkluderade *myResourceGroup,* *myNic*och *myVM*.

Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *centralus-platsen:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Skapa först en undernätskonfiguration med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). I följande exempel skapas ett undernät med namnet *mySubnet:*

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Skapa ett virtuellt nätverk med [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), med *mySubnet-undernätet.*

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa först en regel för nätverkssäkerhetsgrupper med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

Skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) och tilldela säkerhetsregeln *Tillåt-RDP-All.* Förutom regeln *Tillåt-RDP-All* innehåller nätverkssäkerhetsgruppen flera standardregler. En standardregel inaktiverar all inkommande åtkomst från Internet, vilket är anledningen till att regeln *Tillåt-RDP-All* tilldelas nätverkssäkerhetsgruppen så att du kan fjärransluta till den virtuella datorn när den har skapats.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Associera nätverkssäkerhetsgruppen till *undernätet mySubnet* med [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Regeln i nätverkssäkerhetsgruppen gäller för alla resurser som distribueras i undernätet.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverksgränssnitt med accelererade nätverk
Skapa en offentlig IP-adress med hjälp av [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). En offentlig IP-adress krävs inte om du inte planerar att komma åt den virtuella datorn från Internet, men för att slutföra stegen i den här artikeln krävs det.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Skapa ett nätverksgränssnitt med [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) med accelererade nätverk aktiverat och tilldela den offentliga IP-adressen till nätverksgränssnittet. I följande exempel skapas ett nätverksgränssnitt med namnet *myNic* i *mySubnet-undernätet* i *myVnets* virtuella nätverk och den är den för den offentliga IP-adressen *myPublicIp:*

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

Ange autentiseringsuppgifterna `$cred` för den virtuella datorn till variabeln med [Get-Autentiseringsuppgifter:](/powershell/module/microsoft.powershell.security/get-credential)

```powershell
$cred = Get-Credential
```

Definiera först din virtuella dator med [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). I följande exempel definieras en virtuell dator med namnet *myVM* med en vm-storlek som stöder accelererat nätverk *(Standard_DS4_v2):*

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

En lista över alla vm-storlekar och egenskaper finns i [Storlekarna för virtuella datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)i Windows.

Skapa resten av vm-konfigurationen med [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) och [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). I följande exempel skapas en virtuell dator med Windows Server 2016:

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

Koppla nätverksgränssnittet som du tidigare skapade med [Add-AzVMNetworkInterface:](/powershell/module/az.compute/add-azvmnetworkinterface)

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Slutligen, skapa din virtuella dator med [New-AzVM:](/powershell/module/az.compute/new-azvm)

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Bekräfta att drivrutinen är installerad i operativsystemet

När du har skapat den virtuella datorn i Azure ansluter du till den virtuella datorn och bekräftar att drivrutinen är installerad i Windows.

1. Öppna [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto.
2. Skriv *myVm*i rutan som innehåller texten *Sökresurser* högst upp i Azure-portalen . När **myVm** visas i sökresultaten klickar du på den. Om **Skapa** visas under **knappen Anslut** har Azure ännu inte slutfört skapandet av den virtuella datorn. Klicka på **Anslut** i det övre vänstra hörnet i översikten först när du inte längre ser **Skapa** under **knappen Anslut.**
3. Ange det användarnamn och lösenord som du angav i [Skapa den virtuella datorn](#create-the-virtual-machine). Om du aldrig har anslutit till en Windows VM i Azure läser du [Anslut till virtuell dator](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Högerklicka på Startknappen i Windows och klicka på **Enhetshanteraren**. Expandera noden **Nätverkskort.** Bekräfta att **Mellanox ConnectX-3-Ethernet-adaptern** för virtuell funktion visas, vilket visas på följande bild:

    ![Enhetshanteraren](./media/create-vm-accelerated-networking/device-manager.png)

Accelererad nätverk är nu aktiverat för din virtuella dator.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivera accelererat nätverk på befintliga virtuella datorer
Om du har skapat en virtuell dator utan accelererat nätverk är det möjligt att aktivera den här funktionen på en befintlig virtuell dator.  Den virtuella datorn måste stödja accelererade nätverk genom att uppfylla följande förutsättningar som också beskrivs ovan:

* Den virtuella datorn måste vara en storlek som stöds för accelererade nätverk
* Den virtuella datorn måste vara en Azure Gallery-avbildning som stöds (och kärnversion för Linux)
* Alla virtuella datorer i en tillgänglighetsuppsättning eller VMSS måste stoppas/frigöras innan accelererad nätverk aktiveras på alla nätverkskort

### <a name="individual-vms--vms-in-an-availability-set"></a>Enskilda virtuella datorer & virtuella datorer i en tillgänglighetsuppsättning
Första stopp/deallocate den virtuella datorn eller, om en tillgänglighetsuppsättning, alla virtuella datorer i setet:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Viktigt, observera, om din virtuella dator skapades individuellt, utan en tillgänglighetsuppsättning, behöver du bara stoppa/deallocate den enskilda virtuella datorn för att aktivera accelererat nätverk.  Om den virtuella datorn skapades med en tillgänglighetsuppsättning måste alla virtuella datorer som ingår i tillgänglighetsuppsättningen stoppas/frigöras innan accelererade nätverk aktiveras på någon av nätverkskorten. 

Aktivera Accelererat nätverk på nätverkskortet för den virtuella datorn när det har stoppats:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Starta om den virtuella datorn eller, om du är i en tillgänglighetsuppsättning, alla virtuella datorer i uppsättningen och bekräfta att accelererat nätverk är aktiverat:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS är något annorlunda men följer samma arbetsflöde.  Stoppa först de virtuella datorerna:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

När de virtuella datorerna har stoppats uppdaterar du egenskapen Accelerated Networking under nätverksgränssnittet:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Observera att en VMSS har VM-uppgraderingar som tillämpar uppdateringar med tre olika inställningar, automatisk, rullande och manuell.  I dessa instruktioner är principen inställd på automatisk så att VMSS hämtar ändringarna omedelbart efter omstart.  Så här ställer du in den automatiskt så att ändringarna omedelbart plockas upp:

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

När du startar om, vänta tills uppgraderingarna är klara men när de är klara visas VF inuti den virtuella datorn.  (Kontrollera att du använder en os- och vm-storlek som stöds)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändra storlek på befintliga virtuella datorer med accelererat nätverk

Virtuella datorer med accelererat nätverk aktiverat kan endast ändra storlek på virtuella datorer som stöder accelererat nätverkande.  

En virtuell dator med aktiverat accelererat nätverk kan inte ändra storlek på en VM-instans som inte stöder accelererat nätverk med hjälp av storleksåtgärden.  Om du vill ändra storlek på en av dessa virtuella datorer:

* Stoppa/frigöra den virtuella datorn eller om du i en tillgänglighetsuppsättning/VMSS stoppar/frigör alla virtuella datorer i set/VMSS.
* Accelererad nätverk måste inaktiveras på nätverkskortet för den virtuella datorn eller om alla virtuella datorer i set/VMSS i en tillgänglighetsuppsättning/VMSS.
* När accelererade nätverk har inaktiverats kan VM/availability set/VMSS flyttas till en ny storlek som inte stöder accelererat nätverk och startas om.
