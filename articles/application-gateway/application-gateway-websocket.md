---
title: Support för WebSocket i Azure Application Gateway
description: Application Gateway har inbyggt stöd för WebSocket i alla gatewaystorlekar. Det finns inga inställningar som kan konfigureras av användaren.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130327"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Översikt över WebSocket-stöd i Application Gateway

Application Gateway har inbyggt stöd för WebSocket i alla gatewaystorlekar. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. 

WebSocket-protokollet som är standardiserat i [RFC6455](https://tools.ietf.org/html/rfc6455) möjliggör en fullständig dubbelsidig kommunikation mellan en server och en klient via en tidskrävande TCP-anslutning. Den här funktionen möjliggör en mer interaktiv kommunikation mellan webbservern och klienten, vilket kan vara dubbelriktat utan att behöva avsökas enligt kraven i HTTP-baserade implementeringar. WebSocket har låga omkostnader till skillnad från HTTP och kan återanvända samma TCP-anslutning för flera begärande/svar, vilket resulterar i ett effektivare resursutnyttjande. WebSocket-protokoll är utformade för att fungera över traditionella HTTP-portar med 80 och 443.

Du kan fortsätta använda en vanlig HTTP-lyssnare på port 80 eller 443 för att ta emot WebSocket-trafik. WebSocket-trafik dirigeras sedan till WebSocket-aktiverad serverdelsserver med lämplig serverdelspool enligt vad som anges i programgatewayregler. Serverdelsservern måste svara på programgatewayavsökningarna, som beskrivs i [översiktavsnittet för hälsoavsökning.](application-gateway-probe-overview.md) Hälsoavsökningar för programgateway är endast HTTP/HTTPS. Varje serverdelsserver måste svara på HTTP-avsökningar för programgateway för att dirigera WebSocket-trafik till servern.

Den används i appar som drar nytta av snabb kommunikation i realtid, till exempel chatt- och instrumentpanel och spelappar.

## <a name="how-does-websocket-work"></a>Hur fungerar WebSocket

För att upprätta en WebSocket-anslutning utbyts ett specifikt HTTP-baserat handslag mellan klienten och servern. Om det lyckas "uppgraderas" protokollet för programlager från HTTP till WebSockets med den tidigare etablerade TCP-anslutningen. När detta inträffar är HTTP helt ute ur bilden. data kan skickas eller tas emot med WebSocket-protokollet av båda slutpunkterna tills WebSocket-anslutningen stängs. 

![websocket (olika)](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Listener-konfigurationselementet

En befintlig HTTP-lyssnare kan användas för att stödja WebSocket-trafik. Följande är ett utdrag av ett httpListeners-element från en exempelmallfil. Du skulle behöva både HTTP- och HTTPS-lyssnare för att stödja WebSocket och säker WebSocket-trafik. På samma sätt kan du använda portalen eller Azure PowerShell för att skapa en programgateway med lyssnare på port 80/443 för att stödja WebSocket-trafik.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfiguration av backendAddressPool, backendHttpSetting och Routning

En BackendAddressPool används för att definiera en serverdelspool med WebSocket-aktiverade servrar. BackendHttpSetting definieras med en backend-port 80 och 443. Tidsgränsen för begäran i HTTP-inställningar gäller även för WebSocket-sessionen. Det krävs ingen ändring i routningsregeln, som används för att binda lämplig lyssnare till motsvarande backend-adresspool. 

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

## <a name="websocket-enabled-backend"></a>WebSocket aktiverat bakåtsträvt

Serverdelen måste ha en HTTP/HTTPS-webbserver som körs på den konfigurerade porten (vanligtvis 80/443) för att WebSocket ska fungera. Det här kravet beror på att WebSocket-protokollet kräver att det första handskakningen ska vara HTTP med uppgradering till WebSocket-protokoll som huvudfält. Följande är ett exempel på en rubrik:

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

En annan orsak till detta är att programgateway backend hälsa avsökning stöder HTTP och HTTPS-protokoll bara. Om serverdaservern inte svarar på HTTP- eller HTTPS-avsökningar tas den bort från serverdapoolen.

## <a name="next-steps"></a>Nästa steg

När du har lärt dig mer om WebSocket-stöd går du till skapa [en programgateway](quick-create-powershell.md) för att komma igång med ett WebSocket-aktiverat webbprogram.