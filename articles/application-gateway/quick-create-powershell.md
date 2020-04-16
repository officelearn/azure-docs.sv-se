---
title: 'Snabbstart: Direkt webbtrafik med PowerShell'
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder Azure PowerShell för att skapa en Azure Application Gateway som dirigerar webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 04/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 3e1ca14d967b0e88ea7eb559fd9962a3824ff9b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406217"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Snabbstart: Direkt webbtrafik med Azure Application Gateway med Azure PowerShell

I den här snabbstarten använder du Azure PowerShell för att skapa en programgateway. Då du testa den för att se till att det fungerar korrekt. 

Programgatewayen dirigerar programwebbtrafik till specifika resurser i en backend-pool. Du kan tilldela lyssnare till portar, skapa regler och lägga till resurser i en serverdelspool. För enkelhetens skull använder den här artikeln en enkel installation med en offentlig klientdator, en grundläggande lyssnare som är värd för en enda plats i programgatewayen, en grundläggande routningsregel för begäran och två virtuella datorer i serverdelspoolen.

Du kan också slutföra den här snabbstarten med [Azure CLI](quick-create-cli.md) eller [Azure-portalen](quick-create-portal.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell version 1.0.0 eller senare](/powershell/azure/install-az-ps) (om du kör Azure PowerShell lokalt).

## <a name="connect-to-azure"></a>Anslut till Azure

Om du vill `Connect-AzAccount`ansluta till Azure kör du .

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resursgrupp eller skapa en ny.

Om du vill skapa en `New-AzResourceGroup` ny resursgrupp använder du cmdlet: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Skapa nätverksresurser

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk.  Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.  Du kan antingen skapa ett nytt undernät för Application Gateway eller använda ett befintligt. I det här exemplet skapar du två undernät i det här exemplet: ett för programgatewayen och ett annat för serverdelsservrarna. Du kan konfigurera frontend-IP för programgatewayen så att den är offentlig eller privat enligt ditt användningsfall. I det här exemplet väljer du en IP-adress med offentliga frontend.

1. Skapa undernätskonfigurationerna `New-AzVirtualNetworkSubnetConfig`med .
2. Skapa det virtuella nätverket med undernätskonfigurationerna med `New-AzVirtualNetwork`. 
3. Skapa den offentliga `New-AzPublicIpAddress`IP-adressen med . 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Skapa en programgateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Skapa IP-konfigurationerna och klientdelsporten

1. Används `New-AzApplicationGatewayIPConfiguration` för att skapa konfigurationen som associerar undernätet som du skapade med programgatewayen. 
2. Används `New-AzApplicationGatewayFrontendIPConfig` för att skapa konfigurationen som tilldelar den offentliga IP-adressen som du tidigare skapade till programgatewayen. 
3. Används `New-AzApplicationGatewayFrontendPort` för att tilldela port 80 för att komma åt programgatewayen.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Skapa serverdelspoolen

1. Används `New-AzApplicationGatewayBackendAddressPool` för att skapa backend-poolen för programgatewayen. Backend poolen kommer att vara tom för nu. När du skapar nätverkskorten för serverdelskort i nästa avsnitt lägger du till dem i serverdelspoolen.
2. Konfigurera inställningarna för serverdapoolen med `New-AzApplicationGatewayBackendHttpSetting`.

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Skapa lyssnaren och lägga till en regel

Azure kräver att en lyssnare aktiverar programgatewayen för korrekt dirigering av trafiken till serverdelspoolen. Azure kräver även en regel för att lyssnaren ska veta vilken serverdelspool som ska användas för inkommande trafik. 

1. Skapa en lyssnare med `New-AzApplicationGatewayHttpListener` frontend-konfigurationen och klientdelsporten som du tidigare skapade. 
2. Används `New-AzApplicationGatewayRequestRoutingRule` för att skapa en regel med namnet *regel1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Skapa programgatewayen

Nu när du har skapat de nödvändiga stödresurserna skapar du programgatewayen:

1. Används `New-AzApplicationGatewaySku` för att ange parametrar för programgatewayen.
2. Används `New-AzApplicationGateway` för att skapa programgatewayen.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Serverdelsservrar

Nu när du har skapat Application Gateway, skapa servergrupp virtuella datorer som kommer att vara värd för webbplatser. Serverdel kan bestå av nätverkskort, skalningsuppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och backend-ändar med flera innehavare som Azure App Service. I det här exemplet skapar du två virtuella datorer för Azure som ska användas som serverdelsservrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att verifiera att Azure har skapat programgatewayen.

#### <a name="create-two-virtual-machines"></a>Skapa två virtuella datorer

1. Hämta den nyligen skapade backend-poolen för Programgateway med `Get-AzApplicationGatewayBackendAddressPool`.
2. Skapa ett nätverksgränssnitt med `New-AzNetworkInterface`.
3. Skapa en konfiguration `New-AzVMConfig`för virtuella datorer med .
4. Skapa den virtuella `New-AzVM`datorn med .

När du kör följande kodexempel för att skapa virtuella datorer uppmanas du av Azure att ange autentiseringsuppgifter. Ange *azureuser* för användarnamnet och ett lösenord:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

IIS krävs inte för skapande av programgatewayen, men du installerade det i den här snabbstarten för att kontrollera om Azure lyckades skapa programgatewayen. Använd IIS för att testa programgatewayen:

1. Kör `Get-AzPublicIPAddress` för att hämta den offentliga IP-adressen för programgatewayen. 
2. Kopiera och klistra in den offentliga IP-adressen i webbläsarens adressfält. När du uppdaterar webbläsaren bör du se namnet på den virtuella datorn. Ett giltigt svar verifierar att programgatewayen har skapats och att den kan ansluta till backend.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testa programgatewayen](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen tar du bort resursgruppen. När du tar bort resursgruppen tar du också bort programgatewayen och alla dess relaterade resurser. 

Om du vill ta `Remove-AzResourceGroup` bort resursgruppen anropar du cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway med Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

