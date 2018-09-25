---
title: Anpassa brandväggsregler för webbaserade program i Azure Application Gateway – PowerShell | Microsoft Docs
description: Den här artikeln innehåller information om hur du anpassar brandväggsregler för webbaserade program i Application Gateway med PowerShell.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: 7dce3657656effd3765f77ae957c1cfc4d3f4316
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964417"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Anpassa brandväggsregler för webbaserade program via PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI](application-gateway-customize-waf-rules-cli.md)

Azure Application Gateway brandväggen för webbaserade program (WAF) ger skydd för webbprogram. Dessa skydd tillhandahålls av OWASP Open Web Application Security Project () Core regeln ange (CRS). Vissa regler kan leda till falska positiva identifieringar och blockera verklig trafik. Därför ger Application Gateway möjlighet att anpassa regelgrupper och -regler. Läs mer på den specifika regelgrupper och regler, [listan över regelgrupper för web application firewall CRS och regler](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Visa regelgrupper och regler

Följande kodexempel visar hur du kan visa regler och regelgrupper som kan konfigureras på en gateway i WAF-aktiverat program.

### <a name="view-rule-groups"></a>Visa regelgrupper

I följande exempel visas hur du visar regelgrupper:

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

Följande utdata är ett trunkerat svar från föregående exempel:

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>Inaktivera regler

I följande exempel inaktiverar regler `910018` och `910017` på application gateway:

```powershell
$disabledrules=New-AzureRmApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-910-IP-REPUTATION -Rules 910018,910017
Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
```

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din inaktiverade regler för kan du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway Diagnostics](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
