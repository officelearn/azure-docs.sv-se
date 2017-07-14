---
title: "Översikt över URL-baserad innehållsroutning | Microsoft Docs"
description: "Den här sidan ger en översikt över den Application Gateway URL-baserade innehållsroutningen, UrlPathMap-konfigurationen och PathBasedRouting-regeln."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: 
ms.assetid: 4409159b-e22d-4c9a-a103-f5d32465d163
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 4b649379ce41a4d6cea93b42fc492fdc0940e689
ms.contentlocale: sv-se
ms.lasthandoff: 06/07/2017


---
# Översikt över URL-sökvägsbaserad routning
<a id="url-path-based-routing-overview" class="xliff"></a>

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. 

Ett av scenarierna är att dirigerar begäranden för olika innehållstyper till olika serverdels-serverpooler.

I följande exempel servar Application Gateway trafik åt contoso.com från tre serverdels-serverpooler, till exempel: VideoServerPool, ImageServerPool och DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Begäranden för http://contoso.com/video* dirigeras till VideoServerPool och http://contoso.com/images* dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.
    
## UrlPathMap-konfigurationselementet
<a id="urlpathmap-configuration-element" class="xliff"></a>

UrlPathMap-elementet används för att ange sökvägsmönster till mappningar för serverdelen och serverpoolen. Följande kodexempel är utdrag av urlPathMap-element från mallfilen.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

> [!NOTE]
> PathPattern: Den här inställningen är en lista över sökvägsmönster att matcha. Varje måste börja med / och ett * får bara förekomma på slutet följt av ett /. Strängen som skickats till sökvägsmatcharen saknar text efter det första? eller # och de tecknen tillåts inte här.

Du kan kolla en [Resource Manager-mall med URL-baserad routning](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) för mer information.

## PathBasedRouting-regeln
<a id="pathbasedrouting-rule" class="xliff"></a>

RequestRoutingRule av typen PathBasedRouting används för att binda en lyssnare till en urlPathMap. Alla begäranden som tas emot för den här lyssnaren dirigeras baserat på principen som anges i urlPathMap.
Utdrag från PathBasedRouting-regeln:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## Nästa steg
<a id="next-steps" class="xliff"></a>

När du läst om URL-baserad innehållsroutning, kan du gå till [skapa en Application Gateway med URL-baserad routing](application-gateway-create-url-route-portal.md) för att skapa en Application Gateway med regler för URL-routning.

