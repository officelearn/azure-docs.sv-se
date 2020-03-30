---
title: 'Virtuellt WAN: Skapa vägtabell för virtuella nav till NVA: Azure PowerShell'
description: Virtual WAN virtual hub route table för att styra trafiken till en virtuell nätverksinstallation.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: a55e1453fe7fe4d135286b22dabf58d434762581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645114"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Skapa en virtuell hubbvägtabell för att styra trafiken till en virtuell nätverksinstallation

Den här artikeln visar hur du styr trafiken från en virtuell hubb till en virtuell nätverksinstallation. 

![Virtual WAN-diagram](./media/virtual-wan-route-table/vwanroute.png)

I den här artikeln lär du dig hur du:

* Skapa WAN (Wide Area Network)
* Skapa en hubb
* Skapa virtuella nätverksanslutningar för nav
* Skapa en navväg
* Skapa en routningstabell
* Använda flödestabellen

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kontrollera att du har uppfyllt följande kriterier:

1. Du har en virtuell nätverksinstallation (NVA). Detta är en programvara från tredje part som du väljer som vanligtvis etableras från Azure Marketplace i ett virtuellt nätverk.
2. Du har tilldelat en privat IP till NVA-nätverksgränssnittet. 
3. NVA kan inte distribueras i det virtuella navet. Den måste distribueras i ett separat virtuella nätverk. För den här artikeln kallas NVA VNet för "DMZ VNet".
4. Den "DMZ VNet" kan ha en eller flera virtuella nätverk anslutna till den. I den här artikeln kallas detta virtuella nätverk för "Indirekt ekerVnet". Dessa virtuella nätverk kan anslutas till DMZ VNet med hjälp av VNet-peering.
5. Kontrollera att du redan har skapat 2 virtuella nätverk. Dessa kommer att användas som eker virtuella nätverk. I den här artikeln är VNet-ekeradressutrymmena 10.0.2.0/24 och 10.0.3.0/24. Om du behöver information om hur du skapar ett virtuellt nätverk läser du [Skapa ett virtuellt nätverk med PowerShell](../virtual-network/quick-create-powershell.md).
6. Se till att det inte finns några virtuella nätverksgateways i några virtuella nätverk.

## <a name="1-sign-in"></a><a name="signin"></a>1. Logga in

Kontrollera att du installerar den senaste versionen av PowerShell-cmdlets för Resurshanteraren. Mer information om hur du installerar PowerShell-cmdlets finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps). Detta är viktigt eftersom tidigare versioner av cmdletarna inte innehåller de aktuella värden som du behöver för den här övningen.

1. Öppna PowerShell-konsolen med förhöjda privilegier och logga in på ditt Azure-konto. Den här cmdleten uppmanar dig att ange inloggningsuppgifter. När du har loggat in hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Hämta en lista över dina Azure-prenumerationer.

   ```powershell
   Get-AzSubscription
   ```
3. Ange den prenumeration som du vill använda.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. Skapa resurser

1. Skapa en resursgrupp.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Skapa ett virtuellt WAN.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Skapa ett virtuellt nav.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. Skapa anslutningar

Skapa virtuella navnätverksanslutningar från indirekt eker-VNet och VPN DMZ till det virtuella navet.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. Skapa en virtuell hubbväg

I den här artikeln är de indirekta adressutrymmena för spoke 10.0.2.0/24 och 10.0.3.0/24 och dmz NVA-nätverksgränssnittet privat IP-adress är 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. Skapa en virtuell hubbvägstabell

Skapa en virtuell hubbvägstabell och använd sedan den skapade vägen på den.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6.

Genomför ändringarna i det virtuella navet.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
