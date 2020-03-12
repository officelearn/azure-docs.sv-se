---
title: Agera värd åt flera webbplatser i Azure Application Gateway
description: Den här artikeln innehåller en översikt över stödet för Azure Application Gateway för flera platser.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: c43ac0923e0d3d76c25657f4870a0a0431bc8b6e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096443"
---
# <a name="application-gateway-multiple-site-hosting"></a>Flera webbplatser i Application Gateway

Med värd tjänster för flera webbplatser kan du konfigurera fler än ett webb program på samma port i en Programgateway. Den här funktionen låter dig konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en programgateway. Varje webbplats kan dirigeras till en egen serverdelspool. I följande exempel fungerar programgatewayen trafik för `contoso.com` och `fabrikam.com` från två backend-lagringspooler som kallas ContosoServerPool och FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Regler bearbetas i den ordning som de visas i portalen för v1 SKU. För v2-SKU: n har exakta matchningar högre prioritet. Vi rekommenderar starkt att konfigurera lyssnare för flera platser första innan du konfigurerar en grundläggande lyssnare.  Detta säkerställer att trafik dirigeras till rätt serverdel. Om en grundläggande lyssnare visas först och matchar en inkommande begäran kommer den att bearbetas av den lyssnaren.

Begäranden om `http://contoso.com` dirigeras till ContosoServerPool och `http://fabrikam.com` dirigeras till FabrikamServerPool.

På samma sätt kan du vara värd för flera under domäner för samma överordnade domän på samma Application Gateway-distribution. Du kan till exempel vara värd för `http://blog.contoso.com` och `http://app.contoso.com` på en enskild Application Gateway-distribution.

## <a name="host-headers-and-server-name-indication-sni"></a>Värdhuvuden och servernamnsindikator (SNI)

Det finns tre vanliga mekanismer för att aktivera flera platser inom samma infrastruktur.

1. Flera webbprogram på varsin unik IP-adress.
2. Använd värdnamn för att ha flera webbprogram på samma IP-adress.
3. Använd olika portar för att har flera webbprogram på samma IP-adress.

För närvarande Application Gateway stöder en enskild offentlig IP-adress där den lyssnar efter trafik. Det finns för närvarande inte stöd för att ha flera program, var och en med sin egen IP-adress. 

Application Gateway stöder flera program som var och en lyssnar på olika portar, men det här scenariot kräver att programmen accepterar trafik på portar som inte är standard. Detta är ofta inte en konfiguration som du vill använda.

Application Gateway förlitar sig på HTTP 1.1 värdhuvuden för att ha mer än en webbplats på samma offentliga IP-adress och port. Webbplatserna i Application Gateway kan också stödja SSL-avlastning med servernamnsindikator (SNI) TLS-tillägget. Det här scenariot innebär att klientens webbläsare och serverdels-webbservergrupp måste ha stöd för HTTP/1.1 och TLS-tillägg som det definieras i RFC 6066.

## <a name="listener-configuration-element"></a>Listener-konfigurationselementet

Befintliga konfigurations element för HTTPListener har förbättrats för att ge stöd för värdnamn och Server namns indikations element. Den används av Application Gateway för att dirigera trafik till lämplig backend-pool. 

Följande kod exempel är kodfragmentet för ett HttpListeners-element från en mallfil:

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

Ingen ändring krävs i regeln för routning. Routingregeln Basic ska fortfarande väljas för att knyta rätt webbplats-lyssnare till motsvarande serverdels-adresspool.

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

