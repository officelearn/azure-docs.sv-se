---
title: Självstudie – skapa och hantera virtuella Azure-nätverk för virtuella Windows-datorer | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure PowerShell för att skapa och hantera virtuella Azure-nätverk för virtuella Windows-datorer
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a13163949a52503f42642c109a4fd4c1dedd837f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Självstudie: Skapa och hantera virtuella Azure-nätverk för virtuella Windows-datorer med Azure PowerShell

Azures virtuella datorer använder Azure-nätverk för intern och extern nätverkskommunikation. Den här självstudien visar hur du distribuerar två virtuella datorer och konfigurerar Azure-nätverk för dem. Exemplen i den här självstudien förutsätter att de virtuella datorerna är värd för ett webbprogram med databasens serverdel, men något program behöver inte ha distribuerats i självstudien. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och ett undernät
> * Skapa en offentlig IP-adress
> * Skapa en virtuell dator för klientdelen
> * Skydda nätverkstrafiken
> * Skapa en virtuell dator för serverdelen

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt krävs Azure PowerShell-modulen version 5.7.0 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="vm-networking-overview"></a>Nätverksöversikt för VM

Med virtuella Azure-nätverk skyddas nätverksanslutningar mellan virtuella datorer, Internet och andra Azure-tjänster, till exempel Azures SQL-databas. Virtuella nätverk är uppdelade i logiska segment som kallas undernät. Undernät används för att kontrollera nätverksflödet och som en säkerhetsgräns. När du distribuerar en virtuell dator ingår vanligtvis ett virtuellt nätverksgränssnitt som är anslutet till ett undernät.

När du slutför den här självstudien kommer du att se att följande resurser skapas:

