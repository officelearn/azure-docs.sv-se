---
title: WebSocket-stöd i Azure Programgateway | Microsoft Docs
description: Den här sidan innehåller en översikt av programmet Gateway WebSocket-stöd.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836800"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Översikt över WebSocket-stöd i Programgateway

Application Gateway har inbyggt stöd för WebSocket över alla gateway-storlekar. Det finns ingen kan konfigureras inställning du vill aktivera eller inaktivera WebSocket-stöd. 

WebSocket-protokollet är standardiserade i [RFC6455](https://tools.ietf.org/html/rfc6455) aktiverar en fullständig duplex kommunikation mellan en server och en klient över en tidskrävande TCP-anslutning. Den här funktionen tillåter för en mer interaktiva kommunikation mellan webbservern och klienten, som kan vara dubbelriktad utan att behöva för avsökning som krävs i HTTP-baserade implementeringar. WebSocket har lite extra till skillnad från HTTP och kan återanvända samma TCP-anslutning för flera begäranden/svar ledde till ett mer effektivt utnyttjande av resurser. WebSocket-protokoll är utformade att fungera via traditionella HTTP-portarna 80 och 443.

Du kan fortsätta att använda en standard HTTP-lyssnaren på port 80 eller 443 ta emot WebSocket-trafik. WebSocket-trafik omdirigeras sedan till WebSocket aktiverad serverdelen med lämpliga serverdelspoolen som anges i programmet gateway regler. Backend-servern måste svara på gateway-avsökningar programmet som beskrivs i den [hälsa avsökningen översikt](application-gateway-probe-overview.md) avsnitt. Programmet gateway hälsoavsökningar är endast HTTP/HTTPS. Varje backend-servern måste svara på http-avsökningar för Programgateway att dirigera WebSocket-trafik till servern.

## <a name="listener-configuration-element"></a>Listener-konfigurationselementet

En befintlig HTTP-lyssnare kan användas för att stödja WebSocket-trafik. Följande är ett fragment av ett httpListeners element från en exempelfil för mallen. Behöver du både HTTP och HTTPS-lyssnare för att stödja WebSocket och säker WebSocket-trafik. På liknande sätt kan du använda den [portal](application-gateway-create-gateway-portal.md) eller [PowerShell](application-gateway-create-gateway-arm.md) att skapa en Programgateway med lyssnare på port 80/443 för att stödja WebSocket-trafik.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, BackendHttpSetting och routning regelkonfigurationen

En BackendAddressPool används för att definiera en serverdelspool med WebSocket aktiverad servrar. BackendHttpSetting definieras med en backend-port 80 och 443. Egenskaper för cookie-baserad tillhörighet och requestTimeouts är inte relevanta för WebSocket-trafik. Ändring ingen som krävs i regeln för routning, ”Basic” används för att koppla motsvarande serverdelsadresspool lämplig lyssnaren. 

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

## <a name="websocket-enabled-backend"></a>WebSocket aktiverat backend

Din serverdel måste ha en HTTP/HTTPS-webbserver som körs på den konfigurerade port (vanligtvis 80/443) för WebSocket ska fungera. Det här kravet är eftersom WebSocket-protokollet krävs inledande handskakning ska HTTP med uppgraderingen WebSocket-protokollet som en huvudfält. Följande är ett exempel på ett sidhuvud:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

En annan orsak kan vara att programmet gateway backend-hälsoavsökningen stöder endast HTTP och HTTPS-protokoll. Om backend-servern inte svarar på HTTP eller HTTPS avsökningar, tas den utanför serverdelspool.

## <a name="next-steps"></a>Nästa steg

Efter att lära dig mer om WebSocket-support, gå till [skapa en Programgateway](application-gateway-create-gateway.md) komma igång med en WebSocket-aktiverade program.

