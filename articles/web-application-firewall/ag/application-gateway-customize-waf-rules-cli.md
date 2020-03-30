---
title: Anpassa regler med CLI - Azure Web Application Firewall
description: Den här artikeln innehåller information om hur du anpassar webbprogrambrandväggsregler i Application Gateway med Azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048520"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Anpassa brandväggsregler för webbprogram med hjälp av Azure CLI

Azure Application Gateway Web Application Firewall (WAF) skyddar webbprogram. Dessa skydd tillhandahålls av OWASP-huvudregeluppsättningen (Open Web Application Security Project) (CRS). Vissa regler kan orsaka falska positiva identifieringar och blockera verklig trafik. Därför tillhandahåller Application Gateway möjligheten att anpassa regelgrupper och regler. Mer information om specifika regelgrupper och regler finns i [Lista över CRS-regelgrupper och regler för webbprogram brandvägg.](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Visa regelgrupper och regler

Följande kodexempel visar hur du visar regler och regelgrupper som kan konfigureras.

### <a name="view-rule-groups"></a>Visa regelgrupper

I följande exempel visas hur du visar regelgrupperna:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Följande utdata är ett trunkerat svar från föregående exempel:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Visa regler i en regelgrupp

I följande exempel visas hur du visar regler i en angiven regelgrupp:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Följande utdata är ett trunkerat svar från föregående exempel:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Inaktivera regler

I följande exempel `910018` inaktiveras regler och `910017` en programgateway:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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

När du har konfigurerat dina inaktiverade regler kan du läsa om hur du visar dina WAF-loggar. Mer information finns i [Diagnostik för Programgateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
