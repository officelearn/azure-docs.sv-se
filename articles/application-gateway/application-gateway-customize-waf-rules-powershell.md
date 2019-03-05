---
title: Anpassa brandväggsregler för webbaserade program i Azure Application Gateway – PowerShell
description: Den här artikeln innehåller information om hur du anpassar brandväggsregler för webbaserade program i Application Gateway med PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f96395a54f66b787878faeee057f02818f956ade
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317007"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Anpassa brandväggsregler för webbaserade program via PowerShell

Azure Application Gateway brandväggen för webbaserade program (WAF) ger skydd för webbprogram. Dessa skydd tillhandahålls av OWASP Open Web Application Security Project () Core regeln ange (CRS). Vissa regler kan leda till falska positiva identifieringar och blockera verklig trafik. Därför ger Application Gateway möjlighet att anpassa regelgrupper och -regler. Läs mer på den specifika regelgrupper och regler, [listan över regelgrupper för web application firewall CRS och regler](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Visa regelgrupper och regler

Följande kodexempel visar hur du kan visa regler och regelgrupper som kan konfigureras på en gateway i WAF-aktiverat program.

### <a name="view-rule-groups"></a>Visa regelgrupper

I följande exempel visas hur du visar regelgrupper:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Följande utdata är ett trunkerat svar från föregående exempel:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Inaktivera regler

I följande exempel inaktiverar regler `911011` och `911012` på application gateway:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Obligatoriska regler

I följande lista innehåller villkor som gör att WAF för att blockera begäran i Förhindringsläge (i identifieringsläge loggas som undantag). Dessa kan inte vara konfigurerad eller inaktiverad:

* Det gick inte att parsa begärandetexten resulterar i begäran blockeras, såvida inte brödtext inspektion stängs av (XML, JSON, formulärdata)
* Datalängden för begäran brödtext (utan att några filer) är större än den konfigurerade gränsen
* Brödtext (inklusive filer) är större än gränsen
* Ett internt fel inträffade i WAF-motorn

CRS 3.x specifika:

* Inkommande avvikelseidentifiering poäng överskred tröskeln

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din inaktiverade regler för kan du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway Diagnostics](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
