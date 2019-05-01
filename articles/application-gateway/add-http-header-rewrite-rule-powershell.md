---
title: Skriv om HTTP-huvuden i Azure Application Gateway
description: Den här artikeln innehåller information om hur du skriva om HTTP-huvuden i Azure Application Gateway med hjälp av Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947195"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Skriv om HTTP-begäran och svarshuvuden med Azure Application Gateway – Azure PowerShell

Den här artikeln beskriver hur du använder Azure PowerShell för att konfigurera en [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instans att skriva om HTTP-huvuden i begäranden och svar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

- Du behöver köra Azure PowerShell lokalt för att slutföra stegen i den här artikeln. Du måste också ha Az Modulversion 1.0.0 eller senare. Kör `Import-Module Az` och sedan `Get-Module Az` att kontrollera vilken version som du har installerat. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.
- Du måste ha en Application Gateway v2 SKU-instans. Skriva om rubriker stöds inte i v1-SKU. Om du inte har v2-SKU, skapar du en [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instans innan du börjar.

## <a name="create-required-objects"></a>Skapa nödvändiga objekt

För att konfigurera HTTP-huvud omskrivning, måste du slutföra de här stegen.

1. Skapa de objekt som krävs för Skriv om HTTP-huvud:

   - **RequestHeaderConfiguration**: Används för att ange fält i begärans-huvud som du avser att skriva om och det nya värdet för sidhuvudena.

   - **ResponseHeaderConfiguration**: Används för att ange huvudfält för svar som du avser att skriva om och det nya värdet för sidhuvudena.

   - **ActionSet**: Innehåller konfigurationer av begäranden och svar-huvuden som angavs tidigare.

   - **Villkor**: En valfri konfiguration. Skriv om villkor utvärdera innehållet i HTTP (S)-begäranden och svar. Skriv om-åtgärd som ska utföras om den HTTP (S)-begäran eller ett svar matchar villkoret omarbetning.

     Om du associerar mer än ett villkor med åtgärden utförs åtgärden endast när alla villkor är uppfyllda. Åtgärden är med andra ord en logiskt AND-åtgärd.

   - **RewriteRule**: Innehåller flera omskrivning åtgärd / omarbetning kombinationer av villkor.

   - **RuleSequence**: Körning av en valfri konfiguration som hjälper till att avgöra i vilken ordning omarbetning regler. Den här konfigurationen är användbar när du har flera omskrivningsregler i en omarbetning. En omskrivningsregel som har ett lägre värde för regeln sekvens körs första. Om du tilldelar två omskrivningsregler samma regel sekvens värde är ordningen för körningen icke-deterministisk.

     Om du inte uttryckligen anger RuleSequence, ange ett standardvärde på 100.

   - **RewriteRuleSet**: Innehåller flera omskrivningsregler som ska kopplas till en regel för vidarebefordran av begäran.

2. Koppla RewriteRuleSet till en regel för vidarebefordran. Skriv om konfigurationen är kopplat till käll-lyssnaren via en routningsregel för. När du använder en grundläggande routningsregel rubrik Skriv om konfigurationen är associerad med en käll-lyssnare och är en global huvud-omskrivning. När du använder en sökvägsbaserad regel har rubrik Skriv om konfigurationen definierats för Webbadress för sökvägskarta. I så fall gäller endast för området angiven sökväg för en plats.

Du kan skapa flera HTTP-huvud omskrivning uppsättningar och tillämpa varje omarbetning som angetts till flera lyssnare. Men du kan använda endast en omarbetning inställd på en viss lyssnare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Ange HTTP-huvudet omarbetning Regelkonfiguration

I det här exemplet ändrar vi en omdirigerings-URL genom att skriva om location-huvudet i HTTP-svaret när location-huvudet innehåller en referens till azurewebsites.net. Detta gör vi lägger till ett villkor att utvärdera om location-huvudet i svaret innehåller azurewebsites.net. Vi använder mönstret `(https?):\/\/.*azurewebsites\.net(.*)$`. Och vi använder `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som huvudets värde. Det här värdet kommer att ersätta *azurewebsites.net* med *contoso.com* i location-huvudet.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Hämta konfiguration för programgatewayen

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Hämta konfigurationen för din regel för vidarebefordran av begäran

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Uppdatera application gateway med konfigurationen för att skriva om HTTP-huvuden

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Ta bort en omskrivningsregel

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du ställer in några vanliga användningsområden i [gemensam rubrik omarbetning scenarier](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).