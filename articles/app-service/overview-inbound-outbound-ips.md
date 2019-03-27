---
title: Inkommande/utgående IP-adresser – Azure App Service | Microsoft Docs
description: Beskriver hur inkommande och utgående IP-adresserna används i App Service och hur du hittar information om dem för din app.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 96f580532d9ea45dd767e32c2451243e83af66ea
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480812"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Inkommande och utgående IP-adresser i Azure App Service

[Azure App Service](overview.md) är en multiklienttjänst, förutom för [App Service-miljöer](environment/intro.md). Appar som inte är i en App Service-miljö (inte i den [isolerade nivån](https://azure.microsoft.com/pricing/details/app-service/)) dela nätverksinfrastruktur med andra appar. Därför inkommande och utgående IP-adresserna för en app kan vara olika och kan även ändra i vissa situationer. 

[App Service-miljöer](environment/intro.md) använda dedikerade nätverksinfrastrukturer, så att appar som körs i App Service environment får statiska, dedikerad IP-adresser både inkommande och utgående anslutningar.

## <a name="when-inbound-ip-changes"></a>När inkommande IP ändras

Oavsett vilket antal utskalade instanser av har varje app en inkommande IP-adress. Inkommande IP-adressen kan ändras när du utför en av följande åtgärder:

- Ta bort en app och återskapar den i en annan resursgrupp.
- Ta bort den sista appen i en resursgrupp _och_ region kombination och återskapa den.
- Ta bort en befintlig SSL-bindning som vid förnyelse av certifikat (se [förnya certifikat](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Få statiska inkommande IP-adress

Ibland kanske du vill en dedikerad, statisk IP-adress för din app. Om du vill ha en statisk inkommande IP-adress, måste du konfigurera en [IP-baserad SSL-bindning](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Om du inte verkligen behöver SSL-funktioner för att skydda din app, kan du även ladda upp ett självsignerat certifikat för den här bindningen. I en IP-baserad SSL-bindning binds certifikatet till IP-adressen, så App Service tillhandahåller en statisk IP-adress till att göra det direkt. 

## <a name="when-outbound-ips-change"></a>När utgående IP-adresser ändras

Oavsett vilket antal utskalade instanser av har varje app ett visst antal utgående IP-adresser vid en given tidpunkt. Alla utgående anslutningar från App Service-appen, till exempel en backend-databas, använder en av de utgående IP-adresserna som ursprunglig IP-adress. Du kan inte vet i förväg vilka IP-adressen en viss app-instansen kommer att använda och göra den utgående anslutningen om backend-tjänst måste öppna dess brandväggen till att alla utgående IP-adresser för din app.

Uppsättningen med utgående IP-adresser för din app ändras när du skalar din app mellan de lägre nivåerna (**grundläggande**, **Standard**, och **Premium**) och  **Premium V2** nivå.

Du kan hitta uppsättningen med alla möjliga utgående IP-adresser din app kan använda, oavsett prisnivån genom att söka efter den `possibleOutboundIPAddresses` egenskapen eller i den **ytterligare utgående IP-adresser** i den **egenskaper**  bladet i Azure-portalen. Se [hitta utgående IP-adresser](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Hitta utgående IP-adresser

För att hitta de utgående IP-adresser som för närvarande används av din app i Azure portal, klickar du på **egenskaper** i din app vänstra navigeringsfältet. De listas i den **utgående IP-adresser** fält.

Du kan hitta samma information genom att köra följande kommando i den [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Att hitta _alla_ möjliga utgående IP-adresser för din app, oavsett prisnivån klickar du på **egenskaper** i din app vänstra navigeringsfältet. De listas i den **ytterligare utgående IP-adresser** fält.

Du kan hitta samma information genom att köra följande kommando i den [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du begränsar inkommande trafik av källans IP-adresser.

> [!div class="nextstepaction"]
> [Statisk IP-adressbegränsningar](app-service-ip-restrictions.md)
