---
title: Agera värd åt flera webbplatser i Azure Application Gateway
description: Den här artikeln innehåller en översikt över azure application gateway-support för flera webbplatser.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257418"
---
# <a name="application-gateway-multiple-site-hosting"></a>Flera webbplatser i Application Gateway

Med flera webbplatsvärdar kan du konfigurera mer än ett webbprogram på samma port i en programgateway. Den här funktionen låter dig konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en programgateway. Varje webbplats kan dirigeras till en egen serverdelspool. I följande exempel betjänar programgateway trafik för `contoso.com` och `fabrikam.com` från två backend-serverpooler som kallas ContosoServerPool och FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Regler bearbetas i den ordning de visas i portalen för v1 SKU. För v2 SKU har exakta matchningar högre prioritet. Vi rekommenderar starkt att konfigurera lyssnare för flera platser första innan du konfigurerar en grundläggande lyssnare.  Detta säkerställer att trafik dirigeras till rätt serverdel. Om en grundläggande lyssnare visas först och matchar en inkommande begäran kommer den att bearbetas av den lyssnaren.

Begäranden för `http://contoso.com` dirigeras till ContosoServerPool och `http://fabrikam.com` dirigeras till FabrikamServerPool.

På samma sätt kan du vara värd för flera underdomäner för samma överordnade domän på samma programgatewaydistribution. Du kan till `http://blog.contoso.com` exempel `http://app.contoso.com` vara värd för och på en enda programgatewaydistribution.

## <a name="host-headers-and-server-name-indication-sni"></a>Värdhuvuden och servernamnsindikator (SNI)

Det finns tre vanliga mekanismer för att aktivera flera platser inom samma infrastruktur.

1. Flera webbprogram på varsin unik IP-adress.
2. Använd värdnamn för att ha flera webbprogram på samma IP-adress.
3. Använd olika portar för att har flera webbprogram på samma IP-adress.

För närvarande stöder Application Gateway en enda offentlig IP-adress där den lyssnar efter trafik. Så flera program, var och en med sin egen IP-adress stöds för närvarande inte. 

Application Gateway stöder flera program som alla lyssnar på olika portar, men det här scenariot kräver att programmen accepterar trafik på icke-standardportar. Detta är ofta inte en konfiguration som du vill ha.

Application Gateway förlitar sig på HTTP 1.1 värdhuvuden för att ha mer än en webbplats på samma offentliga IP-adress och port. De platser som finns på programgatewayen kan också stödja TLS-avlastning med SNI-tillägget (Server Name Indication). Det här scenariot innebär att klientens webbläsare och serverdels-webbservergrupp måste ha stöd för HTTP/1.1 och TLS-tillägg som det definieras i RFC 6066.

## <a name="listener-configuration-element"></a>Listener-konfigurationselementet

Befintliga HTTPListener-konfigurationselement förbättras för att stödja värdnamn och servernamnindikeringselement. Den används av Application Gateway för att dirigera trafik till lämplig backend-pool. 

Följande kodexempel är kodavsnittet för ett HttpListeners-element från en mallfil:

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Du kan besöka [Resource Manager-mallen för flera webbplatser](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) för en slutpunkt-till-slutpunkts mallbaserad distribution.

## <a name="routing-rule"></a>Routingregeln

Det krävs ingen ändring i routningsregeln. Routingregeln Basic ska fortfarande väljas för att knyta rätt webbplats-lyssnare till motsvarande serverdels-adresspool.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om flera webbplatser, kan du gå till [skapa en programgateway med flera webbplatser](tutorial-multiple-sites-powershell.md) för att skapa en programgateway som stöder flera en ett webbprogram.

