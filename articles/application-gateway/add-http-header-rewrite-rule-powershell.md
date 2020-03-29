---
title: Skriva om HTTP-huvuden i Azure Application Gateway
description: Den här artikeln innehåller information om hur du skriver om HTTP-huvuden i Azure Application Gateway med hjälp av Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947195"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Skriva om HTTP-begäranden och svarshuvuden med Azure Application Gateway - Azure PowerShell

I den hÃ¤r artikeln beskrivs hur du anvÃ¤nder Azure PowerShell fÃ¤r konfigurera en [Application Gateway v2 SKU-förekomst](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) fÃ¤r du skriver om HTTP-huvudena i begäranden och svar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

- Du måste köra Azure PowerShell lokalt för att slutföra stegen i den här artikeln. Du måste också ha Az modul version 1.0.0 eller senare installerat. Kör `Import-Module Az` och `Get-Module Az` sedan för att avgöra vilken version du har installerat. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.
- Du måste ha en Application Gateway v2 SKU-instans. Att skriva om rubriker stöds inte i v1 SKU. Om du inte har v2 SKU skapar du en [Application Gateway v2 SKU-instans](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) innan du börjar.

## <a name="create-required-objects"></a>Skapa obligatoriska objekt

Om du vill konfigurera OMskrivning av HTTP-huvuden måste du utföra de här stegen.

1. Skapa de objekt som krävs för HTTP-huvud skriva om:

   - **RequestHeaderConfiguration**: Används för att ange de fält för begäranhuvud som du tänker skriva om och det nya värdet för rubrikerna.

   - **ResponseHeaderConfiguration**: Används för att ange de svarshuvudfält som du tänker skriva om och det nya värdet för rubrikerna.

   - **ActionSet**: Innehåller konfigurationerna för de begärande- och svarshuvuden som angetts tidigare.

   - **Villkor**: En valfri konfiguration. Omskrivningsvillkor utvärderar innehållet i HTTP(S) begäranden och svar. Omskrivningsåtgärden inträffar om HTTP(S)-begäran eller -svaret matchar omskrivningsvillkoret.

     Om du associerar mer än ett villkor med en åtgärd inträffar åtgärden endast när alla villkor är uppfyllda. Med andra ord är åtgärden en logisk OCH-åtgärd.

   - **Skriv omRule**: Innehåller flera omskrivningsåtgärder/skriva om villkorskombinationer.

   - **Regelsekvens**: En valfri konfiguration som hjälper till att avgöra i vilken ordning omskrivningsregler körs. Den här konfigurationen är användbar när du har flera omskrivningsregler i en omskrivningsuppsättning. En omskrivningsregel som har ett lägre regelsekvensvärde körs först. Om du tilldelar samma regelsekvensvärde till två omskrivningsregler är körningsordningen icke-deterministisk.

     Om du inte uttryckligen anger regelsekvensen anges ett standardvärde på 100.

   - **Skriv omRuleSet**: Innehåller flera omskrivningsregler som ska associeras till en routningsregel för begäran.

2. Koppla om skriv omRuleSet till en routningsregel. Omskrivningskonfigurationen är kopplad till källavlyssningsaren via routningsregeln. När du använder en grundläggande routningsregel associeras konfigurationen för huvudreformat med en källlyssnare och en global rubrikskrivning. När du använder en sökvägsbaserad routningsregel definieras konfigurationen för rubrikreda på URL-sökvägskartan. I så fall gäller det endast för området för en platss specifika sökväg.

Du kan skapa flera HTTP-rubrikstrevuppsättningar och använda varje omskrivningsuppsättning på flera lyssnare. Men du kan bara använda en omskrivningsuppsättning på en viss lyssnare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Ange konfigurationen av REGELN FÖR HTTP-huvud skriva om

I det här exemplet ändrar vi en url för omdirigering genom att skriva om platshuvudet i HTTP-svaret när platshuvudet innehåller en referens till azurewebsites.net. För att göra detta lägger vi till ett villkor för att utvärdera om platshuvudet i svaret innehåller azurewebsites.net. Vi använder mönstret. `(https?):\/\/.*azurewebsites\.net(.*)$` Och vi kommer `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` att använda som rubrik värde. Det här värdet ersätter *azurewebsites.net* med *contoso.com* i platshuvudet.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Hämta konfigurationen av programgatewayen

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Hämta konfigurationen av routningsregeln för begäran

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Uppdatera programgatewayen med konfigurationen för att skriva om HTTP-huvuden

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Ta bort en skrivregel

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar några vanliga användningsfall finns i [vanliga scenarier för omskrivning av huvudhuvuden](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).