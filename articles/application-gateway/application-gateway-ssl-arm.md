---
title: Skapa en Programgateway med SSL-avslutning – Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar en programgateway och lägger till ett certifikat för SSL-avslutning med Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 1d832e999e429dbdd3a0fc730a7ec2227a1dee7e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311346"
---
# <a name="create-an-application-gateway-with-ssl-termination-using-azure-powershell"></a>Skapa en programgateway med SSL-avslutning med hjälp av Azure PowerShell

Du kan använda Azure PowerShell till att skapa en [programgateway](application-gateway-introduction.md) med ett certifikat för [SSL-avslutning](application-gateway-backend-ssl.md) som använder en [VM-skalningsuppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för hantering av webbtrafik på servrarna i serverdelen. I det här exemplet innehåller skalningsuppsättningen två virtuella datorinstanser i serverdelens standardpool i programgatewayen. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Konfigurera ett nätverk
> * Skapa en programgateway med certifikatet
> * Skapa en VM-skalningsuppsättning med serverdelens standardpool

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här självstudien kräver Azure PowerShell-Modulversion 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

I produktion bör du importera ett giltigt certifikat som är signerat av en betrodd provider. I den här självstudiekursen skapar du ett självsignerat certifikat med [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Du kan använda [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) med det tumavtryck som returnerades och exportera en pfx-fil från certifikatet.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Du bör se något som liknar det här resultatet:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Använd tumavtrycket till att skapa pfx-filen:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en Azure-resursgrupp med namnet *myResourceGroupAG* med [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Konfigurera undernät med namnet *myBackendSubnet* och *myAGSubnet* med [New AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Skapa virtuellt nätverk med namnet *myVNet* med [New AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) med undernätskonfigurationerna. Slutligen, skapa offentliga IP-adress med namnet *myAGPublicIPAddress* med [New AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). De här resurserna används för att ge nätverksanslutning till programgatewayen och tillhörande resurser.

```powershell
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

Associera *myAGSubnet* som du skapade tidigare i application gateway med hjälp av [New AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Tilldela *myAGPublicIPAddress* i application gateway med hjälp av [New AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Skapa serverdelspoolen och tillhörande inställningar

Skapa backend-adresspoolen med namnet *appGatewayBackendPool* för application gateway med [New AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Konfigurera inställningar för backend-pool med [New AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings).

```powershell
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Skapa standardlyssnare och -regel

Du behöver en lyssnare så att programgatewayen kan dirigera trafiken till serverdelspoolen på rätt sätt. I det här exemplet skapar du en grundläggande lyssnare som lyssnar efter HTTPS-trafik vid rotadressen. 

Skapa en certifikat-objekt med [New-AzApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) och sedan skapa en lyssnare med namnet *mydefaultListener* med [New-AzApplicationGatewayHttpListener ](/powershell/module/az.network/new-azapplicationgatewayhttplistener) med konfiguration för klientdel, frontend-port och certifikat som du skapade tidigare. Du måste ange en regel för lyssnaren som anger vilken serverdelspool som ska användas för inkommande trafik. Skapa en grundläggande regel med namnet *1* med [New AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-certificate"></a>Skapa programgatewayen med certifikatet

Nu när du har skapat de stödjande resurserna som krävs, ange parametrar för programgatewayen med namnet *myAppGateway* med [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), och sedan skapa den med hjälp av [New AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) med certifikatet.

### <a name="create-the-application-gateway"></a>Skapa programgatewayen

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en VM-skalningsuppsättning som ska tillhandahålla servrar till serverdelspoolen i programgatewayen. Du tilldelar skalningsuppsättningen till serverdelspoolen när du konfigurerar IP-inställningarna.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
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

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan använda [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) att hämta den offentliga IP-adressen för application gateway. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Säkerhetsvarning](./media/application-gateway-ssl-arm/application-gateway-secure.png)

Om du använde ett självsignerat certifikat och vill acceptera säkerhetsvarningen väljer du **Information** och sedan **Fortsätt till webbsidan**. Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

![Testa basadressen i programgatewayen](./media/application-gateway-ssl-arm/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Konfigurera ett nätverk
> * Skapa en programgateway med certifikatet
> * Skapa en VM-skalningsuppsättning med serverdelens standardpool

Om du vill veta mer om application gateway och deras associerade resurser kan du fortsätta i instruktionsartiklarna.
