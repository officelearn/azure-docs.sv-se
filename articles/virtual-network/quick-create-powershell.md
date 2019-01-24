---
title: Skapa ett virtuellt nätverk – snabbstart – Azure PowerShell
titlesuffix: Azure Virtual Network
description: I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Med ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer, kommunicera privat med varandra och med Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: jdial
ms.openlocfilehash: ade8329e6e42fae9f3232617488a6d4a69f8ef1f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437393"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av PowerShell

Med ett virtuellt nätverk kan Azure-resurser, exempelvis virtuella datorer, kommunicera privat med varandra och med Internet. I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket. Sedan ansluter du till de virtuella datorerna från Internet och kommunicerar privat i det virtuella nätverket.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt istället, så kräver den här snabbstarten att du använder version 5.4.1 eller senare av AzureRM PowerShell-modulen. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Information om hur installerar och uppgraderar finns i [Installera Azure PowerShell-modulen](/powershell/azure/azurerm/install-azurerm-ps).

Till sist, om du kör PowerShell lokalt måste du även köra `Connect-AzureRmAccount`. Kommandot skapar en anslutning med Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Skapa en resursgrupp och ett virtuellt nätverk

Det finns en handfull steg du måste gå igenom för att konfigurera din resursgrupp och virtuella nätverk.

### <a name="create-the-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som ska vara värd för det virtuella nätverket. Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I det här exemplet skapas ett virtuellt standardnätverk med namnet *myVirtualNetwork* på platsen *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Lägga till ett undernät

Azure distribuerar resurser till ett undernät i ett virtuellt nätverk, så du måste skapa ett undernät. Skapa en undernätskonfiguration med namnet *standard* med [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associera undernätet med det virtuella nätverket

Du kan skriva undernätskonfigurationen till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork). Det här kommandot skapar undernätet:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa den första virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). När du kör följande kommando måste du ange autentiseringsuppgifter. Ange ett användarnamn och lösenord för den virtuella datorn:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden. Du kan fortsätta till nästa steg.

När Azure börjar skapa den virtuella datorn i bakgrunden får du tillbaka något liknande detta:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Skapa den andra virtuella datorn med det här kommandot:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Du måste skapa en annan användare och lösenord. Det tar några minuter för Azure att skapa den virtuella datorn.

> [!IMPORTANT]
> Fortsätt inte med nästa steg förrän Azure har avslutat uppgiften.  Det hela är klart när Azure returnerar utdata till PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) för att returnera den offentliga IP-adressen för en virtuell dator. I det här exemplet returneras den offentliga IP-adressen för den virtuella datorn *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Öppna en kommandotolk på den lokala datorn. Kör `mstsc`-kommandot. Ersätt `<publicIpAddress>` med den offentliga IP-adress som returnerades från det senaste steget:

> [!NOTE]
> Om du har kört dessa kommandon från en PowerShell-kommandotolk på din lokala dator, och du använder AzureRM PowerShell-modulversion 5.4.1 eller senare, kan du fortsätta i det gränssnittet.

```cmd
mstsc /v:<publicIpAddress>
```

En fjärrskrivbordsprotokollsfil (*.rdp*) laddas ned till datorn och ett fjärrskrivbord öppnas.

1. Välj **Anslut** om du uppmanas att göra det.

1. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn.

    > [!NOTE]
    > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatsvarning. Om så är fallet väljer du **Ja** eller **Fortsätt**.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell på fjärrskrivbordet på *myVm1*.

1. Ange `ping myVm2`.

    Du får tillbaka något liknande detta:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudap
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Pinget misslyckas eftersom det använder Internet Control Message Protocol (ICMP). ICMP släpps som standard inte genom Windows-brandväggen.

1. Ange det efterföljande kommandot för att tillåta *myVm2* att pinga *myVm1* i ett senare skede:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Kommandot tillåter ICMP inkommande genom Windows-brandväggen.

1. Stäng fjärrskrivbordsanslutningen till *myVm1*.

1. Upprepa stegen i [Ansluta till en virtuell dator från Internet](#connect-to-a-vm-from-the-internet). Den här gången ansluter du till *myVm2*.

1. Från en kommandotolk på den virtuella datorn *myVm2* anger du `ping myvm1`.

    Du får tillbaka något liknande detta:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Du får svar från *myVm1*, eftersom du har tillåtit ICMP via Windows-brandväggen på den virtuella datorn *myVm1* i ett tidigare steg.

1. Stäng fjärrskrivbordsanslutningen till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resursgruppen och alla resurser den innehåller med [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. Du har anslutit till en virtuell dator från Internet och kommunicerat privat mellan den virtuella datorn och en annan virtuell dator. Läs mer om virtuella nätverksinställningar i [Hantera ett virtuellt nätverk](manage-virtual-network.md).

Azure tillåter obegränsad privat kommunikation mellan virtuella datorer. Azure tillåter som standard endast inkommande fjärrskrivbordsanslutningar till virtuella Windows-datorer från Internet. Mer information om hur du konfigurerar olika typer av nätverkskommunikation för virtuella datorer finns i självstudien [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md).
