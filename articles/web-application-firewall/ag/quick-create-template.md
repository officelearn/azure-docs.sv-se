---
title: 'Snabbstart: Skapa en Azure WAF v2 på Application Gateway - Resource Manager-mall'
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder en Resource Manager-mall för att skapa en brandvägg för webbprogram v2 på Azure Application Gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 6759071e73adfd3af4ac780da6db3a0e6e967ea1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617979"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>Snabbstart: Skapa en Azure WAF v2 på Application Gateway - Resource Manager-mall

I den här snabbstarten använder du en Resource Manager-mall för att skapa en Azure Web Application Firewall v2 på Application Gateway.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-web-application-firewall"></a>Skapa en brandvägg för webbprogram

Den här mallen skapar en enkel brandvägg för webbprogram v2 på Azure Application Gateway. Detta inkluderar en offentlig IP-klientdel IP-adress, HTTP-inställningar, en regel med en grundläggande lyssnare på port 80 och en backend-pool. En WAF-princip med en anpassad regel skapas för att blockera trafik till backend-poolen baserat på en IP-adressmatchningstyp.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAdresser:**](/azure/templates/microsoft.network/publicipaddresses) en för programgatewayen och två för de virtuella datorerna.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : två virtuella datorer
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : två för de virtuella datorerna
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : för att konfigurera IIS och webbsidorna

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera Resource Manager-mall till Azure:

1. Välj **Distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en programgateway, nätverksinfrastrukturen och två virtuella datorer i serverdelspoolen som kör IIS.

   [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Markera eller skapa resursgruppen.
3. Välj **Jag godkänner de villkor som anges ovan** och välj sedan **Köp**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Även om IIS inte krävs för att skapa programgatewayen, installeras den på serverdelsservrarna för att verifiera om Azure har skapat en WAF v2 på programgatewayen. 

Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för programgatewayen på sidan **Översikt.** ![Registrera den offentliga](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) IP-adressen för programgateway Eller så kan du välja **Alla resurser,** ange *myAGPublicIPAddress* i sökrutan och sedan markera den i sökresultaten. Azure visar den offentliga IP-adressen på sidan **Översikt**.
2. Kopiera den offentliga IP-adressen och klistra sedan in den i webbläsarens adressfält för att bläddra i den IP-adressen.
3. Kontrollera svaret. Ett **403 Förbjuden** svar verifierar att WAF har skapats och blockerar anslutningar till backend-poolen.
4. Ändra den anpassade regeln så att **trafik tillåts**.
  Kör följande Azure PowerShell-skript och ersätt ditt resursgruppnamn:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Uppdatera din webbläsare flera gånger och du bör se anslutningar till både myVM1 och myVM2.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen tar du bort resursgruppen. Detta tar bort programgatewayen och alla relaterade resurser.

Om du vill ta `Remove-AzResourceGroup` bort resursgruppen anropar du cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa en programgateway med en brandvägg för webbprogram med Azure-portalen](application-gateway-web-application-firewall-portal.md)