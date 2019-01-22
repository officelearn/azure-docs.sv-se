---
title: Översikt över dirigering av URL-baserat innehåll med Azure Application Gateway
description: Den här sidan innehåller en översikt över Azure Application Gateway URL-baserad innehållsroutning, UrlPathMap-konfigurationen och PathBasedRouting-regeln.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/8/2019
ms.author: victorh
ms.openlocfilehash: 95d443eeebf2fe5a8fad185210f88576a5f21f6a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435303"
---
# <a name="url-path-based-routing-overview"></a>Översikt över URL-sökvägsbaserad routning

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. 

Ett av scenarierna är att dirigerar begäranden för olika innehållstyper till olika serverdels-serverpooler.

I följande exempel Application Gateway fungerar som värd för trafik åt contoso.com från tre serverdels serverpooler till exempel: VideoServerPool, ImageServerPool och DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Begäranden för http\://contoso.com/video/* dirigeras till VideoServerPool och http\://contoso.com/images/* dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

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

### <a name="pathpattern"></a>PathPattern

PathPattern är en lista över sökvägsmönster att matcha. Vart och ett måste börja med / och ett * får bara förekomma på slutet följt av ett /. Strängen som skickats till sökvägsmatcharen saknar text efter först? eller # och de tecknen tillåts inte här. I annat fall tillåts alla tecken som tillåts i en URL i PathPattern.

Mönster som stöds beror på om du distribuerar Application Gateway v1 eller v2:

#### <a name="v1"></a>v1

Sökväg för regler är skiftlägeskänsliga.

|V1 sökvägsmönster  |Stöds?  |
|---------|---------|
|`/images/*`     |ja|
|`/images*`     |nej|
|`/images/*.jpg`     |nej|
|`/*.jpg`     |nej|
|`/Repos/*/Comments/*`     |nej|
|`/CurrentUser/Comments/*`     |ja|

#### <a name="v2"></a>v2

Sökväg för regler är skiftlägeskänsliga.

|v2 sökvägsmönster  |Stöds?  |
|---------|---------|
|`/images/*`     |ja|
|`/images*`     |ja|
|`/images/*.jpg`     |nej|
|`/*.jpg`     |nej|
|`/Repos/*/Comments/*`     |nej|
|`/CurrentUser/Comments/*`     |ja|

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

När du läst om URL-baserad innehållsroutning, kan du gå till [skapa en Application Gateway med URL-baserad routing](application-gateway-create-url-route-portal.md) för att skapa en Application Gateway med regler för URL-routning.
