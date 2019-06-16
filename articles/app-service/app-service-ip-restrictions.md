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
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 2b0892fb107827cd9060a36855e9b8bf4416463c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069439"
---
# <a name="azure-app-service-access-restrictions"></a>Åtkomstbegränsningar för Azure App Service #

Åtkomstbegränsningar kan du definiera en ordnad tillåta/neka prioritetslistan som styr åtkomst till din app. Listan kan innehålla IP-adresser eller undernät för virtuella Azure-nätverk. När det finns en eller flera poster, är det sedan en implicit ”neka alla” som finns i slutet av listan.

Funktionen åtkomstbegränsningar fungerar med alla App Service värdbaserade work läser in inklusive. webbappar, API-appar, Linux-appar, appar i Linux-behållaren och funktioner.

När en begäran skickas till din app, ska från-adressen utvärderas mot IP-adressregler i listan över begränsningar för åtkomst. Om från-adressen är i ett undernät som är konfigurerad med Tjänsteslutpunkter till Microsoft.Web, jämförs källans undernät mot de virtuella nätverksreglerna i listan över begränsningar för åtkomst. Om adressen inte är tillåten åtkomst baserat på reglerna i listan, tjänsten svarar den med en [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) statuskod.

Funktion för åtkomst-begränsningar har införts i App Service frontend roller, som är överordnad worker-värd där koden körs. Därför är åtkomstbegränsningar i själva verket nätverk ACL: er.

Möjligheten att begränsa åtkomsten till din webbapp från Azure Virtual Network (VNet) anropas [tjänstslutpunkter][serviceendpoints]. Tjänstslutpunkter kan du begränsa åtkomsten till en multiklienttjänst från valda undernät. Den måste aktiveras på såväl nätverk sida som den tjänst som aktiveras med. Det fungerar inte för att begränsa trafik till appar som finns i en App Service Environment.  Om du använder en App Service Environment kan styra du åtkomsten till din app med regler för IP-adress.

![åtkomstflöde för begränsningar](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Lägga till och redigera åtkomstbegränsning regler i portalen ##

Om du vill lägga till en regel för begränsning av åtkomst till din app, använder du menyn för att öppna **nätverk**>**åtkomstbegränsningar** och klicka på **konfigurera åtkomstbegränsningar**

![Nätverksalternativ för App Service](media/app-service-ip-restrictions/access-restrictions.png)  

Du kan granska listan över åtkomstregler begränsning som definierats för din app från Gränssnittet för åtkomst-begränsningar.

![åtkomstbegränsningar för listan](media/app-service-ip-restrictions/access-restrictions-browse.png)

I listan visas alla de aktuella begränsningar som finns på din app. Om du har en VNet-begränsning på din app visar tabellen om Tjänsteslutpunkter har aktiverats för Microsoft.Web. När det finns inga definierade begränsningar för din app, kan din app nås från var som helst.  

## <a name="adding-ip-address-rules"></a>Att lägga till regler för IP-adress

Du kan klicka på **[+] Lägg till** att lägga till en ny regel för begränsning av åtkomst. När du lägger till en regel, börjar den gälla omedelbart. Regler tillämpas i prioritetsordning från och med lägst och ökar. Det finns en implicit neka allt som gäller när du lägger till och med en enda regel.

När du skapar en regel kan välja du tillåta/Neka och också på vilken typ av regel. Du måste också ange prioritetsvärdet och vad du är att begränsa åtkomst till.  Du kan du lägga till ett namn och beskrivning för regeln.  

![Lägg till Begränsningsregel för en IP-åtkomst](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Om du vill ange en IP-adress baserat regeln, Välj en typ av IPv4 eller IPv6. IP-adress notation måste anges i CIDR-notation för både IPv4 och IPv6-adresser. Om du vill ange en exakt adress, kan du använda något som 1.2.3.4/32 där de fyra första oktetterna representerar din IP-adress och /32 är masken. IPv4 CIDR-notation för alla adresser är 0.0.0.0/0. Mer information om CIDR-notation, kan du läsa [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Tjänstslutpunkter

Tjänstslutpunkter kan du begränsa åtkomsten till valda Azure-nätverk undernät. Skapa en begränsningsregel med en typ av virtuellt nätverk för att begränsa åtkomsten till ett specifikt undernät. Du kan välja prenumeration, VNet och undernät som du vill tillåta eller neka åtkomst med. Om Tjänsteslutpunkter inte redan har aktiverats med Microsoft.Web för det undernät som du har valt, aktiveras den automatiskt åt dig, såvida inte kryssrutan där du ombeds inte att göra det. Situationen där du vill aktivera det på appen men inte i undernätet beror huvudsakligen på om du har behörighet att aktivera Tjänsteslutpunkter i undernät eller inte. Om du behöver hämta någon annan att aktivera Tjänsteslutpunkter i undernät kan du markera kryssrutan och har din app har konfigurerats för tjänstslutpunkter i avvaktan på att den aktiveras senare i undernät. 

![Lägg till Begränsningsregel för åtkomst till ett virtuellt nätverk](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Tjänstslutpunkter kan inte användas för att begränsa åtkomsten till appar som körs i en App Service Environment. När appen är i en App Service Environment, kan du styra åtkomsten till din app med regler för IP-åtkomst. 

Med tjänstslutpunkter kan konfigurera du din app med Application Gateways eller andra WAF-enheter. Du kan också konfigurera flernivåprogram med säker serverdelar. Mer information på några av möjligheterna [nätverksfunktioner och App Service](networking-features.md).

## <a name="managing-access-restriction-rules"></a>Hantera åtkomstregler för begränsning

Du kan klicka på en rad för att redigera en befintlig Begränsningsregel för åtkomst. Redigeringar är effektiva omedelbart med ändringar av prioritet ordning.

![Redigera en regel för begränsning av åtkomst](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

När du redigerar en regel kan ändra du inte typen mellan en regel för IP-adress och en regel för virtuella nätverk. 

![Redigera en regel för begränsning av åtkomst](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Ta bort en regel, klicka på den **...**  på regeln och klickar sedan på **ta bort**.

![ta bort åtkomstregel för begränsning](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blockera en IP-adress ##

När du lägger till din första IP-begränsning regeln tjänsten ska lägga till en explicit **neka alla** regel med prioritet 2147483647. I praktiken den explicita **neka alla** regel kommer att senaste regeln har körts och blockerar åtkomst till alla IP-adresser som uttryckligen inte tillåts med hjälp av en **Tillåt** regeln.

Scenariot där användare vill uttryckligen blockera en enskild IP-adress eller IP-Adressblock, men tillåter du att allt annat åtkomst, det är nödvändigt att lägga till en explicit **Tillåt alla** regeln.

![enkel ip-adress](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM-webbplatsen 

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
