---
title: Anpassa web application brandväggsregler i Azure Application Gateway - Azure CLI 2.0 | Microsoft Docs
description: Den här artikeln innehåller information om hur du anpassar web application brandväggsregler i Programgateway med Azure CLI 2.0.
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
ms.openlocfilehash: b0bd79bb7ce584a9abaffbb6c30d6fbfe64f87c2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>Anpassa web application brandväggsregler via Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Den Azure Programgateway brandväggen för webbaserade program (Brandvägg) ger skydd för webbprogram. Dessa skydd tillhandahålls genom att öppna Web Application säkerhet projekt (OWASP) Core regeln ange (CR). Vissa regler kan leda till falska positiva identifieringar och blockera verkliga trafik. Därför ger Programgateway möjlighet att anpassa regelgrupper och regler. Mer information om specifik regelgrupper och regler finns [listan över web brandväggen CR regeln programgrupper och regler](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Visa grupper av regeln och regler

Följande kodexempel visar hur du kan visa regler och regelgrupper som kan konfigureras.

### <a name="view-rule-groups"></a>Visa regelgrupper

I följande exempel visas hur du visar regelgrupper:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Följande utdata är trunkerat svar från föregående exempel:

```
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

### <a name="view-rules-in-a-rule-group"></a>Visa regler i en grupp

I följande exempel visas hur du visar regler i en angiven regelgrupp:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Följande utdata är trunkerat svar från föregående exempel:

```
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

Följande exempel inaktiverar regler `910018` och `910017` på en Programgateway:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din inaktiverade regler du lära dig hur du visar Brandvägg loggarna. Mer information finns i [Programgateway diagnostik](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
