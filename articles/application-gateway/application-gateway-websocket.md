---
title: WebSocket-stöd i Azure Application Gateway | Microsoft Docs
description: Den här sidan innehåller en översikt över Application Gateway WebSocket-stöd.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/18/2019
ms.openlocfilehash: 54c34690e678f07d6309a1877b0ca5d0a0b274f5
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606913"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Översikt över WebSocket-stöd i Application Gateway

Application Gateway erbjuder inbyggt stöd för WebSocket över alla gateway-storlekar. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. 

WebSocket-protokoll är standardiserade i [RFC6455](https://tools.ietf.org/html/rfc6455) möjliggör en fullständig duplex kommunikation mellan en server och en klient via en tidskrävande TCP-anslutning. Den här funktionen möjliggör en mer interaktiv kommunikation mellan servern och klienten, vilket kan vara dubbelriktad utan att behöva avsökningen som krävs i HTTP-baserade implementeringar. WebSocket har låg overhead till skillnad från HTTP och kan återanvända samma TCP-anslutningen för flera begäran/svar vilket resulterar i en mer effektiv användning av resurser. WebSocket-protokoll är utformade att fungera över traditionella HTTP-portarna 80 och 443.

Du kan fortsätta med en standard HTTP-lyssnaren på port 80 eller 443 att ta emot WebSocket-trafik. WebSocket-trafik dirigeras sedan till WebSocket-aktiverade backend-servern med korrekta serverdels-pooler som anges i reglerna för application gateway. Backend-servern måste svara på application gateway-avsökningar som beskrivs i den [översikt över hälsotillståndet för avsökning](application-gateway-probe-overview.md) avsnittet. Hälsoavsökningar för Application gateway är endast HTTP/HTTPS. Varje backend-servern måste svara på HTTP-avsökningar för application gateway att dirigera WebSocket-trafik till servern.

Den används i appar som har nytta av snabba, i realtid kommunikation, till exempel chatt, instrumentpanel och spelappar.

## <a name="how-does-websocket-work"></a>Hur fungerar WebSocket

För att upprätta en WebSocket-anslutning, utväxlas en specifik HTTP-baserade handskakning mellan klienten och servern. Om detta lyckas uppgraderas application layer protocol ”” från HTTP till WebSockets, med hjälp av den tidigare etablerade TCP-anslutningen. När detta inträffar är HTTP helt utanför bild. data kan skickas eller tas emot med WebSocket-protokoll av båda slutpunkterna tills WebSocket-anslutningen är stängd. 

![addcert](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Listener-konfigurationselementet

En HTTP-lyssnare kan användas för att stödja WebSocket-trafik. Följande är ett utdrag från httpListeners-elementet från en exempelfil i mallen. Du behöver både HTTP och HTTPS-lyssnare för att stödja WebSocket och säker WebSocket-trafik. På samma sätt du kan använda portalen eller Azure PowerShell för att skapa en Programgateway med lyssnare på port 80/443 för att stödja WebSocket-trafik.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfiguration av BackendAddressPool BackendHttpSetting och Routning

En BackendAddressPool används för att definiera en serverdelspool med aktiverad WebSocket-servrar. BackendHttpSetting definieras med en backend-port 80 och 443. Egenskaper för cookie-baserad tillhörighet och requestTimeouts är inte relevanta för WebSocket-trafik. Det finns inga ändringar behövs i routingregeln. ”grundläggande” används för att knyta rätt lyssnare till motsvarande serverdels-adresspool. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>WebSocket aktiverat serverdel

Serverdelen måste ha en HTTP/HTTPS-webbserver som körs på den konfigurerade port (vanligtvis 80/443) för WebSocket ska fungera. Det här kravet är eftersom WebSocket-protokoll kräver inledande handskakningen vara HTTP vid uppgraderingar WebSocket-protokoll som ett huvud-fält. Följande är ett exempel på en rubrik:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

En annan orsak kan vara hälsoavsökning för serverdelen som application gateway stöder endast HTTP och HTTPS-protokoll. Om backend-servern inte svarar på HTTP eller HTTPS avsökningar, tas den utanför serverdelspool.

## <a name="next-steps"></a>Nästa steg

När du läst om WebSocket-stöd, gå till [skapa en Programgateway](quick-create-powershell.md) att komma igång med en WebSocket aktiverat webbprogram.