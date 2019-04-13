---
title: Snabbstart – Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell till att skapa en programgateway med Azure Application Gateway som dirigerar webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/11/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d41480def95137e1dc938c845f8cec1d59e26036
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521792"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure PowerShell

Den här snabbstarten visar hur du använder Azure PowerShell för att snabbt skapa en Programgateway.  När du har skapat application gateway testa du sedan den och kontrollera att de fungerar korrekt. Med Azure Application Gateway dirigera application Internet-trafik till specifika resurser genom att tilldela lyssnare till portar, skapa regler och lägga till resurser till en serverdelspool. För enkelhetens skull använder den här artikeln en enkel installation med en offentlig frontend IP-adress, en grundläggande lyssnare till en plats på den här application gateway-värd, två virtuella datorer som används för serverdelspoolen och en regel för vidarebefordran av grundläggande begäran.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Om du väljer att installera och använda Azure PowerShell lokalt kräver den här självstudien Azure PowerShell-Modulversion 1.0.0 eller senare.

1. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). 
2. Skapa en anslutning med Azure genom att köra `Login-AzAccount`.

### <a name="resource-group"></a>Resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Du kan antingen använda en befintlig resursgrupp eller skapa en ny. I det här exemplet ska vi skapa en ny resursgrupp med hjälp av den [New AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) cmdlet enligt följande: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Resurser som krävs

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk.  Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.  Du kan skapa ett nytt undernät för Application Gateway, eller så kan du använda en befintlig. I det här exemplet skapar du två undernät i det här exemplet: en för application gateway och en för backend-servrarna. Du kan konfigurera Frontend IP för Programgatewayen ska vara offentlig eller privat enligt ditt användningsområde. I det här exemplet väljer vi en offentlig Klientdels-IP.

1. Skapa undernätskonfigurationerna genom att anropa [New AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Skapa virtuellt nätverk med undernätskonfigurationerna genom att anropa [New AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Skapa offentlig IP-adress genom att anropa [New AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

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
  -AllocationMethod Dynamic
```
### <a name="backend-servers"></a>Backend-servrar

Serverdel kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade namn (FQDN) och flera innehavare serverprogram som Azure App Service. I det här exemplet skapar du två virtuella datorer för Azure som ska användas som serverdelsservrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att verifiera att Azure har skapat programgatewayen.

#### <a name="create-two-virtual-machines"></a>Skapa två virtuella datorer

1. Skapa ett nätverksgränssnitt med [New AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
2. Skapa en virtuell Datorkonfiguration med [New AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
3. Skapa den virtuella datorn med [New-AzVM](/powershell/module/Az.compute/new-Azvm).

När du kör följande kodexempel för att skapa virtuella datorer uppmanas du av Azure att ange autentiseringsuppgifter. Ange *azureuser* som användarnamn och *Azure123456!* som lösenord:
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
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
  Set-AzVMBootDiagnostics `
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

## <a name="create-an-application-gateway"></a>Skapa en programgateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Skapa IP-konfigurationerna och klientdelsporten

1. Använd [New AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) för att skapa den konfiguration som associerar undernätet som du skapade med application gateway. 
2. Använd [New AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) att skapa den konfiguration som tilldelar den offentliga IP-adressen som du skapade tidigare till application gateway. 
3. Använd [New AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) tilldela port 80 för att få åtkomst till application gateway.

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

1. Använd [New AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) skapa backend-poolen för application gateway. 
2. Konfigurera inställningarna för serverdelspoolen med [New AzApplicationGatewayBackendHttpSettings](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Skapa lyssnaren och lägga till en regel

Azure kräver att en lyssnare aktiverar programgatewayen för korrekt dirigering av trafiken till serverdelspoolen. Azure kräver även en regel för att lyssnaren ska veta vilken serverdelspool som ska användas för inkommande trafik. 

1. Skapa en lyssnare med hjälp av [New AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) med frontend-konfigurationen och frontend-port som du skapade tidigare. 
2. Använd [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) att skapa en regel med namnet *1*. 

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

1. Använd [New AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) att ange parametrar för application gateway.
2. Använd [New AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) att skapa programgatewayen.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
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

## <a name="test-the-application-gateway"></a>Testa programgatewayen

IIS krävs inte för skapande av programgatewayen, men du installerade det i den här snabbstarten för att kontrollera om Azure lyckades skapa programgatewayen. Använd IIS för att testa programgatewayen:

1. Kör [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) att hämta den offentliga IP-adressen för application gateway. 
2. Kopiera och klistra in den offentliga IP-adressen i webbläsarens adressfält. När du uppdaterar webbläsaren bör du se namnet på den virtuella datorn. Ett giltigt svar verifierar att application gateway har skapats och är det kunna ansluta med serverdelen.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testa programgatewayen](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du ta bort resursgruppen. När du tar bort resursgruppen tas även programgatewayen och alla dess relaterade resurser bort. 

Ta bort resursgruppen genom att anropa den [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) cmdlet enligt följande:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway med Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

