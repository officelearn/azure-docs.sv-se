---
title: Inkommande/utgående IP-adresser
description: Lär dig hur inkommande och utgående IP-adresser används i Azure App Service, när de ändras och hur du hittar adresserna för din app.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279213"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Inkommande och utgående IP-adresser i Azure App Service

[Azure App Service](overview.md) är en tjänst med flera innehavare, med undantag för [App Service-miljöer](environment/intro.md). Appar som inte finns i en App Service-miljö (inte på den [isolerade nivån)](https://azure.microsoft.com/pricing/details/app-service/)delar nätverksinfrastruktur med andra appar. Därför kan inkommande och utgående IP-adresser för en app vara olika och kan till och med ändras i vissa situationer. 

[App Service-miljöer](environment/intro.md) använder dedikerade nätverksinfrastrukturer, så att appar som körs i en App Service-miljö får statiska, dedikerade IP-adresser både för inkommande och utgående anslutningar.

## <a name="when-inbound-ip-changes"></a>När inkommande IP-ändringar

Oavsett antalet utskalade instanser har varje app en enda inkommande IP-adress. Den inkommande IP-adressen kan ändras när du utför någon av följande åtgärder:

- Ta bort en app och återskapa den i en annan resursgrupp.
- Ta bort den sista appen i en resursgrupp _och_ regionkombination och återskapa den.
- Ta bort en befintlig SSL-bindning, till exempel under certifikatförnyelse (se [Förnya certifikat](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Hitta den inkommande IP-adressen

Kör bara följande kommando i en lokal terminal:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Hämta en statisk inkommande IP

Ibland kanske du vill ha en dedikerad, statisk IP-adress för din app. Om du vill få en statisk inkommande IP-adress måste du konfigurera en [IP-baserad SSL-bindning](configure-ssl-bindings.md#secure-a-custom-domain). Om du egentligen inte behöver SSL-funktioner för att skydda din app kan du till och med ladda upp ett självsignerat certifikat för den här bindningen. I en IP-baserad SSL-bindning är certifikatet bundet till själva IP-adressen, så App Service etablerar en statisk IP-adress för att det ska hända. 

## <a name="when-outbound-ips-change"></a>När utgående IPs ändras

Oavsett antalet utskalade instanser har varje app ett visst antal utgående IP-adresser vid en given tidpunkt. Alla utgående anslutningar från App Service-appen, till exempel till en backend-databas, använder en av de utgående IP-adresserna som ursprungs-IP-adress. Du kan inte veta i förväg vilken IP-adress en viss appinstans använder för att upprätta den utgående anslutningen, så backend-tjänsten måste öppna brandväggen för alla utgående IP-adresser i din app.

Uppsättningen med utgående IP-adresser för din app ändras när du skalar appen mellan de lägre nivåerna (**Basic,** **Standard**och **Premium**) och **Premium V2-nivån.**

Du kan hitta uppsättningen med alla möjliga utgående IP-adresser som appen kan använda, oavsett prisnivåer, genom att leta efter egenskapen `possibleOutboundIpAddresses` eller i fältet Ytterligare **utgående IP-adresser** i bladet **Egenskaper** i Azure-portalen. Se [Hitta utgående IPs](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Hitta utgående IPs

Om du vill ta reda på de utgående IP-adresser som för närvarande används av din app i Azure-portalen klickar du på **Egenskaper** i appens vänsternavigering. De visas i fältet **Utgående IP-adresser.**

Du kan hitta samma information genom att köra följande kommando i [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Om du vill hitta _alla_ möjliga utgående IP-adresser för din app, oavsett prisnivåer, klickar du på **Egenskaper** i appens vänstra navigering. De visas i fältet **Ytterligare utgående IP-adresser.**

Du kan hitta samma information genom att köra följande kommando i [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du begränsar inkommande trafik efter käll-IP-adresser.

> [!div class="nextstepaction"]
> [Statiska IP-begränsningar](app-service-ip-restrictions.md)
