---
title: Självstudie – Skapa en privat Azure DNS-zon med hjälp av Azure PowerShell
description: I den här självstudien skapar och testar du en privat DNS-zon och post i Azure DNS. Det här är en steg-för-steg-guide om hur du skapar och hanterar din första privata DNS-zon och DNS-post med Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: b4d75c7a6db89b19d88cddcc564fd4e6a9ad0f49
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770468"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Självstudier: Skapa en privat Azure DNS-zon med hjälp av Azure PowerShell

Den här självstudien visar hur du skapar din första privata DNS-zon och DNS-post med Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  De här kallas *virtuella lösningsnätverk*. Du kan även ange ett virtuellt nätverk där Azure DNS ska bibehålla värddatorposter varje gång en virtuell dator skapas, ändrar IP-adress eller tas bort.  Det här kallas *virtuellt registreringsnätverk*.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en privat DNS-zon
> * Skapa virtuella testdatorer
> * Skapa en ytterligare DNS-post
> * Testa den privata zonen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill kan du slutföra den här självstudiekursen med [Azure CLI](private-dns-getstarted-cli.md).

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Först skapar du en resursgrupp som ska innehålla DNS-zonen: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Skapa en privat DNS-zon

Du skapar en DNS-zon med hjälp av `New-AzDnsZone` -cmdleten med värdet *Private* för parametern **ZoneType**. I följande exempel skapas en DNS-zon med namnet **private.contoso.com** i resursgruppen med namnet **MyAzureResourceGroup** och gör DNS-zon som är tillgängliga för det virtuella nätverk som kallas  **MyAzureVnet**.

Om parametern **ZoneType** utelämnas skapas zonen som en offentlig zon. Med andra ord krävs den om du vill skapa en privat zon. 

```azurepowershell
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Om du vill skapa en zon bara för namnmatchning (ingen automatisk generering av värddatornamn) använder du parametern *ResolutionVirtualNetworkId* i stället för parametern *RegistrationVirtualNetworkId*.

> [!NOTE]
> Du kommer inte att kunna se de automatiskt skapade posterna för värddatornamn. Senare kommer du dock att testa för att säkerställa att de finns.

### <a name="list-dns-private-zones"></a>Lista privata DNS-zoner

Genom att utelämna zonnamnet från `Get-AzDnsZone` kan du räkna upp alla zoner i en resursgrupp. Den här åtgärden returnerar en matris med zonobjekt.

```azurepowershell
Get-AzDnsZone -ResourceGroupName MyAzureResourceGroup
```

Genom att utelämna både zonnamnet och resursgruppsnamnet från `Get-AzDnsZone` kan du räkna upp alla zoner i Azure-prenumerationen.

```azurepowershell
Get-AzDnsZone
```

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Det här kan ta några minuter.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

Du skapar postuppsättningar med hjälp av cmdleten `New-AzDnsRecordSet`. I följande exempel skapas en post med det relativa namnet **db** i DNS-zonen **private.contoso.com**, i resursgruppen **MyAzureResourceGroup**. Det fullständigt kvalificerade namnet på postuppsättningen är **db.private.contoso.com**. Posttypen är ”A” med IP-adressen ”10.2.0.4”, och TTL är 3600 sekunder.

```azurepowershell
New-AzDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Visa DNS-poster

Om du vill visa en lista med DNS-poster i din zon kör du:

```azurepowershell
Get-AzDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```
Kom ihåg att du inte automatiskt kommer att se den skapade A-posten för dina två virtuella testdatorer.

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namnmatchning för din **private.contoso.com** privat zon.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurera virtuella datorer för att tillåta inkommande ICMP

Du kan använda ping-kommandot för att testa namnmatchning. Konfigurera därför brandväggen på båda de virtuella datorerna att tillåta inkommande ICMP-paket.

1. Anslut till myVM01 och öppna ett Windows PowerShell-fönster med administratörsbehörighet.
2. Kör följande kommando:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Upprepa för myVM02.

### <a name="ping-the-vms-by-name"></a>Pinga de virtuella datorerna efter namn

1. Från Windows PowerShell-kommandotolken för myVM02 pingar du myVM01 med hjälp av det automatiskt registrerade värddatornamnet:
   ```
   ping myVM01.private.contoso.com
   ```
   Du bör se utdata som liknar följande:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Nu pingar du det **db**-namn som du skapade tidigare:
   ```
   ping db.private.contoso.com
   ```
   Du bör se utdata som liknar följande:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Ta bort alla resurser

När resursgruppen **MyAzureResourceGroup** inte längre behövs kan du ta bort den för att ta bort de resurser som skapades i den här självstudien.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en privat DNS-zon, skapade en DNS-post och testade zonen.
Härnäst kan du läsa mer om privata DNS-zoner.

> [!div class="nextstepaction"]
> [Använda Azure DNS för privata domäner](private-dns-overview.md)




