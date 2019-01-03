---
title: Begränsa klienten IP-adresser – Azure App Service | Microsoft Docs
description: Hur du använder IP-restriktioner med Azure App Service
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
ms.openlocfilehash: 337d71c84ace7f44c2668cf2344d9083c4a85bee
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651070"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Statisk IP-begränsningar för Azure App Service #

IP-restriktioner kan du definiera en prioritet sorterade tillåta/Neka lista med IP-adresser som ska kunna komma åt din app. Listan över tillåtna kan innehålla IPv4 och IPv6-adresser. När det finns en eller flera poster, är det en implicit neka allt som finns i slutet av listan. 

Begränsningar för IP-funktionen fungerar med alla App Service finns arbetsbelastningar, bland annat; webbappar, API-appar, Linux-appar, appar i Linux-behållaren och funktioner. 

När en begäran skickas till din app, utvärderas från IP-adress mot listan över IP-begränsningar. Om adressen inte är tillåten åtkomst baserat på reglerna i listan, tjänsten svarar den med en [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) statuskod.

Begränsningar för IP-funktionen är implementerad i App Service frontend roller, som är överordnad worker-värd där koden körs. IP-begränsningar är därför effektivt nätverk ACL: er.  

![Begränsningar för IP-flöde](media/app-service-ip-restrictions/ip-restrictions-flow.png)

Under en tid har begränsningar för IP-funktionen i portalen ett skikt som ligger ovanpå den ipSecurity kapaciteten i IIS. Aktuella begränsningar för IP-funktionen är olika. Du kan fortfarande konfigurera ipSecurity i filen web.config för programmet men frontend baserat IP-restriktioner reglerna tillämpas innan all trafik når IIS.

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>Lägga till och redigera regler för IP-begränsning i portalen ##

Om du vill lägga till en regel för IP-begränsning i din app använder du menyn för att öppna **nätverk**>**IP-restriktioner** och klicka på **konfigurera IP-adressbegränsningar**

![Nätverksalternativ för App Service](media/app-service-ip-restrictions/ip-restrictions.png)  

Du kan använda Gränssnittet för IP-begränsningar för att granska listan över regler som IP-begränsning har definierats för din app.

![lista över IP-restriktioner](media/app-service-ip-restrictions/ip-restrictions-browse.png)

Om dina regler har konfigurerats som den här bilden kan din app skulle bara att ta emot trafik från 131.107.159.0/24 och skulle avvisas från alla andra IP-adresser.

Du kan klicka på **[+] Lägg till** att lägga till en ny regel för IP-begränsning. När du lägger till en regel, börjar den gälla omedelbart. Regler tillämpas i prioritetsordning från och med lägst och ökar. Det finns en implicit neka allt som gäller när du lägger till och med en enda regel. 

![Lägg till en regel för IP-begränsning](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP-adress notation måste anges i CIDR-notation för både IPv4 och IPv6-adresser. Om du vill ange en exakt adress, kan du använda något som 1.2.3.4/32 där de fyra första oktetterna representerar din IP-adress och /32 är masken. IPv4 CIDR-notation för alla adresser är 0.0.0.0/0. Mer information om CIDR-notation, kan du läsa [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).  

Du kan klicka på en rad för att redigera en befintlig IP-begränsning av regel. Redigeringar är effektiva omedelbart med ändringar av prioritet ordning.

![Redigera en regel för IP-begränsning](media/app-service-ip-restrictions/ip-restrictions-edit.png)

Ta bort en regel, klicka på den **...**  på regeln och klickar sedan på **ta bort**. 

![ta bort regeln för IP-begränsning](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>Programmässig manipulation av regler för IP-begränsning ##

Det för närvarande finns ingen CLI eller PowerShell för den nya funktionen för IP-begränsningar, men värdena kan anges manuellt med en PUT-åtgärd för appkonfiguration i Resource Manager. Du kan använda resources.azure.com och redigera ipSecurityRestrictions-blocket för att lägga till JSON som krävs till exempel. 

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
