---
title: 'Azure VPN Gateway: Ta bort en gateway: PowerShell'
description: Ta bort en virtuell nätverksgateway med PowerShell i distributionsmodellen för Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162114"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Ta bort en virtuell nätverksgateway med PowerShell
> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Powershell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassisk)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Det finns ett par olika metoder som du kan ta när du vill ta bort en virtuell nätverksgateway för en VPN-gatewaykonfiguration.

- Om du vill ta bort allt och börja om, som i fallet med en testmiljö, kan du ta bort resursgruppen. När du tar bort en resursgrupp tas alla resurser i gruppen bort. Den här metoden rekommenderas bara om du inte vill behålla någon av resurserna i resursgruppen. Du kan inte selektivt ta bort bara ett fåtal resurser med den här metoden.

- Om du vill behålla några av resurserna i resursgruppen blir det något mer komplicerat att ta bort en virtuell nätverksgateway. Innan du kan ta bort den virtuella nätverksgatewayen måste du först ta bort alla resurser som är beroende av gatewayen. Vilka steg du följer beror på vilken typ av anslutningar du har skapat och de beroende resurserna för varje anslutning.

## <a name="before-beginning"></a>Innan du börjar



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Hämta de senaste PowerShell-cmdletsen i Azure Resource Manager.

Hämta och installera den senaste versionen av Azure Resource Manager PowerShell-cmdlets. Mer information om hur du hämtar och installerar PowerShell-cmdletar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Anslut till ditt Azure-konto.

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
```

Kontrollera prenumerationerna för kontot.

```powershell
Get-AzSubscription
```

Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Ta bort en VPN-gateway för plats till plats

Om du vill ta bort en virtuell nätverksgateway för en S2S-konfiguration måste du först ta bort varje resurs som hör till den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan måste några av värdena anges, medan andra värden är ett utdataresultat. Vi använder följande specifika värden i exemplen för demonstrationsändamål:

VNet-namn: VNet1<br>
Namn på resursgrupp: RG1<br>
Namn på virtuell nätverksgateway: GW1<br>

Följande steg gäller för resurshanterarens distributionsmodell.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgateway som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Kontrollera om den virtuella nätverksgatewayen har några anslutningar.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Ta bort alla anslutningar.

Du kan bli ombedd att bekräfta borttagningen av var och en av anslutningarna.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Ta bort den virtuella nätverksgatewayen.

Du kan bli ombedd att bekräfta borttagningen av gatewayen. Om du har en P2S-konfiguration till det här virtuella nätverket utöver S2S-konfigurationen kopplas alla P2S-klienter från att ta bort den virtuella nätverksgatewayen automatiskt utan förvarning.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu har din virtuella nätverksgateway tagits bort. Du kan använda nästa steg för att ta bort resurser som inte längre används.

### <a name="5-delete-the-local-network-gateways"></a>5 Ta bort de lokala nätverksgatewayerna.

Hämta listan över motsvarande lokala nätverksgateways.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Ta bort de lokala nätverksgatewayerna. Du kan bli ombedd att bekräfta borttagningen av var och en av den lokala nätverksgatewayen.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Ta bort de offentliga IP-adressresurserna.

Hämta IP-konfigurationerna för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adressresurser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort de offentliga IP-resurserna.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Ta bort gateway-undernätet och ställ in konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Ta bort en VPN-gateway för VNet-till-VNet

Om du vill ta bort en virtuell nätverksgateway för en V2V-konfiguration måste du först ta bort varje resurs som hör till den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan måste några av värdena anges, medan andra värden är ett utdataresultat. Vi använder följande specifika värden i exemplen för demonstrationsändamål:

VNet-namn: VNet1<br>
Namn på resursgrupp: RG1<br>
Namn på virtuell nätverksgateway: GW1<br>

Följande steg gäller för resurshanterarens distributionsmodell.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgateway som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Kontrollera om den virtuella nätverksgatewayen har några anslutningar.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Det kan finnas andra anslutningar till den virtuella nätverksgatewayen som ingår i en annan resursgrupp. Sök efter ytterligare anslutningar i varje ytterligare resursgrupp. I det här exemplet söker vi efter anslutningar från RG2. Kör detta för varje resursgrupp som du har som kan ha en anslutning till den virtuella nätverksgatewayen.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Få listan över anslutningar i båda riktningarna.

Eftersom det här är en VNet-till-VNet-konfiguration behöver du listan över anslutningar i båda riktningarna.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
I det här exemplet söker vi efter anslutningar från RG2. Kör detta för varje resursgrupp som du har som kan ha en anslutning till den virtuella nätverksgatewayen.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Ta bort alla anslutningar.

Du kan bli ombedd att bekräfta borttagningen av var och en av anslutningarna.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Ta bort den virtuella nätverksgatewayen.

Du kan bli ombedd att bekräfta borttagningen av den virtuella nätverksgatewayen. Om du har P2S-konfigurationer till dina virtuella nätverk utöver din V2V-konfiguration kopplar du automatiskt från alla P2S-klienter utan förvarning om du tar bort de virtuella nätverksgatewayerna.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu har din virtuella nätverksgateway tagits bort. Du kan använda nästa steg för att ta bort resurser som inte längre används.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Ta bort de offentliga IP-adressresurserna

Hämta IP-konfigurationerna för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adressresurser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort de offentliga IP-resurserna. Du kan uppmanas att bekräfta borttagningen av den offentliga IP-adressen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Ta bort gateway-undernätet och ställ in konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Ta bort en VPN-gateway för punkt till plats

Om du vill ta bort en virtuell nätverksgateway för en P2S-konfiguration måste du först ta bort varje resurs som hör till den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan måste några av värdena anges, medan andra värden är ett utdataresultat. Vi använder följande specifika värden i exemplen för demonstrationsändamål:

VNet-namn: VNet1<br>
Namn på resursgrupp: RG1<br>
Namn på virtuell nätverksgateway: GW1<br>

Följande steg gäller för resurshanterarens distributionsmodell.


>[!NOTE]
> När du tar bort VPN-gatewayen kopplas alla anslutna klienter bort från det virtuella nätverket utan förvarning.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgateway som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Ta bort den virtuella nätverksgatewayen.

Du kan bli ombedd att bekräfta borttagningen av den virtuella nätverksgatewayen.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu har din virtuella nätverksgateway tagits bort. Du kan använda nästa steg för att ta bort resurser som inte längre används.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Ta bort de offentliga IP-adressresurserna

Hämta IP-konfigurationerna för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adresser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort de offentliga IPs. Du kan uppmanas att bekräfta borttagningen av den offentliga IP-adressen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Ta bort gateway-undernätet och ställ in konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Ta bort en VPN-gateway genom att ta bort resursgruppen

Om du inte är orolig för att behålla någon av dina resurser i resursgruppen och bara vill börja om från början kan du ta bort en hel resursgrupp. Detta är ett snabbt sätt att ta bort allt. Följande steg gäller endast för resurshanterarens distributionsmodell.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Få en lista över alla resursgrupper i din prenumeration.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Leta reda på den resursgrupp som du vill ta bort.

Leta reda på den resursgrupp som du vill ta bort och visa listan över resurser i resursgruppen. I exemplet är namnet på resursgruppen RG1. Ändra exemplet för att hämta en lista över alla resurser.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Kontrollera resurserna i listan.

När listan returneras granskar du den för att kontrollera att du vill ta bort alla resurser i resursgruppen samt själva resursgruppen. Om du vill behålla några av resurserna i resursgruppen följer du stegen i de tidigare avsnitten i den här artikeln för att ta bort gatewayen.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Ta bort resursgruppen och resurserna.

Om du vill ta bort resursgruppen och alla resurser som finns i resursgruppen ändrar du exemplet och kör.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Kontrollera status.

Det tar lite tid för Azure att ta bort alla resurser. Du kan kontrollera resursgruppens status med hjälp av den här cmdleten.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Resultatet som returneras visar "Lyckades".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
