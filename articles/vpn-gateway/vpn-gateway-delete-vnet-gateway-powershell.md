---
title: 'Ta bort en virtuell nätverksgateway: PowerShell: Azure Resource Manager | Microsoft Docs'
description: Ta bort en virtuell nätverksgateway med PowerShell i Resource Manager-distributionsmodellen.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: cherylmc
ms.openlocfilehash: c7e536b62ecaa374a278aeb8d18ef39489675711
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Ta bort en virtuell nätverksgateway med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassisk)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Det finns ett par olika metoder som du kan använda när du vill ta bort en virtuell nätverksgateway för en konfiguration för VPN-gateway.

- Om du vill ta bort allt och börja om från början, som i fallet med en testmiljö kan du ta bort resursgruppen. När du tar bort en resursgrupp tar bort alla resurser i gruppen. Det här är metod rekommenderas endast om du inte vill behålla några resurser i resursgruppen. Du kan inte selektivt Radera endast några resurser med hjälp av den här metoden.

- Om du vill behålla vissa av resurserna i resursgruppen blir tar bort en virtuell nätverksgateway något mer komplicerad. Innan du kan ta bort den virtuella nätverksgatewayen, måste du först ta bort alla resurser som är beroende av denna gateway. De steg du följer beror på vilken typ av anslutningar som du skapade och beroende resurser för varje anslutning.

## <a name="before-beginning"></a>Innan du börjar

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Hämta det senaste Azure Resource Manager PowerShell-cmdlet.

Hämta och installera den senaste versionen av Azure Resource Manager PowerShell-cmdlets. Mer information om hämtning och installation av PowerShell-cmdlets finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Ansluta till ditt Azure-konto.

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Login-AzureRmAccount
```

Kontrollera prenumerationerna för kontot.

```powershell
Get-AzureRmSubscription
```

Om du har mer än en prenumeration kan du ange den prenumeration som du vill använda.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Ta bort en plats-till-plats VPN-gateway

Du måste ta bort varje resurs som gäller för den virtuella nätverksgatewayen för att ta bort en virtuell nätverksgateway för en S2S-konfiguration. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan, vissa av måste värdena anges, medan andra värden är ett resultat i utdata. Vi använder följande specifika värden i exemplen i exempelsyfte:

VNet-name: VNet1<br>
Resursgruppens namn: RG1<br>
Gateway för virtuella nätverksnamnet: GW1<br>

Följande steg gäller för Resource Manager-distributionsmodellen.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Kontrollera om den virtuella nätverksgatewayen har alla anslutningar.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Ta bort alla anslutningar.

Du kan uppmanas att bekräfta borttagningen av anslutning.

```powershell
$Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Ta bort den virtuella nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av gatewayen. Om du har en P2S-konfiguration till detta virtuella nätverk utöver konfigurationen S2S bort tar bort virtuell nätverksgateway automatiskt alla P2S-klienter utan varning skapas.


```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu är har din virtuella nätverksgateway tagits bort. Du kan använda i nästa steg att ta bort alla resurser som används inte längre.

### <a name="5-delete-the-local-network-gateways"></a>5 ta bort de lokala nätverksgatewayerna.

Hämta listan över motsvarande lokala nätverksgatewayer.

```powershell
$LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Ta bort de lokala nätverksgatewayerna. Du kan uppmanas att bekräfta borttagningen av var och en av den lokala nätverksgatewayen.

```powershell
$LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Ta bort resurser för offentlig IP-adress.

Hämta IP-konfigurationer för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentlig IP-adressresurser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort offentlig IP-resurser.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Ta bort gateway-undernät och ställer in konfigurationen.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Ta bort en VNet-till-VNet VPN-gateway

Du måste ta bort varje resurs som gäller för den virtuella nätverksgatewayen för att ta bort en virtuell nätverksgateway för en V2V-konfiguration. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan, vissa av måste värdena anges, medan andra värden är ett resultat i utdata. Vi använder följande specifika värden i exemplen i exempelsyfte:

VNet-name: VNet1<br>
Resursgruppens namn: RG1<br>
Gateway för virtuella nätverksnamnet: GW1<br>

Följande steg gäller för Resource Manager-distributionsmodellen.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Kontrollera om den virtuella nätverksgatewayen har alla anslutningar.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Det kan finnas andra anslutningar till den virtuella nätverksgatewayen som ingår i en annan resursgrupp. Sök efter ytterligare anslutningar i varje ytterligare resursgrupp. I det här exemplet kontrollerar för anslutningar från RG2. Kör detta för varje resursgrupp som du har som kan ha en anslutning till den virtuella nätverksgatewayen.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Hämta listan över anslutningar i båda riktningarna.

Eftersom detta är en VNet-till-VNet-konfiguration måste listan över anslutningar i båda riktningarna.

```powershell
$ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
I det här exemplet kontrollerar för anslutningar från RG2. Kör detta för varje resursgrupp som du har som kan ha en anslutning till den virtuella nätverksgatewayen.

