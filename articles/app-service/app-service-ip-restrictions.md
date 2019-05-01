---
title: Begränsa åtkomst – Azure App Service | Microsoft Docs
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
ms.date: 04/22/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 558b67b5b0e1ce4f452ce2ca2e97dd7e785c80b6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728696"
---
# <a name="azure-app-service-access-restrictions"></a>Åtkomstbegränsningar för Azure App Service #

Åtkomstbegränsningar kan du definiera en ordnad tillåta/neka prioritetslistan som styr åtkomst till din app. Listan kan innehålla IP-adresser eller undernät för virtuella Azure-nätverk. När det finns en eller flera poster, är det sedan en implicit ”neka alla” som finns i slutet av listan.

Funktionen åtkomstbegränsningar fungerar med alla App Service värdbaserade work läser in inklusive. webbappar, API-appar, Linux-appar, appar i Linux-behållaren och funktioner.

När en begäran skickas till din app, ska från-adressen utvärderas mot IP-adressregler i listan över begränsningar för åtkomst. Om från-adressen är i ett undernät som är konfigurerad med Tjänsteslutpunkter till Microsoft.Web, jämförs källans undernät mot de virtuella nätverksreglerna i listan över begränsningar för åtkomst. Om adressen inte är tillåten åtkomst baserat på reglerna i listan, tjänsten svarar den med en [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) statuskod.

Funktion för åtkomst-begränsningar har införts i App Service frontend roller, som är överordnad worker-värd där koden körs. Därför är åtkomstbegränsningar i själva verket nätverk ACL: er.

Möjligheten att begränsa åtkomsten till din webbapp från Azure Virtual Network (VNet) anropas [tjänstslutpunkter][serviceendpoints]. Tjänstslutpunkter kan du begränsa åtkomsten till en multiklienttjänst från valda undernät. Den måste aktiveras på såväl nätverk sida som den tjänst som aktiveras med. 

![åtkomstflöde för begränsningar](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Lägga till och redigera åtkomstbegränsning regler i portalen ##

Om du vill lägga till en regel för begränsning av åtkomst till din app, använder du menyn för att öppna **nätverk**>**åtkomstbegränsningar** och klicka på **konfigurera åtkomstbegränsningar**

![Nätverksalternativ för App Service](media/app-service-ip-restrictions/access-restrictions.png)  

Du kan granska listan över åtkomstregler begränsning som definierats för din app från Gränssnittet för åtkomst-begränsningar.

![åtkomstbegränsningar för listan](media/app-service-ip-restrictions/access-restrictions-browse.png)

I listan visas alla de aktuella begränsningar som finns på din app. Om du har en VNet-begränsning på din app visar tabellen om Tjänsteslutpunkter har aktiverats för Microsoft.Web. När det finns inga definierade begränsningar för din app, kan din app nås från var som helst.  

Du kan klicka på **[+] Lägg till** att lägga till en ny regel för begränsning av åtkomst. När du lägger till en regel, börjar den gälla omedelbart. Regler tillämpas i prioritetsordning från och med lägst och ökar. Det finns en implicit neka allt som gäller när du lägger till och med en enda regel.

![Lägg till Begränsningsregel för en IP-åtkomst](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

När du skapar en regel kan välja du tillåta/Neka och också på vilken typ av regel. Du måste också ange prioritetsvärdet och vad du är att begränsa åtkomst till.  Du kan du lägga till ett namn och beskrivning för regeln.  

Om du vill ange en IP-adress baserat regeln, Välj en typ av IPv4 eller IPv6. IP-adress notation måste anges i CIDR-notation för både IPv4 och IPv6-adresser. Om du vill ange en exakt adress, kan du använda något som 1.2.3.4/32 där de fyra första oktetterna representerar din IP-adress och /32 är masken. IPv4 CIDR-notation för alla adresser är 0.0.0.0/0. Mer information om CIDR-notation, kan du läsa [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

![Lägg till Begränsningsregel för åtkomst till ett virtuellt nätverk](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Välj en typ av virtuellt nätverk för att begränsa åtkomsten till valda undernät. Nedan som du kan välja prenumeration, VNet och undernät som du vill tillåta eller neka åtkomst med. Om Tjänsteslutpunkter inte redan har aktiverats med Microsoft.Web för det undernät som du har valt, aktiveras den automatiskt åt dig, såvida inte kryssrutan där du ombeds inte att göra det. Situationen där du vill aktivera det på appen men inte i undernätet beror huvudsakligen på om du har behörighet att aktivera Tjänsteslutpunkter i undernät eller inte. Om du behöver hämta någon annan att aktivera Tjänsteslutpunkter i undernät kan du markera kryssrutan och har din app har konfigurerats för tjänstslutpunkter i avvaktan på att den aktiveras senare i undernät. 

Du kan klicka på en rad för att redigera en befintlig Begränsningsregel för åtkomst. Redigeringar är effektiva omedelbart med ändringar av prioritet ordning.

![Redigera en regel för begränsning av åtkomst](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

När du redigerar en regel kan ändra du inte typen mellan en regel för IP-adress och en regel för virtuella nätverk. 

![Redigera en regel för begränsning av åtkomst](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Ta bort en regel, klicka på den **...**  på regeln och klickar sedan på **ta bort**.

![ta bort åtkomstregel för begränsning](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>SCM-webbplatsen 

Förutom att du kan styra åtkomsten till din app, kan du också begränsa åtkomsten till scm-webbplatsen som används av din app. Scm-webbplatsen är webbdistribution slutpunkt och Kudu-konsolen. Separat kan du tilldela åtkomstbegränsningar till scm-webbplatsen från appen eller använda samma för både appen och scm-webbplatsen. När du markerar kryssrutan har samma begränsningar som din app är allt blanked ut. Om du avmarkerar kryssrutan, tillämpas de inställningar som du tidigare hade på scm-webbplatsen. 

![åtkomstbegränsningar för listan](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

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

IP-begränsningar finns för både Funktionsappar med samma funktioner som App Service-planer. Aktivera IP-begränsningar inaktiverar portal kodredigeraren för alla Otillåten IP-adresser.

[Läs mer här](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
