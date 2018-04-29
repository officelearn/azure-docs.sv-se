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
ms.openlocfilehash: 105a32f37c0a6a212888f9ee8457844769b9a3c7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Skapa en Windows-dator med snabbare nätverk

> [!IMPORTANT]
> Virtuella datorer måste skapas med snabbare nätverk aktiverad. Den här funktionen kan inte aktiveras på befintliga virtuella datorer. Utför följande steg för att möjliggöra snabbare nätverksfunktioner:
>   1. Ta bort den virtuella datorn
>   2. Skapa den virtuella datorn med snabbare nätverksfunktioner som är aktiverad
>

Lär dig hur du skapar en Windows-dator (VM) med snabbare nätverk i den här självstudiekursen. Om du vill skapa en Linux VM med snabbare nätverk finns [skapa ett Linux VM med snabbare nätverk](create-vm-accelerated-networking-cli.md). Snabbare nätverksfunktioner kan single-root I/O virtualization (SR-IOV) till en virtuell dator, vilket avsevärt minskar tiden dess nätverksprestanda. Den här sökvägen för högpresterande kringgår värden från datapath, vilket minskar latens och jitter CPU-belastningen för användning med de mest krävande nätverksbelastning på VM-typer som stöds. Följande bild visar kommunikation mellan två virtuella datorer med och utan snabbare nätverksfunktioner:

![Jämförelse](./media/create-vm-accelerated-networking/accelerated-networking.png)

Utan snabbare nätverk måste alla nätverkstrafiken till och från den virtuella datorn passerar värden och den virtuella växeln. Den virtuella växeln tillhandahåller alla tvingande, till exempel nätverkssäkerhetsgrupper, åtkomstkontrollistor, isolering och andra virtualiserade nätverkstjänster för nätverkstrafik. Mer information om virtuella växlar på [Hyper-V-nätverksvirtualisering och den virtuella växeln](https://technet.microsoft.com/library/jj945275.aspx) artikel.

Med snabbare nätverksfunktioner trafik anländer till den Virtuella datorns nätverksgränssnitt (NIC) och sedan vidarebefordras till den virtuella datorn. Alla principer för nätverk som gäller för den virtuella växeln avläst nu och tillämpas i maskinvara. Tillämpa principen i den maskinvara som gör det möjligt för nätverkskort för att vidarebefordra trafik direkt till den virtuella datorn, vilket kringgår värden och den virtuella växeln samtidigt som den principen tillämpades på värden.

Fördelarna med snabbare nätverksfunktioner gäller endast för den virtuella datorn som den är aktiverad på. Det är perfekt att aktivera funktionen på minst två virtuella datorer anslutna till samma Azure virtuella nätverk (VNet) för bästa resultat. Vid kommunikation över Vnet eller anslutande lokalt har funktionen minimal inverkan på övergripande svarstiden.

## <a name="benefits"></a>Fördelar
* **Lägre latens / högre paket per sekund (pps):** att ta bort den virtuella växeln från datapath tar bort paket tid i värden för behandling av princip för och ökar antalet paket som kan bearbetas inuti den virtuella datorn.
* **Minskar jitter:** virtuella växeln bearbetning beror på mängden principinformation som måste tillämpas och arbetsbelastningen processorkraft som utför bearbetning. Avlastning tvingande principer till maskinvaran som tar bort den variationen genom att leverera paket direkt till den virtuella datorn, ta bort värden till programvara avbrott och kontext växlar för VM-kommunikation och alla.
* **Minskas CPU-användning:** kringgår den virtuella växeln på värden leder till färre CPU-belastningen för bearbetning av nätverkstrafik.

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Microsoft Windows Server 2012 R2 Datacenter och Windows Server 2016.

## <a name="supported-vm-instances"></a>VM-instanser som stöds
Snabbare nätverksfunktioner stöds på mest generella och beräknings-optimerad instans storlekar med 4 eller fler vCPUs. På instanser, till exempel D/DSv3 eller E/ESv3 som stöder hypertrådar stöds snabbare nätverk för VM-instanser med 8 eller flera vCPUs. Stöds serien är: D/DSv2, D/DSv3, E/ESv3, Fsv2-F/Fs och Ms-/ Mms.

Mer information om VM-instanser finns [Windows VM-storlekar](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regioner
Tillgänglig i alla offentliga Azure-regioner och Azure Government-molnet.

## <a name="limitations"></a>Begränsningar
Följande begränsningar gäller när du använder den här funktionen:

* **Network interface skapa:** Accelerated nätverk kan bara aktiveras för en ny nätverkskort. Det går inte att aktivera för en befintlig nätverkskort.
* **Skapa en virtuell dator:** A nätverkskortet med snabbare nätverksfunktioner som är aktiverad kan endast kopplas till en virtuell dator när den virtuella datorn skapas. Nätverkskortet kan inte kopplas till en befintlig virtuell dator. Om du lägger till den virtuella datorn i en befintlig tillgänglighetsuppsättning måste alla virtuella datorer i tillgänglighetsuppsättningen också ha snabbare nätverk som är aktiverad.
* **Distribution via Azure Resource Manager:** virtuella datorer (klassisk) kan inte distribueras med snabbare nätverk.

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
