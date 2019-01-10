---
title: Skapa en Azure virtuellt WAN virtuella hubben routningstabellen för att undvika att NVA | Microsoft Docs
description: Virtuellt WAN virtuella hubben routningstabellen för att styra trafik till en virtuell nätverksenhet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 7d5cd8aab0f368ffec636e6dfcacf127c910dafc
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190178"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Skapa en virtuell hubb routningstabellen för att styra trafik till en virtuell nätverksinstallation

Den här artikeln visar hur du styra trafik från en virtuell hubb till en virtuell nätverksenhet. 

![Virtual WAN-diagram](./media/virtual-wan-route-table/vwanroute.png)

I den här artikeln lär du dig hur du:

* Skapa WAN (Wide Area Network)
* Skapa en hubb
* Skapa hubb virtuella nätverksanslutningar
* Skapa en hubb-väg
* Skapa en routningstabell
* Applicera routningstabellen

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor:

1. Du har en virtuell nätverksinstallation (NVA) är en programvara från tredje part som tillhandahålls vanligtvis från Azure Marketplace (länk) i ett virtuellt nätverk.
2. Du har en privat IP-adress som tilldelats nätverksgränssnittet NVA. 
3. Kan inte distribueras till NVA i den virtuella hubben. Den måste distribueras i ett separat virtuellt nätverk. I den här artikeln kallas det virtuella nätverket DMZ VNet.
4. DMZ VNet kan ha en eller flera virtuella nätverk som är anslutna till den. I den här artikeln kallas det här virtuella nätverket indirekt eker VNet. Dessa virtuella nätverk kan anslutas till DMZ VNet med VNet-peering.
5. Kontrollera att du har redan skapat på 2 virtuella nätverk. Dessa ska användas som virtuella ekernätverken. I den här artikeln är adressutrymmen för virtuellt nätverk eker 10.0.2.0/24 och 10.0.3.0/24. Om du behöver information om hur du skapar ett virtuellt nätverk finns i [skapa ett virtuellt nätverk med hjälp av PowerShell](../virtual-network/quick-create-powershell.md).
6. Se till att det finns inga virtuella nätverksgatewayer i alla virtuella nätverk.

## <a name="signin"></a>1. Logga in

Kontrollera att du installerar den senaste versionen av Resource Manager PowerShell-cmdletar. Mer information om hur du installerar PowerShell-cmdlets finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/azurerm/overview). Detta är viktigt eftersom tidigare versioner av cmdletarna inte innehåller de aktuella värden som du behöver för den här övningen. Moduler i dig följande exempel är Azure RM. Den här artikeln kommer att uppdateras till Azure-Az i framtiden.

1. Öppna PowerShell-konsolen med förhöjd behörighet och logga in på kontot. Denna cmdlet uppmanar dig autentiseringsuppgifter för inloggning. När du har loggat in hämtas dina kontoinställningar så att de är tillgängliga för Azure PowerShell.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Hämta en lista över dina Azure-prenumerationer.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Ange den prenumeration som du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```

## <a name="rg"></a>2. Skapa resurser

1. Skapa en resursgrupp.

  ```powershell
  New-AzureRmResourceGroup -Location "West US" -Name "testRG"
  ```
2. Skapa ett virtuellt WAN.

  ```powershell
  $virtualWan = New-AzureRmVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
  ```
3. Skapa en virtuell hubb.

  ```powershell
  New-AzureRmVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
  ```

## <a name="connections"></a>3. Skapa anslutningar

Skapa hub virtuella nätverksanslutningar från indirekt ekrar VNet och DMZ-VNet till den virtuella hubben.

  ```powershell
  $remoteVirtualNetwork1= Get-AzureRmVirtualNetwork -Name “indirectspoke1” -ResourceGroupName “testRG”
  $remoteVirtualNetwork2= Get-AzureRmVirtualNetwork -Name “indirectspoke2” -ResourceGroupName “testRG”
  $remoteVirtualNetwork3= Get-AzureRmVirtualNetwork -Name “dmzvnet” -ResourceGroupName “testRG”

  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection1” -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection2” -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection3” -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Skapa en virtuell hubb-väg

I den här artikeln adressutrymmen indirekt ekrar VNet är 10.0.2.0/24 och 10.0.3.0/24 och DMZ-NVA network interface privata IP-adressen är 10.0.4.5.

```powershell
$route1 = New-AzureRmVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Skapa en virtuell hubb routningstabell

Skapa en virtuell hubb routningstabell och avser det skapade flödet.
 
```powershell
$routeTable = New-AzureRmVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Spara ändringarna

Ändringarna i den virtuella hubben.

```powershell
Update-AzureRmVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub” -RouteTable $routeTable
```

## <a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