![Virtuellt nätverk med två undernät](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – Det virtuella nätverk som de virtuella datorerna använder för att kommunicera med varandra och Internet.
- *myFrontendSubnet* – Undernät i *myVNet* som används av klientdelsresurserna.
- *myPublicIPAddress* – Offentlig IP-adress som används för att få åtkomst till *myFrontendVM* från Internet.
- *myFrontentNic* – Nätverksgränssnitt som används av *myFrontendVM* till att kommunicera med *myBackendVM*.
- *myFrontendVM* – Den virtuella dator som används för att kommunicera mellan Internet och *myBackendVM*.
- *myBackendNSG* – Nätverkssäkerhetsgruppen som styr kommunikationen mellan *myFrontendVM* och *myBackendVM*.
- *myBackendSubnet* – Det undernät som är associerat med *myBackendNSG* och används av serverdelsresurserna.
- *myBackendNic* – Nätverksgränssnittet som används av *myBackendVM* till att kommunicera med *myFrontendVM*.
- *myBackendVM* – Den virtuella dator som använder port 1433 för att kommunicera med *myFrontendVM*.


## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

I den här självstudien skapas ett enda virtuellt nätverk med två undernät. Ett klientdelsundernät som är värd för ett webbprogram och ett serverdelsundernät som är värd för en databasserver.

Innan du kan skapa ett virtuellt nätverk, skapar du en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Följande exempel skapar en resursgrupp med namnet *myRGNetwork* på platsen *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Skapa undernätskonfigurationer

Skapa en undernätskonfiguration med namnet *myFrontendSubnet* med [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Och skapa en undernätskonfiguration med namnet *myBackendSubnet*:

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

Ett nätverk har nu skapats och delats upp i två undernät, ett för klientdelstjänster och ett för serverdelstjänster. I nästa avsnitt skapar vi virtuella datorer och ansluter dem till dessa undernät.

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Med en offentlig IP-adress blir Azure-resurser tillgängliga på Internet. Allokeringsmetoden för den offentliga IP-adressen kan konfigureras som dynamisk eller statisk. Som standard blir en offentlig IP-adress dynamiskt allokerad. Dynamiska IP-adresser släpps när de virtuella datorerna frigörs. Detta medför IP-adressen ändras vid åtgärder som innebär att en virtuell dator frigörs.

Allokeringsmetoden kan anges som statisk, vilket garanterar att IP-adressen förblir tilldelad till en virtuell dator, även när den frigjorts. Det går inte att ange IP-adressen när du använder en statiskt tilldelad IP-adress. I stället allokeras den från en pool med tillgängliga adresser.

Skapar en offentlig IP-adress med namnet *myPublicIPAddress* med [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Du kan ändra parametern -AllocationMethod för `Static` för att tilldela en statisk offentlig IP-adress.

## <a name="create-a-front-end-vm"></a>Skapa en virtuell dator för klientdelen

För att en virtuell dator ska kommunicera i ett virtuellt nätverk, behöver den ett virtuellt nätverksgränssnitt (NIC). Skapa ett nätverkskort med [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Ange användarnamn och lösenord för administratörskontot på den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Du använder autentiseringsuppgifterna för att ansluta till den virtuella datorn i ytterligare steg:

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Skydda nätverkstrafiken

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk (VNet). NSG:er kan vara associerade med undernät eller separata nätverksgränssnitt. När en NSG är associerad med ett nätverksgränssnitt, tillämpar den bara den associerade virtuella datorn. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet.

### <a name="network-security-group-rules"></a>Regler för nätverkssäkerhetsgrupp

NSG-regler definierar nätverksportar där trafik tillåts eller nekas. Reglerna kan innehålla käll- och målintervall för IP-adresser så att trafiken styrs mellan specifika system eller undernät. NSG-regler kan även innehålla en prioritet (mellan 1 och 4 096). Reglerna utvärderas i prioritetsordning. En regel med en prioritet på 100 utvärderas före en regel med prioritet 200.

Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar.

- **Virtuellt nätverk** – Trafik som kommer från eller som går till ett virtuellt nätverk tillåts både i inkommande och utgående riktning.
- **Internet** – Utgående trafik tillåts, men inkommande trafik blockeras.
- **Belastningsutjämnare** – Tillåter att Azures belastningsutjämnare avsöker hälsotillståndet för dina virtuella datorer och rollinstanser. Du kan åsidosätta den här regeln om du inte använder någon belastningsutjämnad uppsättning.

### <a name="create-network-security-groups"></a>Skapa nätverkssäkerhetsgrupper

Skapa en inkommande regel med namnet *myFrontendNSGRule* för att tillåta inkommande webbtrafik på *myFrontendVM* med [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

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

Du kan begränsa intern trafik till *myBackendVM* från endast *myFrontendVM* genom att skapa en NSG för serverdelsundernätet. Följande exempel skapar en NSG-regel med namnet *myLoadBalancerNSGRule*:

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

Lägg till en nätverkssäkerhetsgrupp med namnet *myFrontendNSG* med [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Lägg nu till en nätverkssäkerhetsgrupp med namnet *myBackendNSG* med New-AzureRmNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Lägg till nätverkssäkerhetsgrupper till undernäten:

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

## <a name="create-a-back-end-vm"></a>Skapa en virtuell dator för serverdelen

Det enklaste sättet att skapa den virtuella serverdelsdatorn för den här självstudien är med en SQL Server-avbildning. Den här självstudien skapar bara den virtuella datorn med databasservern, men ger inte information om åtkomst till databasen.

Skapa *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Ange användarnamn och lösenord för administratörskontot på den virtuella datorn med Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa *myBackendVM*.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Den avbildning som används har SQL Server installerad men det används inte i den här självstudien. Det ingår för att visa hur du kan konfigurera en virtuell dator för att hantera webbtrafik och en virtuell dator för att hantera databashantering.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat och skyddat Azure-nätverk som är relaterade till virtuella datorer. 

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och ett undernät
> * Skapa en offentlig IP-adress
> * Skapa en virtuell dator för klientdelen
> * Skydda nätverkstrafiken
> * Skapa en virtuell dator för serverdelen

Gå vidare till nästa självstudie för att lära dig om att övervaka och skydda data på virtuella datorer med Azure Backup.

> [!div class="nextstepaction"]
> [Säkerhetskopiera virtuella Windows-datorer i Azure](./tutorial-backup-vms.md)
