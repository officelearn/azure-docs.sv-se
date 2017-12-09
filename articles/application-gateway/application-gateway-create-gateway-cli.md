---
title: Skapa en Programgateway - Azure CLI 2.0 | Microsoft Docs
description: "Lär dig hur du skapar en Programgateway med Azure CLI 2.0 i Resource Manager."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: beb2dab177d021fee1dbbe630f8b6854a7d94f68
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Skapa en Programgateway med hjälp av Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure klassiska PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager-mall](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Azure Application Gateway är en dedikerad virtuell installation som ger programmet leverans domänkontrollant (ADC) som en tjänst med olika layer 7 belastningsutjämning funktioner för ditt program.

## <a name="cli-versions"></a>CLI-versioner

Du kan skapa en Programgateway med någon av följande versioner kommandoradsgränssnittet (CLI):

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md): Azure CLI för klassiskt och Azure Resource Manager distributionsmodellerna
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md): nästa generations CLI för Resource Manager-distributionsmodellen

## <a name="prerequisite-install-the-azure-cli-20"></a>Förutsättning: Installera Azure CLI 2.0

Om du vill utföra stegen i den här artikeln, måste du [installerar Azure CLI för macOS, Linux och Windows](https://docs.microsoft.com/cli/azure/install-az-cli2).

> [!NOTE]
> Du behöver ett Azure-konto att skapa en Programgateway. Om du inte har någon registrera dig för en [kostnadsfri utvärderingsversion](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scenario

I det här scenariot att lära dig hur du skapar en Programgateway med hjälp av Azure portal.

Det här scenariot kommer:

* Skapa en medelhög Programgateway med två instanser.
* Skapa ett virtuellt nätverk med namnet AdatumAppGatewayVNET med en 10.0.0.0/16 reserverade CIDR-blocket.
* Skapa undernätet Appgatewaysubnet som använder 10.0.0.0/28 som dess CIDR-block.

> [!NOTE]
> Ytterligare konfiguration av programgatewayen, inklusive anpassade hälsoavsökningar, adresser backend-poolen och ytterligare regler sker när programgatewayen har skapats och inte under inledande distributionen.

## <a name="before-you-begin"></a>Innan du börjar

En Programgateway kräver sin egen undernät. När du skapar ett virtuellt nätverk, se till att du lämnar tillräckligt med adressutrymme för flera undernät. När du har distribuerat en Programgateway till ett undernät kan du lägga till bara ytterligare programgatewayer till det undernätet.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna den **Kommandotolken för Microsoft Azure** och logga in:

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> Du kan också använda `az login` utan att växeln för inloggning på enheten som kräver att ange en kod i aka.ms/devicelogin.

När du har angett kommandot ovan får du en kod. Gå till https://aka.ms/devicelogin i en webbläsare för att fortsätta inloggningen.

![cmd visar enhet inloggning][1]

Ange den kod som du fick i webbläsaren. Detta omdirigerar dig till en inloggningssida.

![webbläsare för att ange koden][2]

Ange kod för inloggning och stäng sedan webbläsaren om du vill fortsätta.

![loggat in][3]

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Innan du skapar programgatewayen måste du skapa en resursgrupp för att hysa den. Ange följande kommando:

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Använd backend-IP-adresser för IP-adresser backend-server. Dessa värden kan vara privata IP-adresser i det virtuella nätverket, offentliga IP-adresser eller fullständigt kvalificerat domännamn för backend-servrar. I följande exempel skapas en Programgateway med ytterligare konfigurationer för HTTP-inställningar, portar och regler:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

I föregående exempel visas flera egenskaper som inte krävs under genereringen av en Programgateway. Följande exempel skapar en Programgateway med informationen som krävs:

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> En lista över parametrar som ska användas vid skapandet, kör du följande kommando: `az network application-gateway create --help`.

Det här exemplet skapar en basic-Programgateway med standardinställningarna för lyssnare, backend-adresspool, backend-HTTP-inställningar och regler. Du kan ändra dessa inställningar efter distributionen när etableringen är klar.

Om webbprogrammet har definierats med backend-pool i föregående steg, börjar belastningsutjämning nu.

## <a name="get-the-application-gateway-dns-name"></a>Hämta DNS-namnet på programmet gateway
När du skapar gatewayen, konfigurerar du sedan klientdelen för kommunikation. När du använder en offentlig IP-adress, kräver en dynamiskt tilldelad DNS-namn som inte är eget programgatewayen. För att säkerställa att användarna kan trycka programgatewayen, använder du en CNAME-post för att peka till offentlig slutpunkt för programgatewayen. Mer information finns i [Använd Azure DNS för att ange inställningar för anpassad domän för en Azure-tjänst](../dns/dns-custom-domain.md).

Hämta information om programgatewayen och dess tillhörande IP DNS-namn med hjälp av PublicIPAddress-element som är kopplade till programgatewayen om du vill konfigurera ett alias. Använda den Programgateway DNS-namn för att skapa en CNAME-post som pekar på två webbprogram till DNS-namnet. Inte omstart Använd A-poster eftersom VIP kan ändras på av programgatewayen.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="delete-all-resources"></a>Ta bort alla resurser

Kör följande kommando för att ta bort alla resurser som har skapats i den här artikeln:

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du skapar anpassade hälsoavsökningar, gå till [skapa en anpassad avsökningsåtgärd för Programgateway med hjälp av portalen](application-gateway-create-probe-portal.md).

Information om hur du konfigurerar SSL-avlastning och ta kostsamma SSL-dekryptering av webbservrar finns [konfigurera en Programgateway för SSL-avlastning med hjälp av Azure Resource Manager](application-gateway-ssl-arm.md).

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
