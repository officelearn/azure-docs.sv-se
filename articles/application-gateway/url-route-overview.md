---
title: Översikt över dirigering av URL-baserat innehåll med Azure Application Gateway
description: Den här artikeln ger en översikt över den Application Gateway URL-baserade innehållsroutningen, UrlPathMap-konfigurationen och PathBasedRouting-regeln.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/23/2018
ms.author: victorh
ms.openlocfilehash: ee0267146140d095487b293331a7de493ba151c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895591"
---
# <a name="azure-application-gateway-url-path-based-routing-overview"></a>Översikt över routning av URL-sökvägsbaserat innehåll med Azure Application Gateway

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. 

Ett av scenarierna är att dirigerar begäranden för olika innehållstyper till olika serverdels-serverpooler.

I följande exempel Application Gateway fungerar som värd för trafik åt contoso.com från tre serverdels serverpooler till exempel: VideoServerPool, ImageServerPool och DefaultServerPool.

![imageURLroute](./media/url-route-overview/figure1.png)

Begäranden om <http://contoso.com/video/*> dirigeras till VideoServerPool, och <http://contoso.com/images/*> dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

> [!IMPORTANT]
> Regler bearbetas i den ordning de visas i portalen. Vi rekommenderar starkt att konfigurera lyssnare för flera platser första innan du konfigurerar en grundläggande lyssnare.  Detta säkerställer att trafik dirigeras till rätt serverdel. Om en grundläggande lyssnare visas först och matchar en inkommande begäran kommer den att bearbetas av den lyssnaren.

## <a name="urlpathmap-configuration-element"></a>UrlPathMap-konfigurationselementet

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
> PathPattern: Den här inställningen är en lista över sökvägsmönster att matcha. Vart och ett måste börja med / och ett * får bara förekomma på slutet följt av ett /. Strängen som skickats till sökvägsmatcharen saknar text efter det första? eller # och de tecknen tillåts inte här.

Du kan kolla en [Resource Manager-mall med URL-baserad routning](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) för mer information.

## <a name="pathbasedrouting-rule"></a>PathBasedRouting-regeln

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

## <a name="next-steps"></a>Nästa steg

När du läst om URL-baserad innehållsroutning, kan du gå till [skapa en Application Gateway med URL-baserad routing](tutorial-url-route-powershell.md) för att skapa en Application Gateway med regler för URL-routning.
