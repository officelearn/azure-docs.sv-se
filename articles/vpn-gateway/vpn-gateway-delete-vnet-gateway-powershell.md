---
title: 'Ta bort en virtuell nätverksgateway: PowerShell: Azure Resource Manager | Microsoft Docs'
description: Ta bort en virtuell nätverksgateway med hjälp av PowerShell i Resource Manager-distributionsmodellen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.openlocfilehash: 922aa739a42eddbe8cd7e3cabe46681c0c2c6d46
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417082"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Ta bort en virtuell nätverksgateway med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassisk)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Det finns ett par olika metoder som du kan använda när du vill ta bort en virtuell nätverksgateway för en VPN-gateway-konfiguration.

- Om du vill ta bort allt innehåll och börja om från början, som i fallet med en testmiljö kan du ta bort resursgruppen. När du tar bort en resursgrupp tas alla resurser i gruppen bort. Det här är metoden rekommenderas endast om du inte vill behålla någon av resurserna i resursgruppen. Du kan inte selektivt Radera endast några resurser med hjälp av den här metoden.

- Om du vill behålla några av resurserna i resursgruppen blir tar du bort en virtuell nätverksgateway något mer komplicerad. Innan du kan ta bort den virtuella nätverksgatewayen, måste du först radera alla resurser som är beroende av gatewayen. De steg du följer beror på vilken typ av anslutningar som du skapade och alla beroende resurser för varje anslutning.

## <a name="before-beginning"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Ladda ned de senaste Azure Resource Managers PowerShell-cmdletarna.

