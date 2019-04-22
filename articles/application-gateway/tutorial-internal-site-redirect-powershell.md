---
title: Skapa en Programgateway med intern omdirigering – Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar en Programgateway som omdirigerar interna Internet-trafik till korrekta serverdels-pooler för servrar med hjälp av Azure Powershell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: victorh
ms.openlocfilehash: 3b13e75694f17a8ea22fee1f025692b9c64d8fa4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877500"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-azure-powershell"></a>Skapa en Programgateway med intern omdirigering med Azure PowerShell

Du kan använda Azure Powershell för att konfigurera [web traffic omdirigering](application-gateway-multi-site-overview.md) när du skapar en [Programgateway](application-gateway-introduction.md). I den här självstudien definierar du en serverdelspool med hjälp av en skalningsuppsättning för virtuella datorer. Du sedan konfigurera lyssnare och regler baserat på domäner som du äger för att se till att Internet-trafik anländer till i poolen. Den här självstudien förutsätter att du äger flera domäner och använder exempel på *www\.contoso.com* och *www\.contoso.org*.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägg till lyssnare och omdirigering av regel
> * Skapa en VM-skalningsuppsättning med backend-pool
> * Skapa en CNAME-post i domänen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-Modulversion 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en Azure-resurs med [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Skapa undernätskonfigurationerna för *myBackendSubnet* och *myAGSubnet* med [New AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Skapa virtuellt nätverk med namnet *myVNet* med [New AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) med undernätskonfigurationerna. Slutligen, skapa offentliga IP-adress med namnet *myAGPublicIPAddress* med [New AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). De här resurserna används för att ge nätverksanslutning till programgatewayen och tillhörande resurser.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Skapa IP-konfigurationerna och klientdelsporten

Associera *myAGSubnet* som du skapade tidigare i application gateway med hjälp av [New AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Tilldela *myAGPublicIPAddress* i application gateway med hjälp av [New AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig). Och du kan sedan skapa HTTP-porten med [New AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Skapa serverdelspoolen och tillhörande inställningar

Skapa en serverdelspool med namnet *contosoPool* för application gateway med [New AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Konfigurera inställningar för backend-pool med [New AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$contosoPool = New-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-first-listener-and-rule"></a>Skapa den första lyssnaren och regel

Du behöver en lyssnare så att programgatewayen kan dirigera trafiken till serverdelspoolen på rätt sätt. I den här självstudien skapar du två lyssnare för de två domänerna. I det här exemplet lyssnare har skapats för domänerna med *www\.contoso.com* och *www\.contoso.org*.

Skapa den första lyssnaren med namnet *contosoComListener* med [New AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) med frontend-konfigurationen och frontend-port som du skapade tidigare. Du måste ange en regel för lyssnaren som anger vilken serverdelspool som ska användas för inkommande trafik. Skapa en grundläggande regel med namnet *contosoComRule* med [New AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$contosoComlistener = New-AzApplicationGatewayHttpListener `
  -Name contosoComListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -HostName "www.contoso.com"
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name contosoComRule `
  -RuleType Basic `
  -HttpListener $contosoComListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Skapa programgatewayen

Nu när du har skapat de stödjande resurserna som krävs, ange parametrar för programgatewayen med namnet *myAppGateway* med [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), och sedan skapa den med hjälp av [Nya AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $contosoComListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-the-second-listener"></a>Lägg till andra lyssnare

Lägg till lyssnaren med namnet *contosoOrgListener* som behövs för att omdirigera trafik med hjälp av [Lägg till AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$frontendPort = Get-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -ApplicationGateway $appgw
$ipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name contosoOrgListener `
  -Protocol Http `
  -FrontendIPConfiguration $ipconfig `
  -FrontendPort $frontendPort `
  -HostName "www.contoso.org"
Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Lägga till konfigurationen för omdirigering

Du kan konfigurera omdirigering för lyssnaren med [Lägg till AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration). 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoComlistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoComListener `
  -ApplicationGateway $appgw
$contosoOrglistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoOrgListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectOrgtoCom `
  -RedirectType Found `
  -TargetListener $contosoComListener `
  -IncludePath $true `
  -IncludeQueryString $true
Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-second-routing-rule"></a>Lägg till den andra regeln för Routning

Därefter kan du associera omdirigeringskonfiguration till en ny regel med namnet *contosoOrgRule* med [Lägg till AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoOrglistener = Get-AzApplicationGatewayHttpListener `
  -Name contosoOrgListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name redirectOrgtoCom `
  -ApplicationGateway $appgw   
Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name contosoOrgRule `
  -RuleType Basic `
  -HttpListener $contosoOrgListener `
  -RedirectConfiguration $redirectConfig
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-set"></a>Skapa VM-skalningsuppsättningar

I det här exemplet skapar du en skalningsuppsättning för virtuella datorer som har stöd för serverdelspoolen som du skapade. Skalningsuppsättningen som du skapar heter *myvmss* och innehåller två instanser av virtuella datorer som du installerar IIS. Du tilldelar skalningsuppsättningen till serverdelspoolen när du konfigurerar IP-inställningarna.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Installera IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-cname-record-in-your-domain"></a>Skapa en CNAME-post i domänen

När du har skapat programgatewayen med dess offentliga IP-adress kan du hämta DNS-adressen och använda den till att skapa en CNAME-post i domänen. Du kan använda [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) att hämta DNS-adressen för application gateway. Kopiera värdet *fqdn* för DNSSettings och använd det som värde för CNAME-posten du skapar. Du bör inte använda A-poster eftersom den virtuella IP-adressen kan ändras när programgatewayen startas om.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Ange domännamnet i adressfältet i webbläsaren. Till exempel http://www.contoso.com.

![Testa contoso-webbplatsen i programgatewayen](./media/tutorial-internal-site-redirect-powershell/application-gateway-iistest.png)

Ändra adressen till din domän, till exempel http://www.contoso.org så bör du se att trafiken har omdirigerats till lyssnaren för www\.contoso.com.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägg till lyssnare och omdirigering av regel
> * Skapa en VM-skalningsuppsättning med backend-pooler
> * Skapa en CNAME-post i domänen

> [!div class="nextstepaction"]
> [Läs mer om vad du kan göra med en programgateway](./application-gateway-introduction.md)
