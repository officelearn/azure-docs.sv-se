---
title: Begränsa åtkomsten för IP-adresser
description: Lär dig hur du skyddar din app i Azure App Service genom uttryckligen vit listning klient-IP-adresser eller adress intervall.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 64ce74c84f8f69e72510be76a1309e1a5ea42f2f
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672171"
---
# <a name="azure-app-service-access-restrictions"></a>Begränsningar för Azure App Service åtkomst #

Med åtkomst begränsningar kan du definiera en prioriterad lista över tillåtna/nekade listor som styr nätverks åtkomsten till din app. Listan kan innehålla IP-adresser eller Azure Virtual Network-undernät. När det finns en eller flera poster finns det en implicit "Neka alla" som finns i slutet av listan.

Funktionen åtkomst begränsningar fungerar med alla App Service värdbaserade arbets belastningar, inklusive; webbappar, API Apps, Linux-appar, Linux container Apps och functions.

När en begäran görs till din app, utvärderas från-adressen mot IP-adressbegränsningar i listan åtkomst begränsningar. Om från-adressen finns i ett undernät som har kon figurer ATS med tjänstens slut punkter till Microsoft. Web, jämförs käll under nätet med de virtuella nätverks reglerna i listan med åtkomst begränsningar. Om adressen inte tillåts åtkomst baserat på reglerna i listan, svarar tjänsten med en status kod för [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

Funktionen åtkomst begränsningar implementeras i App Service-frontend-roller, som är överordnade arbets värdar där koden körs. Åtkomst begränsningarna är därför effektiva nätverks-ACL: er.

Möjligheten att begränsa åtkomsten till din webbapp från en Azure-Virtual Network (VNet) kallas [tjänst slut punkter][serviceendpoints]. Med tjänst slut punkter kan du begränsa åtkomsten till en tjänst för flera innehavare från valda undernät. Den måste vara aktive rad på både nätverks sidan och tjänsten som den aktive ras med. Det fungerar inte för att begränsa trafik till appar som finns i en App Service-miljön.  Om du befinner dig i ett App Service-miljön kan du kontrol lera åtkomsten till din app med IP-regler.

![flöde för åtkomst begränsningar](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Lägga till och redigera regler för åtkomst begränsning i portalen ##

Om du vill lägga till en regel för åtkomst begränsning i appen kan du använda menyn för att öppna **nätverks**>**åtkomst begränsningar** och klicka på **Konfigurera åtkomst begränsningar**

![App Service nätverks alternativ](media/app-service-ip-restrictions/access-restrictions.png)  

I användar gränssnittet för åtkomst begränsningar kan du granska listan över regler för åtkomst begränsning som definierats för din app.

![lista åtkomst begränsningar](media/app-service-ip-restrictions/access-restrictions-browse.png)

I listan visas alla aktuella begränsningar som finns i din app. Om du har en VNet-begränsning i appen visar tabellen om tjänstens slut punkter är aktiverade för Microsoft. Web. När det inte finns några definierade begränsningar för appen kommer appen att vara tillgänglig från valfri plats.  

## <a name="adding-ip-address-rules"></a>Lägger till IP-adress regler

Du kan klicka på **[+] Lägg till** för att lägga till en ny regel för begränsning av åtkomst. När du lägger till en regel börjar den gälla omedelbart. Regler tillämpas i prioritetsordning med början från det lägsta antalet och fortsätter. Det finns en implicit neka allt som gäller när du lägger till en enda regel.

När du skapar en regel måste du välja Tillåt/neka och även typ av regel. Du måste också ange prioritet svärdet och vad du begränsar åtkomsten till.  Du kan också lägga till ett namn och en beskrivning av regeln.  

![Lägg till en begränsnings regel för IP-åtkomst](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Om du vill ange en IP-baserad regel väljer du en typ av IPv4 eller IPv6. IP-adress notation måste anges i CIDR-notering för både IPv4-och IPv6-adresser. Om du vill ange en exakt adress kan du använda något som 1.2.3.4/32 där de första fyra oktetterna representerar din IP-adress och/32 är masken. IPv4 CIDR-noteringen för alla adresser är 0.0.0.0/0. Om du vill lära dig mer om CIDR-notering kan du läsa [Interplatsroutning mellan domäner](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Tjänstslutpunkter

Med tjänst slut punkter kan du begränsa åtkomsten till de valda Azure-undernäten för virtuella nätverk. Om du vill begränsa åtkomsten till ett speciellt undernät skapar du en begränsnings regel med en typ av Virtual Network. Du kan välja prenumeration, VNet och undernät som du vill tillåta eller neka åtkomst med. Om tjänst slut punkter inte redan har Aktiver ATS med Microsoft. Web för det undernät som du har valt aktive ras den automatiskt åt dig om du inte markerar kryss rutan där du inte vill göra det. Situationen där du vill aktivera det i appen, men inte under nätet är i stort sett relaterat till om du har behörighet att aktivera tjänstens slut punkter i under nätet eller inte. Om du behöver få någon annan att aktivera tjänstens slut punkter i under nätet kan du markera kryss rutan och låta appen vara konfigurerad för tjänst slut punkter i förväntat att den aktive ras senare i under nätet. 

![Lägg till begränsnings regel för VNet-åtkomst](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Tjänst slut punkter kan inte användas för att begränsa åtkomsten till appar som körs i en App Service-miljön. När din app är i ett App Service-miljön, kan du kontrol lera åtkomsten till din app med IP-regler för åtkomst. 

Med tjänst slut punkter kan du konfigurera din app med programgatewayer eller andra WAF-enheter. Du kan också konfigurera flera nivåer med säkra server delar. För ytterligare information om några av möjligheterna, Läs [nätverksfunktioner och App Service](networking-features.md).

## <a name="managing-access-restriction-rules"></a>Hantera regler för åtkomst begränsning

Du kan klicka på en rad om du vill redigera en befintlig regel för begränsning av åtkomst. Redigeringarna träder i kraft direkt, inklusive ändringar i prioritets ordning.

![Redigera en regel för begränsning av åtkomst](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

När du redigerar en regel kan du inte ändra typen mellan en IP-serveradress och en Virtual Network regel. 

![Redigera en regel för begränsning av åtkomst](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Om du vill ta bort en regel klickar du på **...** i regeln och klickar sedan på **ta bort**.

![ta bort regeln för åtkomst begränsning](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blockera en enskild IP-adress ##

När du lägger till din första regel för begränsning av IP-begränsning, lägger tjänsten till en explicit **Neka alla** -regel med prioritet 2147483647. I praktiken kommer den uttryckliga regeln **Neka alla** att köras och blockerar åtkomsten till en IP-adress som inte uttryckligen tillåts med en **Tillåt** -regel.

För scenariot där användare uttryckligen vill blockera en enskild IP-adress eller ett IP-adressblock, men ge allt annat åtkomst, är det nödvändigt att lägga till en explicit **Tillåt alla** regler.

![blockera enskild IP-adress](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM-webbplats 

Förutom att kunna kontrol lera åtkomsten till din app kan du också begränsa åtkomsten till den SCM-webbplats som används av din app. SCM-platsen är webb distributions slut punkten och även kudu-konsolen. Du kan separat tilldela åtkomst begränsningar till SCM-webbplatsen från appen eller använda samma uppsättning för både appen och SCM-platsen. När du markerar kryss rutan för att ha samma begränsningar som appen är allt tomt. Om du avmarkerar kryss rutan tillämpas de tidigare inställningarna på SCM-platsen. 

![lista åtkomst begränsningar](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Program mässig modifiering av regler för åtkomst begränsning ##

Det finns för närvarande ingen CLI eller PowerShell för den nya funktionen för åtkomst begränsningar, men värdena kan anges manuellt med en åtgärd för att lägga till [Azure-REST API](https://docs.microsoft.com/rest/api/azure/) i app-konfigurationen i Resource Manager. Som exempel kan du använda resources.azure.com och redigera ipSecurityRestrictions-blocket för att lägga till den nödvändiga JSON-filen.

Platsen för den här informationen i Resource Manager är:

management.azure.com/subscriptions/**prenumerations-ID**/resourceGroups/**resurs grupper**/providers/Microsoft.Web/Sites/**Web App Name**/config/Web? API-version = 2018-02-01

JSON-syntaxen för det tidigare exemplet är:

    {
      "properties": {
        "ipSecurityRestrictions": [
          {
            "ipAddress": "122.133.144.0/24",
            "action": "Allow",
            "tag": "Default",
            "priority": 100,
            "name": "IP example rule"
          }
        ]
      }
    }

## <a name="function-app-ip-restrictions"></a>Funktionsapp IP-begränsningar

IP-begränsningar är tillgängliga för båda funktionerna med samma funktioner som App Service planer. Om du aktiverar IP-begränsningar inaktive ras Portal kod redigeraren för alla otillåtna IP-adresser.

[Läs mer här](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
