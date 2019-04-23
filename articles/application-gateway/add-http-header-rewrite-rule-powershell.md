---
title: Skriv om HTTP-huvuden i Azure Application Gateway
description: Den här artikeln innehåller information om hur skriva om HTTP-huvuden i Azure Application Gateway med Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 405bc9aed4605e9728e112595f33c879bf55ec7f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005629"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Skriv om HTTP-begäran och svarshuvuden med Azure Application Gateway – Azure PowerShell

Den här artikeln visar hur du använder Azure PowerShell för att konfigurera en [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) skriva om HTTP-huvuden i begäranden och svar.

> [!IMPORTANT]
> SKU:n för zonredundant programgateway för automatisk skalning är för närvarande tillgänglig som en offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Den här självstudien kräver att du kör Azure PowerShell lokalt. Du måste ha Az Modulversion 1.0.0 eller senare. Kör `Import-Module Az` och sedan`Get-Module Az` att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.
- Du måste ha en Application Gateway-v2 SKU eftersom möjlighet till omskrivning av rubriken inte stöds för v1-SKU. Om du inte har v2-SKU, skapar du en [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) innan du börjar.

## <a name="what-is-required-to-rewrite-a-header"></a>Vad som krävs för att skriva om en rubrik

För att konfigurera HTTP-huvud omskrivning, måste du:

1. Skapa nya objekt som krävs för att skriva om http-huvuden:

   - **RequestHeaderConfiguration**: det här objektet används för att ange fält i begärans-huvud som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till.

   - **ResponseHeaderConfiguration**: det här objektet används för att ange huvudfält för svar som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till.

   - **ActionSet**: det här objektet innehåller konfigurationerna för de begärande- och svarshuvuden som anges ovan.

   - **Villkor**: Det är en valfri konfiguration. Om ett omskrivning villkor läggs utvärderar det innehållet i HTTP (S)-begäranden och svar. Beslutet att köra åtgärden Skriv om som är associerade med villkoret omskrivning baseras om HTTP (S)-begäran eller svar som matchade med Skriv om villkoret. 

     Om flera villkor som är associerade med en åtgärd och sedan åtgärden körs endast när alla villkor är uppfyllda, d.v.s., ett logiskt AND-åtgärd utförs.

   - **RewriteRule**: innehåller flera Skriv om åtgärd - omskrivning villkor kombinationer.

   - **RuleSequence**: Det här är en valfri konfiguration. Det hjälper dig fastställa i vilken ordning som de olika omskrivningsregler utförs. Det här är användbart när det finns flera omskrivningsregler i en omarbetning. Omskrivningsregel med mindre regeln Sekvensvärde hämtar köras först. Om du anger samma regelsekvens till två omskrivningsregler kommer ordningen för körningen att icke-deterministisk.

     Om du inte anger RuleSequence uttryckligen, ställs ett standardvärde på 100.

   - **RewriteRuleSet**: det här objektet innehåller flera omskrivningsregler som ska kopplas till en regel för vidarebefordran av begäran.

2. Du kommer att behöva koppla rewriteRuleSet med en regel för vidarebefordran. Det beror på att Skriv om konfigurationen är kopplad till käll-lyssnaren via en routningsregel för. När du använder en grundläggande routningsregel rubrik Skriv om konfigurationen är associerad med en käll-lyssnare och är en global huvud-omskrivning. När en sökvägsbaserad regel används har rubrik Skriv om konfigurationen definierats för Webbadress för sökvägskarta. Därför gäller detta bara till området angiven sökväg för en plats.

Du kan skapa flera http-huvud omskrivning uppsättningar och varje uppsättning omskrivning kan tillämpas på flera lyssnare. Du kan dock använda endast en omarbetning inställd på en viss lyssnare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Ange http-huvud omarbetning Regelkonfiguration**

I det här exemplet ska vi ändra omdirigerings-URL genom att skriva om location-huvudet i http-svaret när location-huvudet innehåller en referens till ”azurewebsites.net”. Då ska vi lägga till ett villkor att utvärdera om location-huvudet i svaret innehåller azurewebsites.net med hjälp av mönstret `(https?):\/\/.*azurewebsites\.net(.*)$`. Vi använder `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som huvudets värde. Det här ersätter *azurewebsites.net* med *contoso.com* i location-huvudet.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Hämta konfigurationen av din befintliga application-gateway

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Hämta konfigurationen av din befintliga routningsregel för begäran

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Uppdatera application gateway med konfigurationen för att skriva om http-huvuden

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

Läs mer om den konfiguration som krävs för att utföra några vanliga användningsfall i [gemensam rubrik omarbetning scenarier](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).