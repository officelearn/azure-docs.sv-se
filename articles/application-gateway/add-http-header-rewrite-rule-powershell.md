---
title: Skriv om HTTP-huvuden i en befintlig Azure Application Gateway
description: Den här artikeln innehåller information om hur skriva om HTTP-huvuden i en befintlig Azure Application Gateway med Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: cb3af5dc8368dc7e598bd0b05653b8ae921a5097
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452317"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>Skriv om HTTP-huvuden i en befintlig Programgateway

Du kan använda Azure PowerShell för att konfigurera [regler för att skriva om HTTP-begäran och svarshuvuden](rewrite-http-headers.md) i en befintlig [automatisk skalning och zonredundant application gateway SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> SKU:n för zonredundant programgateway för automatisk skalning är för närvarande tillgänglig som en offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Hämta konfigurationen av en befintlig Programgateway
> * Ange http-huvud omarbetning Regelkonfiguration
> * Uppdatera application gateway med ovanstående konfiguration för att skriva om http-huvuden

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien kräver att du kör Azure PowerShell lokalt. Du måste ha Az Modulversion 1.0.0 eller senare. Kör `Import-Module Az` och sedan`Get-Module Az` att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Ange http-huvud omarbetning Regelkonfiguration**

Konfigurera de nya objekt som krävs för att skriva om http-huvuden:

- **RequestHeaderConfiguration**: det här objektet används för att ange fält i begärans-huvud som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till.
- **ResponseHeaderConfiguration**: det här objektet används för att ange huvudfält för svar som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till.
- **ActionSet**: det här objektet innehåller konfigurationerna för de begärande- och svarshuvuden som anges ovan. 
- **RewriteRule**: det här objektet innehåller alla de *actionSets* angetts ovan. 
- **RewriteRuleSet**– det här objektet innehåller alla de *rewriteRules* och måste vara kopplad till en begäran routningsregel - grundläggande eller -baserad.

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Hämta konfigurationen av din befintliga application-gateway

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Hämta konfigurationen av din befintliga routningsregel för begäran

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Uppdatera application gateway med konfigurationen för att skriva om http-huvuden

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Ta bort en omskrivningsregel

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "rewriteRuleSet1" -ApplicationGateway $appgw 
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw 
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en programgateway med webbadressbaserade routningsregler](./tutorial-url-route-powershell.md)