Hämta och installera den senaste versionen av Azure Resource Managers PowerShell-cmdlet: ar. Mer information om hämtning och installation av PowerShell-cmdlets finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Anslut till ditt Azure-konto.

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
```

Kontrollera prenumerationerna för kontot.

```powershell
Get-AzSubscription
```

Om du har mer än en prenumeration kan du ange den prenumeration som du vill använda.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Ta bort en plats-till-plats-VPN-gateway

Om du vill ta bort en virtuell nätverksgateway för en S2S-konfiguration, måste du först ta bort varje resurs som gäller för den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan, några av måste värdena anges, medan andra värden är ett resultat i utdata. Vi använder följande specifika värden i exemplen i demonstrationssyfte:

Namn på virtuellt nätverk: VNet1<br>
Resursgruppnamn: RG1<br>
Det virtuella nätverkets gateway name: GW1<br>

Följande steg gäller för Resource Manager-distributionsmodellen.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Kontrollera om den virtuella nätverksgatewayen har några anslutningar.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Ta bort alla anslutningar.

Du kan uppmanas att bekräfta borttagningen av var och en av anslutningarna.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Ta bort den virtuella nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av gatewayen. Om du har en P2S-konfiguration till den här virtuella nätverket utöver din S2S-konfiguration, bort tar bort den virtuella nätverksgatewayen automatiskt alla P2S-klienter utan varning skapas.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu kan har din virtuella nätverksgateway tagits bort. Du kan använda nästa steg att ta bort alla resurser som används inte längre.

### <a name="5-delete-the-local-network-gateways"></a>5 ta bort de lokala nätverksgatewayerna.

Hämta listan över de motsvarande lokala nätverksgatewayerna.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Ta bort de lokala nätverksgatewayerna. Du kan uppmanas att bekräfta borttagningen av var och en av den lokala nätverksgatewayen.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Ta bort offentlig IP-adressresurser.

Hämta IP-konfigurationer för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adressresurser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort offentlig IP-resurser.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Ta bort gateway-undernätet och ange konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Ta bort en VNet-till-VNet VPN-gateway

Om du vill ta bort en virtuell nätverksgateway för en V2V-konfiguration, måste du först ta bort varje resurs som gäller för den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan, några av måste värdena anges, medan andra värden är ett resultat i utdata. Vi använder följande specifika värden i exemplen i demonstrationssyfte:

Namn på virtuellt nätverk: VNet1<br>
Resursgruppnamn: RG1<br>
Det virtuella nätverkets gateway name: GW1<br>

Följande steg gäller för Resource Manager-distributionsmodellen.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Kontrollera om den virtuella nätverksgatewayen har några anslutningar.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Det kan finnas andra anslutningar till den virtuella nätverksgatewayen som ingår i en annan resursgrupp. Sök efter ytterligare anslutningar i varje ytterligare resursgrupp. I det här exemplet kontrollerar för anslutningar från RG2. Kör detta för varje resursgrupp att du har som kan ha en anslutning till den virtuella nätverksgatewayen.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Hämta listan över anslutningar i båda riktningarna.

Eftersom detta är en VNet-till-VNet-konfiguration måste listan över anslutningar i båda riktningarna.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
I det här exemplet kontrollerar för anslutningar från RG2. Kör detta för varje resursgrupp att du har som kan ha en anslutning till den virtuella nätverksgatewayen.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Ta bort alla anslutningar.

Du kan uppmanas att bekräfta borttagningen av var och en av anslutningarna.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Ta bort den virtuella nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av den virtuella nätverksgatewayen. Om du har P2S konfigurationer till dina virtuella nätverk utöver V2V konfigurationen bort tar bort de virtuella nätverksgatewayerna automatiskt alla P2S-klienter utan varning skapas.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu kan har din virtuella nätverksgateway tagits bort. Du kan använda nästa steg att ta bort alla resurser som används inte längre.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Ta bort offentlig IP-adressresurser

Hämta IP-konfigurationer för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adressresurser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort offentlig IP-resurser. Du kan uppmanas att bekräfta borttagningen av den offentliga IP-Adressen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Ta bort gateway-undernätet och ange konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Ta bort en punkt-till-plats-VPN-gateway

Om du vill ta bort en virtuell nätverksgateway för en P2S-konfiguration, måste du först ta bort varje resurs som gäller för den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan, några av måste värdena anges, medan andra värden är ett resultat i utdata. Vi använder följande specifika värden i exemplen i demonstrationssyfte:

Namn på virtuellt nätverk: VNet1<br>
Resursgruppnamn: RG1<br>
Det virtuella nätverkets gateway name: GW1<br>

Följande steg gäller för Resource Manager-distributionsmodellen.


>[!NOTE]
> När du tar bort en VPN-gateway kommer att kopplas från alla anslutna klienter från det virtuella nätverket utan varning.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Ta bort den virtuella nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av den virtuella nätverksgatewayen.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu kan har din virtuella nätverksgateway tagits bort. Du kan använda nästa steg att ta bort alla resurser som används inte längre.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Ta bort offentlig IP-adressresurser

Hämta IP-konfigurationer för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adresser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort den offentliga IP-adresser. Du kan uppmanas att bekräfta borttagningen av den offentliga IP-Adressen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Ta bort gateway-undernätet och ange konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Ta bort en VPN-gateway genom att ta bort resursgruppen.

Om du inte är orolig att behålla någon av dina resurser i resursgruppen och du bara vill börja om från början, kan du ta bort en hela resursgruppen. Det här är ett snabbt sätt att ta bort allt. Följande steg gäller endast för Resource Manager-distributionsmodellen.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Hämta en lista över alla resursgrupper i din prenumeration.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Leta upp den resursgrupp som du vill ta bort.

Leta upp den resursgrupp som du vill ta bort och visa listan över resurser i resursgruppen. I det här exemplet är namnet på resursgruppen RG1. Ändra exemplet om du vill hämta en lista över alla resurser.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Kontrollera resurserna i listan.

När listan returneras kan du granska den för att kontrollera att du vill ta bort alla resurser i resursgruppen, samt själva resursgruppen. Om du vill behålla några av resurserna i resursgruppen du använda stegen i de tidigare avsnitten i den här artikeln för att ta bort din gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Ta bort resursgruppen och resurser.

Ta bort resursgruppen och alla resurser i resursgruppen genom att ändra exemplet och kör.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Kontrollera status.

Det tar lite tid för Azure för att ta bort alla resurser. Du kan kontrollera status för en resursgrupp med hjälp av denna cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Det resultat som returneras visas ”lyckades”.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
