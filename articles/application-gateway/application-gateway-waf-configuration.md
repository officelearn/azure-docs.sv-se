---
title: Storleksgränser för Web application firewall begäran och undantagslistor i Azure Application Gateway – Azure-portalen
description: Den här artikeln innehåller information om storleksgränser för web application firewall begäran och undantag visar konfigurationen i Application Gateway med Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 1/29/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a814fc6e9a72ba92d915821bd1e1694366844555
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277432"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Storleksgränser för Web application firewall begäran och undantagslistor

Azure Application Gateway brandväggen för webbaserade program (WAF) ger skydd för webbprogram. Den här artikeln beskriver storleksgränser för WAF-begäran och undantag visar konfigurationen.

## <a name="waf-request-size-limits"></a>Storleksgränser för WAF-begäran

![Storleksgränser för begäran](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Brandvägg för webbaserade program kan du konfigurera storleksbegränsningar för begäran i lägre och övre gränser. Följande två storlek gränser konfigurationer finns:

- Fältet tillåtna brödtext storleken anges i KB-artiklar och kontroller övergripande storleksgränsen för begäran exkludera en fil laddas upp. Det här fältet kan variera mellan 1 KB minst 128 KB maximala värdet. Standardvärdet för textstorleken för begäran är 128 KB.
- Fältet Spara uppladdning gränsen anges i MB och styr den maximalt tillåtna storleken för filöverföring. Det här fältet kan ha ett minsta värde på 1 MB och högst 500 MB för stora SKU-instanser även medel SKU har högst 100 MB. Standardvärdet för överföring filgränsen är 100 MB.

WAF erbjuder även en konfigurerbar ratten om du vill aktivera begäran brödtext inspektion eller inaktivera. Begäran brödtext granskning är aktiverat som standard. Om begäran brödtext-kontroll är inaktiverad, utvärderar WAF inte innehållet i meddelandetexten för HTTP. I sådana fall kan fortsätter WAF WAF-regler på rubriker, cookies och URI: N. Om begäran brödtext-kontroll är inaktiverad fältet för tillåtna brödtext storlek som inte är tillämplig och kan inte anges. Stänga av begäran brödtext inspektion större än 128 KB skickas till WAF gör för meddelanden, men meddelandetexten kontrolleras inte sårbarheter.

## <a name="waf-exclusion-lists"></a>WAF-undantagslistor

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF-undantagslistor kan du utelämna vissa begärandeattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory infogas token som används för autentisering eller lösenorden. Attribut är felbenägna innehåller specialtecken som kan utlösa en falsk positiv identifiering från WAF-regler. När ett attribut har lagts till i undantagslistan för WAF, anses det är inte av någon konfigurerad och aktiv WAF-regel. Undantagslistor är globala omfång.

Följande attribut kan läggas till undantagslistor:

* Begärandehuvuden
* Begäran om Cookies
* Begärandetext

   * Flera delar formulärdata
   * XML
   * JSON

Du kan ange en exakt begärandehuvudet, brödtext, cookie eller fråga strängmatchning för attributet.  Eller alternativt kan du ange delmatchningar. Undantaget är alltid på ett fält med rubriken, aldrig på dess värde. Undantagsregler är globala omfång och gäller för alla sidor och alla regler.

Här följer stöds matchning kriterier operatorer:

- **Är lika med**:  Den här operatorn används efter en exakt matchning. Till exempel för att välja en rubrik med namnet **bearerToken**, använda equals-operatorn med väljaren som **bearerToken**.
- **Börjar med**: Den här operatorn matchar alla fält som börjar med det angivna väljarvärdet.
- **Slutar med**:  Den här operatorn matchar alla fält i begäran som slutar med det angivna väljarvärdet.
- **Innehåller**: Den här operatorn matchar alla fält i begäran som innehåller det angivna väljarvärdet.
- **Lika med valfritt**: Den här operatorn matchar alla fält i begäran. * kommer att väljarvärdet för.

I samtliga fall matchar är skiftlägeskänsligt och reguljära uttryck tillåts inte som väljare.

### <a name="examples"></a>Exempel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande Azure PowerShell-kodavsnitt visar användning av undantag:

```azurepowershell
// exclusion 1: exclude request head start with xyz
// exclusion 2: exclude request args equals a

$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestHeaderNames" -SelectorMatchOperator "StartsWith" -Selector "xyz"

$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestArgNames" -SelectorMatchOperator "Equals" -Selector "a"

// add exclusion lists to the firewall config

$firewallConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention -RuleSetType "OWASP" -RuleSetVersion "2.2.9" -DisabledRuleGroups $disabledRuleGroup1,$disabledRuleGroup2 -RequestBodyCheck $true -MaxRequestBodySizeInKb 80 -FileUploadLimitInMb 70 -Exclusions $exclusion1,$exclusion2
```

Följande json-kodfragmentet visar användning av undantag:

```json
"webApplicationFirewallConfiguration": {
          "enabled": "[parameters('wafEnabled')]",
          "firewallMode": "[parameters('wafMode')]",
          "ruleSetType": "[parameters('wafRuleSetType')]",
          "ruleSetVersion": "[parameters('wafRuleSetVersion')]",
          "disabledRuleGroups": [],
          "exclusions": [
            {
                "matchVariable": "RequestArgNames",
                "selectorMatchOperator": "StartsWith",
                "selector": "a^bc"
            }
```

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat inställningarna WAF du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md#diagnostic-logging).
