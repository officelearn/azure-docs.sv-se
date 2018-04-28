---
title: IP-adresser i Azure App Service | Microsoft Docs
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
ms.openlocfilehash: 906a5d511615c57b6ff807ac240a838c63917e66
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Inkommande och utgående IP-adresser i Azure App Service

[Azure Apptjänst](app-service-web-overview.md) är en tjänst för flera innehavare, förutom för [Apptjänstmiljöer](environment/intro.md). Appar som inte ingår i en Apptjänst-miljö (inte i den [isolerade nivå](https://azure.microsoft.com/pricing/details/app-service/)) resursen nätverksinfrastruktur med andra appar. Därför inkommande och utgående IP-adresser för en app kan vara olika och kan även ändra i vissa situationer. 

[Apptjänstmiljöer](environment/intro.md) använda dedicerade nätverksinfrastrukturer, så att appar som körs i en Apptjänst-miljö får statiska dedicerade IP-adresser både inkommande och utgående anslutningar.

## <a name="when-inbound-ip-changes"></a>När inkommande IP ändras

Oavsett vilket antal utskalat instanser för har varje app en inkommande IP-adress. Inkommande IP-adressen kan ändras när du utför någon av följande åtgärder:

- Ta bort en app och återskapa det i en annan resursgrupp.
- Ta bort den sista appen i en resursgrupp _och_ region kombination och återskapa den.
- Ta bort en befintlig SSL-bindning som vid förnyelse av certifikat (se [förnya certifikat](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Hämta inkommande statisk IP

Ibland kanske du vill en dedikerad, statisk IP-adress för din app. För att få en statisk inkommande IP-adress, måste du konfigurera en [IP-baserade SSL-bindning](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Om du inte verkligen behöver SSL-funktioner för att skydda din app kan överföra du även ett självsignerat certifikat för den här bindningen. I en IP-baserade SSL-bindning bunden certifikatet till IP-adressen, så App Service tillhandahåller en statisk IP-adress så att det händer. 

## <a name="when-outbound-ips-change"></a>När utgående IP-adresser ändras

Oavsett vilket antal utskalat instanser för har varje app ett visst antal utgående IP-adresser vid en given tidpunkt. En utgående anslutning från App Service-appen, till exempel en backend-databas, använder en utgående IP-adresser som den ursprungliga IP-adressen. Du kan inte vet i förväg vilka IP-adressen en viss app-instansen ska använda utgående anslutning utan backend-tjänst måste öppna dess brandväggen till att alla utgående IP-adresser för din app.

Uppsättningen med utgående IP-adresserna för dina appändringar när du skalar appen mellan lägre nivåer (**grundläggande**, **Standard**, och **Premium**) och  **Premium-V2** nivå.

Du kan hitta uppsättningen av alla möjliga utgående IP-adresser din app kan använda oavsett prisnivåer genom att söka efter den `possibleOutboundIPAddresses` egenskapen. Se [hitta utgående IP-adresser](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Sök efter utgående IP-adresser

Sök efter utgående IP-adresser som för närvarande används av din app i Azure-portalen, klicka på **egenskaper** i din app vänstra navigeringsfönstret. 

Du kan hitta samma information genom att köra följande kommando i den [moln Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

Du hittar alla möjliga utgående IP-adresser för din app, oavsett prisnivåer, kör följande kommando den [moln Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att begränsa inkommande trafik genom käll-IP-adresser.

> [!div class="nextstepaction"]
> [Statiska IP-adressbegränsningar](app-service-ip-addresses.md)