```powershell
 $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Ta bort alla anslutningar.

Du kan uppmanas att bekräfta borttagningen av anslutning.

```powershell
$ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Ta bort den virtuella nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av den virtuella nätverksgatewayen. Om du har P2S konfigurationer till ditt Vnet förutom V2V-konfiguration bort om du tar bort de virtuella nätverksgatewayerna automatiskt alla P2S-klienter utan varning skapas.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu är har din virtuella nätverksgateway tagits bort. Du kan använda i nästa steg att ta bort alla resurser som används inte längre.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Ta bort offentlig IP-adressresurser

Hämta IP-konfigurationer för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentlig IP-adressresurser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort offentlig IP-resurser. Du kan uppmanas att bekräfta borttagningen av offentlig IP.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Ta bort gateway-undernät och ställer in konfigurationen.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Ta bort en punkt-till-plats VPN-gateway

Om du vill ta bort en virtuell nätverksgateway för en P2S-konfiguration, måste du först ta bort varje resurs som gäller för den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan, vissa av måste värdena anges, medan andra värden är ett resultat i utdata. Vi använder följande specifika värden i exemplen i exempelsyfte:

VNet-name: VNet1<br>
Resursgruppens namn: RG1<br>
Gateway för virtuella nätverksnamnet: GW1<br>

Följande steg gäller för Resource Manager-distributionsmodellen.


>[!NOTE]
> När du tar bort VPN-gateway kommer att kopplas från alla anslutna klienter från VNet utan varning.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Ta bort den virtuella nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av den virtuella nätverksgatewayen.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Nu är har din virtuella nätverksgateway tagits bort. Du kan använda i nästa steg att ta bort alla resurser som används inte längre.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Ta bort offentlig IP-adressresurser

Hämta IP-konfigurationer för den virtuella nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adresser som används för den här virtuella nätverksgatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort den offentliga IP-adresser. Du kan uppmanas att bekräfta borttagningen av offentlig IP.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Ta bort gateway-undernät och ställer in konfigurationen.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Ta bort en VPN-gateway genom att ta bort resursgruppen.

Om du inte vill inte behålla någon av dina resurser i resursgruppen och du vill börja om från början, kan du ta bort en hela resursgruppen. Detta är ett snabbt sätt att ta bort allt. Följande steg gäller endast för Resource Manager-distributionsmodellen.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Hämta en lista över alla resursgrupper i din prenumeration.

```powershell
Get-AzureRmResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Leta upp den resursgrupp som du vill ta bort.

Leta upp den resursgrupp som du vill ta bort och visa listan över resurser i resursgruppen. I det här exemplet är namnet på resursgruppen RG1. Ändra exempel för att hämta en lista över alla resurser.

```powershell
Find-AzureRmResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Kontrollera resurserna i listan.

När listan returneras kan du granska den för att verifiera att du vill ta bort alla resurser i resursgruppen som resursgruppen. Om du vill behålla vissa av resurserna i resursgruppen använder du stegen i de föregående avsnitten i den här artikeln för att ta bort din gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Ta bort resursgruppen och resurserna.

Ändra exemplet och kör om du vill ta bort resursgruppen och alla resurser i resursgruppen.

```powershell
Remove-AzureRmResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Kontrollera status.

Det tar en stund innan Azure för att ta bort alla resurser. Du kan kontrollera statusen för din resursgrupp med denna cmdlet.

```powershell
Get-AzureRmResourceGroup -ResourceGroupName RG1
```

Det resultat som returneras visar ”lyckades”.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
