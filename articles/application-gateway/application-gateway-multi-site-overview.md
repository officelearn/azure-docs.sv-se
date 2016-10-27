<properties
   pageTitle="Ha flera webbplatser i Application Gateway | Microsoft Azure"
   description="Den här sidan ger en översikt över Application Gateways stöd för flera webbplatser."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/04/2016"
   ms.author="amsriva"/>


# <a name="application-gateway-multiple-site-hosting"></a>Flera webbplatser i Application Gateway

Om du har flera webbplatser så kan du konfigurera fler än ett webbprogram inom samma programgateway-instans. Den här funktionen låter dig konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 20 webbplatser till en programgateway. Varje webbplats kan dirigeras till en egen serverdelspool. I följande exempel servar Application Gateway trafik åt contoso.com och fabrikam.com från två serverdels-serverpooler som heter ContosoServerPool och FabrikamServerPool.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

Begäranden för http://contoso.com dirigeras till ContosoServerPool och http://fabrikam.com dirigeras till FabrikamServerPool.

På samma sätt kan två underdomäner i samma överordnade domän finnas på samma distribution av en programgateway. Exempel på användning av underdomäner kan vara http://blog.contoso.com och http://app.contoso.com på samma distribution av en programgateway.

## <a name="host-headers-and-server-name-indication-(sni)"></a>Värdhuvuden och servernamnsindikator (SNI)

Det finns tre vanliga mekanismer för att aktivera flera platser inom samma infrastruktur.

1. Flera webbprogram på varsin unik IP-adress.
2. Använd värdnamn för att ha flera webbprogram på samma IP-adress.
3. Använd olika portar för att har flera webbprogram på samma IP-adress.

För tillfället får en Application Gateway en enda IP-adress som den lyssnar på trafik från. Därför stöds inte alternativet där varje program har sin egen IP-adress för tillfället. Application Gateway stöder att ha flera program som alla lyssnar på olika portar, men det här scenariot kräver att programmen accepterar trafik på icke-standardportar och det är ofta en oönskad konfiguration. Application Gateway förlitar sig på HTTP 1.1 värdhuvuden för att ha mer än en webbplats på samma offentliga IP-adress och port. Webbplatserna i Application Gateway kan också stödja SSL-avlastning med servernamnsindikator (SNI) TLS-tillägget. Det här scenariot innebär att klientens webbläsare och serverdels-webbservergrupp måste ha stöd för HTTP/1.1 och TLS-tillägg som det definieras i RFC 6066.

## <a name="listener-configuration-element"></a>Listener-konfigurationselementet

Det befintliga HTTPListener-konfigurationselement utökas till att stödja värdnamn och servernamnsindikator-element, som används av Application Gateway för att dirigera trafik till korrekta serverdels-pooler. Följande kodexempel är ett utdrag från HttpListeners-elementet från mallfilen.

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




Du kan besöka [Resource Manager-mallen för flera webbplatser](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) för en slutpunkt-till-slutpunkts mallbaserad distribution.

## <a name="routing-rule"></a>Routingregeln

Inga ändringar behövs i routingregeln. Routingregeln Basic ska fortfarande väljas för att knyta rätt webbplats-lyssnare till motsvarande serverdels-adresspool.

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

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om flera webbplatser, kan du gå till [skapa en programgateway med flera webbplatser](application-gateway-create-multisite-azureresourcemanager-powershell.md) för att skapa en programgateway som stöder flera en ett webbprogram.



<!--HONumber=Oct16_HO3-->


