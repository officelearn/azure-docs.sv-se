---
title: Inkommande/utgående IP-adresser
description: Lär dig hur inkommande och utgående IP-adresser används i Azure App Service när de ändras och hur du hittar adresserna för din app.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373910"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Inkommande och utgående IP-adresser i Azure App Service

[Azure App Service](overview.md) är en tjänst för flera innehavare, förutom [App Service miljöer](environment/intro.md). Appar som inte finns i en App Service miljö (inte på den [isolerade nivån](https://azure.microsoft.com/pricing/details/app-service/)) delar nätverks infrastruktur med andra appar. Därför kan inkommande och utgående IP-adresser för en app vara olika och kan till och med ändras i vissa situationer. 

[App Service miljöer](environment/intro.md) använder dedikerade nätverks infrastrukturer, så appar som körs i en app service-miljö får statiska, dedikerade IP-adresser både för inkommande och utgående anslutningar.

## <a name="when-inbound-ip-changes"></a>När inkommande IP-ändringar

Oavsett antalet utskalade instanser har varje app en enskild inkommande IP-adress. Den inkommande IP-adressen kan ändras när du utför någon av följande åtgärder:

- Ta bort en app och återskapa den i en annan resurs grupp.
- Ta bort den sista appen i en kombination av resurs grupp _och_ region och återskapa den.
- Ta bort en befintlig SSL-bindning, till exempel vid certifikat förnyelse (se [Förnya certifikat](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Hitta den inkommande IP-adressen

Kör bara följande kommando i en lokal Terminal:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Hämta en statisk inkommande IP-adress

Ibland kanske du vill ha en dedikerad statisk IP-adress för din app. Om du vill hämta en statisk inkommande IP-adress måste du konfigurera en [IP-baserad SSL-bindning](configure-ssl-bindings.md#secure-a-custom-domain). Om du inte behöver SSL-funktioner för att skydda din app kan du till och med Ladda upp ett självsignerat certifikat för den här bindningen. I en IP-baserad SSL-bindning är certifikatet bundet till själva IP-adressen, så App Service etablerar en statisk IP-adress så att den sker. 

## <a name="when-outbound-ips-change"></a>När utgående IP-ändringar ändras

Oavsett antalet utskalade instanser har varje app ett angivet antal utgående IP-adresser vid en angiven tidpunkt. Utgående anslutningar från App Service-appen, till exempel till en backend-databas, använder en av de utgående IP-adresserna som ursprungs-IP-adress. Du kan inte veta i förväg vilken IP-adress en specifik App-instans kommer att använda för att göra den utgående anslutningen, så din backend-tjänst måste öppna brand väggen till alla utgående IP-adresser för din app.

Uppsättningen utgående IP-adresser för din app ändras när du skalar appen mellan de lägre nivåerna (**Basic**, **standard**och **Premium**) och **Premium v2** -nivån.

Du hittar uppsättningen med alla möjliga utgående IP-adresser som din app kan använda, oavsett pris nivå, genom att leta efter `possibleOutboundIpAddresses` egenskap eller i fältet **ytterligare utgående IP-adresser** på bladet **Egenskaper** i Azure Portal. Se [hitta utgående IP-adresser](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Hitta utgående IP-adresser

Du hittar de utgående IP-adresser som används av din app i Azure Portal genom att klicka på **Egenskaper** i appens vänstra navigering. De visas i fältet **utgående IP-adresser** .

Du kan hitta samma information genom att köra följande kommando i [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Om du vill hitta _alla_ möjliga utgående IP-adresser för din app, oavsett pris nivå, klickar du på **Egenskaper** i appens vänstra navigering. De visas i fältet **ytterligare utgående IP-adresser** .

Du kan hitta samma information genom att köra följande kommando i [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du begränsar inkommande trafik efter Källans IP-adresser.

> [!div class="nextstepaction"]
> [Statiska IP-begränsningar](app-service-ip-restrictions.md)
