---
title: "Värd för flera platser med Azure Programgateway | Microsoft Docs"
description: "Den här sidan innehåller instruktioner för att konfigurera en befintlig Azure-program-gateway som värd för flera webbprogram på samma gateway med Azure-portalen."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 28a7fcb3e08a9c4b6a27e9fbc8d3ebae309adc62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Konfigurera en befintlig Programgateway som värd för flera webbprogram

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-multisite-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

Värd för flera plats kan du distribuera flera webbprogram på samma programgatewayen. Det är beroende av förekomsten av värdhuvudet i den inkommande HTTP-begäranden att avgöra vilka lyssnare skulle ta emot trafik. Lyssnaren dirigerar sedan trafik till lämplig serverdelspool som konfigurerats i regler definitionen av gatewayen. I SSL aktiverat webbprogram Programgateway förlitar sig på servern Servernamnsindikation (SNI)-tillägg för att välja rätt lyssnaren för Internet-trafik. Ett vanligt användningsområde för värd för flera plats är att belastningsutjämna förfrågningar för olika webbdomäner till olika backend-serverpooler. På samma sätt kan flera underdomäner på samma rotdomänen också finnas på samma programgatewayen.

## <a name="scenario"></a>Scenario

I följande exempel Programgateway betjäna trafik för contoso.com och fabrikam.com med två backend-server-adresspooler: contoso-serverpoolen och fabrikam-serverpoolen. Liknande installationsprogrammet kan användas för att värden underdomäner som app.contoso.com och blog.contoso.com.

![Multisite scenario][multisite]

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot lägger till stöd för flera platser i en befintlig Programgateway. En befintlig Programgateway måste kunna konfigureras för att slutföra det här scenariot. Besök [skapa en Programgateway med hjälp av portalen](application-gateway-create-gateway-portal.md) att lära dig hur du skapar en basic-Programgateway i portalen.

Här följer de steg som krävs för att uppdatera programgatewayen:

1. Skapa backend-pooler för varje plats.
2. Skapa en lyssnare för varje plats Programgateway stöder.
3. Skapa regler för att mappa varje lyssnare med lämpliga serverdel.

## <a name="requirements"></a>Krav

* **Backend-serverpool:** Listan med IP-adresser för backend-servrarna. IP-adresserna som anges bör antingen tillhöra det virtuella undernätet eller vara en offentlig IP-/VIP-adress. FQDN kan också användas.
* **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Frontend-port:** Den här porten är den offentliga porten som är öppen på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning). För flera platser aktiverade programmet gateway läggs värdnamn och SNI indikatorer till.
* **Regel:** regeln Binder lyssnaren poolen backend-server och definierar vilka backend-serverpoolen trafiken ska dirigeras till när den når en viss lyssnare. Regler bearbetas i angiven ordning och trafik dirigeras via den första regeln som matchar oavsett särskilda egenskaper. Till exempel om du har en regel med hjälp av en grundläggande lyssnare och en regel med en flera platser lyssnare båda på samma port måste regeln med flera platser lyssnaren anges innan en regel med grundläggande lyssnare för flera platser regeln för att fungera som förväntat. 
* **Certifikat:** varje lyssnare kräver ett unikt certifikat, i det här exemplet skapas 2-lyssnare för flera platser. Två PFX-certifikat och lösenord för att de måste skapas.

## <a name="create-back-end-pools-for-each-site"></a>Skapa backend-pooler för varje plats

En backend-poolen för varje plats att programmet gateway stöder krävs, i det här fallet 2 är att skapa en för contoso11.com och en för fabrikam11.com.

### <a name="step-1"></a>Steg 1

Gå till en befintlig Programgateway i Azure-portalen (https://portal.azure.com). Välj **serverdelspooler** och på **Lägg till**

![Lägg till serverdelspooler][7]

### <a name="step-2"></a>Steg 2

Fyll i information om backend-poolen **pool1**, lägger till ip-adresser eller FQDN för backend-servrar och på **OK**

![backend pool1 poolinställningarna][8]

### <a name="step-3"></a>Steg 3

Klicka på bladet serverdelspooler **Lägg till** att lägga till en ytterligare backend-adresspool **pool2**, lägga till ip-adresser eller FQDN för backend-servrar och klicka på **OK**

![backend pool2 poolinställningarna][9]

## <a name="create-listeners-for-each-back-end"></a>Skapa lyssnare för varje serverdel

Application Gateway förlitar sig på HTTP 1.1 värdhuvuden för att ha mer än en webbplats på samma offentliga IP-adress och port. Den grundläggande lyssnare har skapats i portalen innehåller inte den här egenskapen.

### <a name="step-1"></a>Steg 1

Klicka på **lyssnare** i befintliga Programgateway och på **flera platser** att lägga till den första lyssnaren.

![lyssnare översikt bladet][1]

### <a name="step-2"></a>Steg 2

Fyll i informationen för lyssnaren. I det här exemplet SSL-avslutning har konfigurerats, skapa en ny frontend-port. Ladda upp PFX-certifikat som ska användas för SSL-avslutning. Den enda skillnaden på det här bladet jämfört med standard grundläggande lyssnare bladet är värdnamnet.

![lyssnare egenskapsbladet][2]

### <a name="step-3"></a>Steg 3

Klicka på **flera platser** och skapa en annan lyssnare enligt beskrivningen i föregående steg för den andra platsen. Se till att använda ett annat certifikat för andra lyssnaren. Den enda skillnaden på det här bladet jämfört med standard grundläggande lyssnare bladet är värdnamnet. Fyll i informationen för lyssnaren och klickar på **OK**.

![lyssnare egenskapsbladet][3]

> [!NOTE]
> Skapa lyssnare i Azure-portalen för Programgateway är en tidskrävande uppgift kan det ta lite tid att skapa två lyssnare i det här scenariot. När du är klar visas lyssnare i portalen som visas i följande bild:

![Översikt över lyssnare][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>Skapa regler för att mappa lyssnare till serverdelspooler

### <a name="step-1"></a>Steg 1

Gå till en befintlig Programgateway i Azure-portalen (https://portal.azure.com). Välj **regler** och välj befintlig Standardregeln **regel 1** och på **redigera**.

### <a name="step-2"></a>Steg 2

Fyll i bladet regler som visas i följande bild. Välja första lyssnare och första poolen och klicka på **spara** när du är klar.

![Redigera en befintlig regel][6]

### <a name="step-3"></a>Steg 3

Klicka på **regel** att skapa den andra regeln. Fyll i formuläret med andra lyssnare och andra serverdelspoolen och klicka på **OK** att spara.

![Lägg till regel bladet][10]

Det här scenariot har slutförts konfigurerar en Programgateway med befintliga med stöd för flera platser via Azure-portalen.

## <a name="next-steps"></a>Nästa steg

Lär dig att skydda dina webbplatser med [Programgateway - Brandvägg för webbaserade program](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png
