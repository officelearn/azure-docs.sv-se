---
title: "Skapa en Programgateway med hjälp av Routning regler för URL - Azure CLI 2.0 | Microsoft Docs"
description: "Den här sidan innehåller instruktioner om hur du skapar och konfigurerar en Programgateway med hjälp av regler för routning av URL: en."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Skapa en Programgateway med sökväg-baserad routning med Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure-portalen](application-gateway-create-url-route-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Med URL-sökväg-baserade routning, kan du associera rutter baserat på HTTP-begäranden URL-sökvägen. Den kontrollerar om det finns en väg till poolen backend-server som konfigurerats för den URL som visas i programgatewayen och skickar sedan nätverkstrafiken till definierade poolen. Ett vanligt användningsområde för URL-sökväg-baserad routning är att belastningsutjämna förfrågningar för olika typer av innehåll till olika backend-serverpooler.

Azure Application Gateway använder två typer: basic och regler för URL-sökväg-baserade. Grundläggande regeltypen innehåller resursallokering för backend-pooler. Sökväg-baserade regler, förutom resursallokering, kan du också använda sökvägar för den begärda Webbadressen för att välja lämplig backend-pool.

## <a name="scenario"></a>Scenario

I följande exempel visar en Programgateway trafik för contoso.com med två backend-server-adresspooler: en standard-serverpoolen och en bild serverpoolen.

Begäranden för http://contoso.com/image * dirigeras till serverpoolen bild (**imagesBackendPool**). Om sökvägen mönstret inte matchar programgatewayen väljs poolen server standard (**appGatewayBackendPool**).

![URL-väg](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna den **Kommandotolken för Microsoft Azure** och logga in:

```azurecli
az login -u "username"
```

> [!NOTE]
> Du kan också använda `az login` utan att växeln för inloggning på enheten som kräver att ange en kod i aka.ms/devicelogin.

När du anger kommandot ovan, får du en kod. Gå till https://aka.ms/devicelogin i en webbläsare för att fortsätta inloggningen.

![cmd visar enhet inloggning][1]

Ange den kod som du fick i webbläsaren. Detta omdirigerar dig till en inloggningssida.

![webbläsare för att ange koden][2]

Ange kod för inloggning och stäng sedan webbläsaren om du vill fortsätta.

![loggat in][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Lägga till en sökväg-baserade regel till en befintlig Programgateway

Följande steg visar hur du lägger till en sökväg-baserade regel till en befintlig gateway för programmet.
### <a name="create-a-new-back-end-pool"></a>Skapa en ny backend-pool

Konfigurera gateway inställningen **imagesBackendPool** för nätverkstrafik Utjämning av nätverksbelastning i backend-poolen. I det här exemplet kan du konfigurera olika backend-processpool-inställningar för den nya backend-poolen. Varje backend-pool kan ha sina egna inställningar. Sökväg-baserade regler använder backend-HTTP-inställningar för att dirigera trafik till rätt backend-poolmedlemmar. Anger protokoll och port som används när du skickar trafik till backend-poolmedlemmar. Backend-HTTP-inställningar avgör också cookie-baserad sessioner.  Om aktiverad, skickar cookie-baserad session tillhörighet trafik till samma serverdelen som tidigare begäranden för varje paket.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>Skapa en ny frontend-port för en Programgateway

Konfigurationsobjektet frontend-porten används av en lyssnare för att definiera vilken port som programgatewayen lyssnar efter trafik på lyssnaren.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Skapa en ny lyssnare

Det här steget konfigurerar lyssnaren för den offentliga IP-adressen och porten som används för att ta emot inkommande nätverkstrafik. I följande exempel tar tidigare konfigurerade frontend IP-konfigurationen och frontend portkonfiguration ett protokoll (http eller https, vilket är skiftlägeskänsliga) och konfigurerar lyssnaren. I det här exemplet lyssnaren lyssnar efter HTTP-trafik på port 82 på den offentliga IP-adressen skapade tidigare i det här scenariot.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Skapa URL-sökväg karta

Det här steget konfigurerar relativ URL-sökvägen som används av programgatewayen för att definiera mappning mellan sökvägen och backend-poolen som tilldelats för den inkommande trafiken.

> [!IMPORTANT]
> Varje sökväg måste börja med ”/”, och den enda plats en asterisk tillåts i slutet. Giltiga exempel är /xyz, /xyz* eller/xyz / *. Strängen som skickas till sökvägen matcher innehåller inte någon text efter först ””? eller ”#”, och dessa tecken är inte tillåtna. 

I följande exempel skapas en regel för/bilder / * sökväg, routning trafik till backend- **imagesBackendPool**. Den här regeln innebär att trafik för varje uppsättning URL-adresser dirigeras till serverdelen. Till exempel http://adatum.com/images/figure1.jpg går till **imagesBackendPool**. Om sökvägen inte matchar någon av de fördefinierade sökvägsreglerna, konfigurerar regelkonfigurationen sökväg kartan även en standard backend-adresspool. Till exempel http://adatum.com/shoppingcart/test.html går till **pool1** eftersom det har definierats som standard för omatchade trafik.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta om Secure Sockets Layer (SSL)-avlastning, se [konfigurera en Programgateway för SSL-avlastning](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png
