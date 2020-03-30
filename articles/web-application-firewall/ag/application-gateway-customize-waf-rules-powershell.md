---
title: Anpassa regler med PowerShell
titleSuffix: Azure Web Application Firewall
description: Den här artikeln innehåller information om hur du anpassar webbprogram brandväggsregler i Application Gateway med PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048509"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Anpassa brandväggsregler för webbprogram med PowerShell

Azure Application Gateway Web Application Firewall (WAF) skyddar webbprogram. Dessa skydd tillhandahålls av OWASP-huvudregeluppsättningen (Open Web Application Security Project) (CRS). Vissa regler kan orsaka falska positiva identifieringar och blockera verklig trafik. Därför tillhandahåller Application Gateway möjligheten att anpassa regelgrupper och regler. Mer information om specifika regelgrupper och regler finns i [Lista över CRS-regelgrupper och regler för webbprogram brandvägg.](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Visa regelgrupper och regler

Följande kodexempel visar hur du visar regler och regelgrupper som kan konfigureras på en WAF-aktiverad programgateway.

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

I följande exempel `911011` inaktiveras regler och `911012` en programgateway:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Tvingande regler

Följande lista innehåller villkor som gör att WAF blockerar begäran i förebyggande läge (i identifieringsläge loggas de som undantag). Dessa kan inte konfigureras eller inaktiveras:

* Om begäran inte tolkas resulterar det i att begäran blockeras, såvida inte kroppsinspektionen är inaktiverad (XML, JSON, formulärdata)
* Datalängden för begäran (utan filer) är större än den konfigurerade gränsen
* Begärantexten (inklusive filer) är större än gränsen
* Ett internt fel inträffade i WAF-motorn

CRS 3.x specifik:

* Inkommande avvikelsepoäng överskred tröskelvärdet

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat dina inaktiverade regler kan du läsa om hur du visar dina WAF-loggar. Mer information finns i [Diagnostik för programgateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
