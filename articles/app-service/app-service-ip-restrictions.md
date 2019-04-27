---
title: Begränsa klienten IP-adresser – Azure App Service | Microsoft Docs
description: Hur du använder åtkomstbegränsningar med Azure App Service
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bb6ab29f02282a394e3f93e41682ceaec5208b75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853294"
---
# <a name="azure-app-service-static-access-restrictions"></a>Statisk åtkomstbegränsningar för Azure App Service #

Åtkomstbegränsningar kan du definiera en prioritet sorterade tillåta/Neka lista med IP-adresser som ska kunna komma åt din app. Listan över tillåtna kan innehålla IPv4 och IPv6-adresser. När det finns en eller flera poster, är det en implicit neka allt som finns i slutet av listan.

Funktionen åtkomstbegränsningar fungerar med alla App Service finns arbetsbelastningar, bland annat; webbappar, API-appar, Linux-appar, appar i Linux-behållaren och funktioner.

När en begäran skickas till din app, utvärderas från IP-adress mot listan över åtkomstbegränsningar. Om adressen inte är tillåten åtkomst baserat på reglerna i listan, tjänsten svarar den med en [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) statuskod.

Åtkomstbegränsningar-funktionen är implementerad i App Service frontend roller, som är överordnad worker-värd där koden körs. Därför är åtkomstbegränsningar i själva verket nätverk ACL: er.  

![åtkomstflöde för begränsningar](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Under en tid har åtkomstbegränsningar-funktionen i portalen ett skikt som ligger ovanpå den ipSecurity kapaciteten i IIS. Den aktuella kapaciteten för åtkomstbegränsningar är olika. Du kan fortfarande konfigurera ipSecurity i filen web.config för programmet men frontend baserat åtkomstbegränsningar reglerna tillämpas innan all trafik når IIS.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Lägga till och redigera åtkomstbegränsning regler i portalen ##

Om du vill lägga till en regel för begränsning av åtkomst till din app, använder du menyn för att öppna **nätverk**>**åtkomstbegränsningar** och klicka på **konfigurera åtkomstbegränsningar**

![Nätverksalternativ för App Service](media/app-service-ip-restrictions/ip-restrictions.png)  

Du kan granska listan över åtkomstregler begränsning som definierats för din app från Gränssnittet för åtkomst-begränsningar.

![åtkomstbegränsningar för listan](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Om dina regler har konfigurerats som den här bilden kan din app skulle bara att ta emot trafik från 131.107.159.0/24 och skulle avvisas från alla andra IP-adresser.

Du kan klicka på **[+] Lägg till** att lägga till en ny regel för begränsning av åtkomst. När du lägger till en regel, börjar den gälla omedelbart. Regler tillämpas i prioritetsordning från och med lägst och ökar. Det finns en implicit neka allt som gäller när du lägger till och med en enda regel.

![Lägg till en regel för begränsning av åtkomst](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP-adress notation måste anges i CIDR-notation för både IPv4 och IPv6-adresser. Om du vill ange en exakt adress, kan du använda något som 1.2.3.4/32 där de fyra första oktetterna representerar din IP-adress och /32 är masken. IPv4 CIDR-notation för alla adresser är 0.0.0.0/0. Mer information om CIDR-notation, kan du läsa [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Du kan klicka på en rad för att redigera en befintlig Begränsningsregel för åtkomst. Redigeringar är effektiva omedelbart med ändringar av prioritet ordning.

![Redigera en regel för begränsning av åtkomst](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Ta bort en regel, klicka på den **...**  på regeln och klickar sedan på **ta bort**.

![ta bort åtkomstregel för begränsning](media/app-service-ip-restrictions/ip-restrictions-delete.png)

Du kan också begränsa åtkomsten för distribution i nästa flik. Att lägga till/redigera/ta bort var och en regel kan följa samma steg som ovan.

![åtkomstbegränsningar för listan](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programmässig manipulation av regler för begränsning av åtkomst ##

Det för närvarande finns ingen CLI eller PowerShell för den nya funktionen för åtkomstbegränsningar, men värdena kan anges manuellt med en PUT-åtgärd för appkonfiguration i Resource Manager. Du kan använda resources.azure.com och redigera ipSecurityRestrictions-blocket för att lägga till JSON som krävs till exempel.

Platsen för den här informationen i Resource Manager är:

Management.Azure.com/subscriptions/**prenumerations-ID**/resourceGroups/**resursgrupper**/providers/Microsoft.Web/sites/**webbappnamnet**  /config/web? API-version = 2018-02-01

JSON-syntaxen för det tidigare exemplet är:

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>Funktionen App IP-restriktioner

IP-begränsningar finns för både Funktionsappar med samma funktioner som App Service-planer. Observera att aktiverar IP begränsningar inaktiveras portal kodredigeraren för alla Otillåten IP-adresser.

[Läs mer här](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)