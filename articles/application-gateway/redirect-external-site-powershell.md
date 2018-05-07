---
title: Skapa en Programgateway med externa omdirigering - Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar en Programgateway som omdirigerar Internet-trafik till en extern plats med hjälp av Azure Powershell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: ae3fcc65d3a377d3317e632f28a43263e1d1fce2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-external-redirection-using-azure-powershell"></a>Skapa en Programgateway med externa genom att använda Azure PowerShell

Du kan använda Azure Powershell för att konfigurera [web trafik omdirigering](multiple-site-overview.md) när du skapar en [Programgateway](overview.md). I kursen får konfigurera du en lyssnare och en regel som omdirigerar Internet-trafik som når Programgateway till en extern plats.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en lyssnare och omdirigering av regel
> * Skapa en programgateway

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Om du vill ta reda på vilken version du kör ` Get-Module -ListAvailable AzureRM` . Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Skapa en Azure-resurs med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Skapa undernätskonfiguration *myAGSubnet* med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Skapa ett virtuellt nätverk med namnet *myVNet* med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) med undernätskonfiguration. Slutligen, skapa en offentlig IP-adress med hjälp av [ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Dessa resurser används för att ge nätverksanslutning till gateway för programmet och dess associerade resurser.

```azurepowershell-interactive
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Skapa IP-konfigurationer och klientdelsport

Associera *myAGSubnet* som du skapade tidigare till programmet gateway med [ny AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Tilldela den offentliga IP-adressen till den gateway som använder [ny AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig). Och du kan sedan skapa en HTTP-porten med hjälp av [ny AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Skapa serverdelspool och inställningar

Skapa serverdelspoolen med namnet *defaultPool* för programmet gateway med [ny AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Konfigurera inställningarna för pool med [ny AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name defaultPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-rule"></a>Skapa lyssnare och regel

En lyssnare krävs för att aktivera Programgateway att dirigera trafik på lämpligt sätt. Skapa lyssnare med [ny AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) med klientdel konfiguration och klientdelsport som du skapade tidigare. Det krävs en regel för lyssnaren ska kunna skicka inkommande trafik. Skapa en enkel regel med namnet *redirectRule* med [ny AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultListener = New-AzureRmApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$redirectConfig = New-AzureRmApplicationGatewayRedirectConfiguration `
  -Name myredirect `
  -RedirectType Temporary `
  -TargetUrl "http://bing.com"
$redirectRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name redirectRule `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -RedirectConfiguration $redirectConfig
```

### <a name="create-the-application-gateway"></a>Skapa programgatewayen

Nu när du har skapat de nödvändiga stödresurser ange parametrar för Programgateway med namnet *myAppGateway* med [ny AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), och sedan skapa den med hjälp av [ Nya AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $redirectRule `
  -RedirectConfigurations $redirectConfig `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan använda [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) att hämta den offentliga IP-adressen för programgatewayen. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

Du bör se *bing.com* visas i webbläsaren.

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en lyssnare och omdirigering av regel
> * Skapa en programgateway