---
title: "Virtuella Azure-nätverk och virtuella Windows-datorer | Microsoft Docs"
description: "Självstudiekurs – hantera virtuella Azure-nätverk och virtuella Windows-datorer med Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/12/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 21f2d586a4c468071bec55c65005b35baf323fe7
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Hantera virtuella Azure-nätverk och virtuella Windows-datorer med Azure PowerShell

Azure virtual machines använder Azure-nätverk för interna och externa nätverkskommunikation. Den här kursen går igenom hur du distribuerar två virtuella datorer och konfigurera Azure nätverk för dessa virtuella datorer. Exemplen i den här kursen förutsätter att de virtuella datorerna är värd för ett webbprogram med en-databas, men ett program inte har distribuerats i självstudiekursen. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och undernät
> * Skapa en offentlig IP-adress
> * Skapa en frontend virtuell dator
> * Skydda nätverkstrafik
> * Skapa backend-VM

Du kan se dessa resurser som skapas när den här kursen:

![Virtuellt nätverk med två undernät](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* -det virtuella nätverket som de virtuella datorerna använder för att kommunicera med varandra och internet.
- *myFrontendSubnet* -undernät i *myVNet* används av frontend resurserna.
- *myPublicIPAddress* -offentlig IP-adress som används för att få åtkomst till *myFrontendVM* från internet.
- *myFrontentNic* -nätverksgränssnittet som används av *myFrontendVM* att kommunicera med *myBackendVM*.
- *myFrontendVM* -den virtuella datorns används för att kommunicera mellan internet och *myBackendVM*.
- *myBackendNSG* -nätverkssäkerhetsgruppen som styr kommunikationen mellan den *myFrontendVM* och *myBackendVM*.
- *myBackendSubnet* -undernätet med *myBackendNSG* och används av backend-resurser.
- *myBackendNic* -nätverksgränssnittet som används av *myBackendVM* att kommunicera med *myFrontendVM*.
- *myBackendVM* -den virtuella datorns som använder port 1433 för att kommunicera med *myFrontendVM*.

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="vm-networking-overview"></a>VM-nätverk – översikt

Virtuella Azure-nätverk kan säkra nätverksanslutningar mellan virtuella datorer, internet och andra Azure-tjänster, till exempel Azure SQL-databas. Virtuella nätverk är uppdelad i logiska segment som kallas subnät. Undernät används för flödeskontroll för nätverk och som en säkerhetsgräns. När du distribuerar en virtuell dator innehåller vanligtvis ett virtuellt nätverksgränssnitt som är ansluten till ett undernät.

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och undernät

Ett enda virtuellt nätverk skapas med två undernät för den här kursen. Ett frontend undernät som värd för ett webbprogram och ett backend-undernät som värd för en databasserver.

Innan du kan skapa ett virtuellt nätverk, skapa en resurs med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myRGNetwork* i den *EastUS* plats:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Skapa undernät

Skapa en konfiguration av undernät med namnet *myFrontendSubnet* med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Skapa en konfiguration av undernät med namnet *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett VNET med namnet *myVNet* med *myFrontendSubnet* och *myBackendSubnet* med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Ett nätverk har nu skapats och upp i två undernät, en för frontend-tjänster och en för backend-tjänster. I nästa avsnitt, virtuella datorer skapas och anslutna till dessa undernät.

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

En offentlig IP-adress kan Azure-resurser ska vara tillgänglig på internet. Allokeringsmetod för offentliga IP-adressen kan konfigureras som dynamiska eller statiska. Som standard tilldelas dynamiskt offentlig IP-adress. Dynamiska IP-adresser släpps när en virtuell dator har frigjorts. Detta medför IP-adressen ändras under åtgärder som innehåller en VM-flyttningen.

Allokeringsmetoden kan anges som statisk, vilket garanterar att IP-adressen fortfarande kopplad till en virtuell dator, även under en frigjord tillstånd. Den IP-adressen kan inte anges när du använder en statiskt tilldelade IP-adress. Istället tilldelas den från en pool med tillgängliga adresser.

Skapa en offentlig IP-adress med namnet *myPublicIPAddress* med [ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Du kan ändra parametern - AllocationMethod att `Static` att tilldela en statisk offentlig IP-adress.

## <a name="create-a-front-end-vm"></a>Skapa en frontend virtuell dator

En virtuell dator, för att kommunicera på ett virtuellt nätverk måste den ha ett virtuellt nätverksgränssnitt (NIC). Skapa ett nätverkskort med [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Ange användarnamn och lösenord för administratörskontot på den virtuella datorn med hjälp av [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Du kan använda dessa autentiseringsuppgifter för att ansluta till den virtuella datorn i ytterligare åtgärder:

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa de virtuella datorerna med hjälp av [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [Set AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [Set AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [Lägga till AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface), och [nya AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="secure-network-traffic"></a>Skydda nätverkstrafik

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk (VNet). NSG: er kan vara kopplad till undernät eller individuella nätverksgränssnitt. När en NSG är associerad med ett nätverksgränssnitt gäller bara den associera virtuella datorn. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet.

### <a name="network-security-group-rules"></a>Regler för nätverkssäkerhetsgrupper

NSG-regler definiera nätverk portar under vilken trafik tillåts eller nekas. Regler kan innehålla käll- och IP-adressintervall så att trafik styrs mellan specifika system eller undernät. NSG-regler kan även innehålla en prioritet (mellan 1 – och 4096). Reglerna utvärderas prioritsordning. En regel med en prioritet på 100 utvärderas innan en regel med prioritet 200.

Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar.

- **Virtuellt nätverk** - trafik med ursprung och slutar med ett virtuellt nätverk tillåts både i inkommande och utgående riktningar.
- **Internet** - utgående trafik tillåts, men inkommande trafik blockeras.
- **Belastningsutjämnaren** -Tillåt Azure belastningsutjämnare avsökning hälsotillståndet för dina virtuella datorer och rollinstanser. Om du inte använder en belastningsutjämnad uppsättning, kan du åsidosätta den här regeln.

### <a name="create-network-security-groups"></a>Skapa säkerhetsgrupper för nätverk

Skapa en regel med namnet *myFrontendNSGRule* så att inkommande webbtrafik på *myFrontendVM* med [ny AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Du kan begränsa intern trafik till *myBackendVM* från endast *myFrontendVM* genom att skapa en NSG för backend-undernät. I följande exempel skapas en NSG regeln med namnet *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Lägg till en nätverkssäkerhetsgrupp med namnet *myFrontendNSG* med [ny AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Lägg till en nätverkssäkerhetsgrupp med namnet *myBackendNSG* med ny AzureRmNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Lägg till nätverkssäkerhetsgrupper i undernät:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Skapa en backend-virtuell dator

Det enklaste sättet att skapa backend-VM för den här kursen är med hjälp av en SQL Server-avbildning. Den här kursen kan du bara skapar den virtuella datorn med databasservern, men ger inte information om åtkomst till databasen.

Skapa *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Ange användarnamn och lösenord för administratörskontot på den virtuella datorn med Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa *myBackendVM*:

```azurepowershell-interactive
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016SP1-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

Den bild som används för SQL Server är installerad men används inte i den här kursen. Den ingår för att visa hur du kan konfigurera en virtuell dator för att hantera webbtrafik och en virtuell dator för att hantera databasen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen skapas och skyddas av Azure-nätverk som är relaterade till virtuella datorer. 

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och undernät
> * Skapa en offentlig IP-adress
> * Skapa en frontend virtuell dator
> * Skydda nätverkstrafik
> * Skapa en backend-virtuell dator

Gå vidare till nästa kurs vill veta mer om övervakning för att skydda data på virtuella datorer med hjälp av Azure-säkerhetskopiering.

> [!div class="nextstepaction"]
> [Säkerhetskopiera Windows-datorer i Azure](./tutorial-backup-vms.md)
