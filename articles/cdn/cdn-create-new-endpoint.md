---
title: "Komma igång med Azure CDN | Microsoft Docs"
description: "Det här avsnittet beskriver hur du aktiverar Azure Content Delivery Network (CDN). Självstudiekursen beskriver steg för steg hur du skapar en ny CDN-profil och CDN-slutpunkt."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2018
ms.author: mazha
ms.openlocfilehash: 81a88f6495ca9092ca3b55b8ffb3e41def3b4623
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="getting-started-with-azure-cdn"></a>Komma igång med Azure CDN
I den här artikeln beskrivs hur du aktiverar Azure Content Delivery Network (CDN) genom att skapa en ny profil och slutpunkt för CDN.

> [!IMPORTANT]
> En introduktion till CDN och en lista över funktioner finns i [CDN-översikt](cdn-overview.md).
> 
> 

## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil
En CDN-profil är en samling CDN-slutpunkter. Varje profil kan innehålla en eller flera CDN-slutpunkter. Du kan organisera dina CDN-slutpunkter efter internetdomän, webbapp eller något annat kriterium genom att använda flera profiler.

> [!NOTE]
> En Azure-prenumeration har standardgränser för följande resurser:
> - Antalet CDN-profiler som kan skapas
> - Antalet slutpunkter som kan skapas i en CDN-profil 
> - Antal anpassade domäner som kan mappas till en slutpunkt
>
> Information om CDN-prenumerationsbegränsningar finns i [CDN limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits) (CDN-begränsningar).
>
> CDN-priserna tillämpas på CDN-profilnivå. Om du vill använda en blandning av olika Azure CDN-prisnivåer måste du därför skapa flera CDN-profiler.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Skapa en ny CDN-slutpunkt
**Så här skapar du en ny CDN-slutpunkt**

1. Gå till din CDN-profil på [Azure-portalen](https://portal.azure.com). Du kanske fäste den på instrumentpanelen i föregående steg. Om inte så kan du hitta den genom att välja **Alla tjänster** och sedan **CDN-profiler**. Välj den profil du vill lägga till på slutpunkten i fönstret **CDN-profiler**. 
   
    Fönstret för CDN-profilen visas.
   
    ![CDN-profil][cdn-profile-settings]

2. Välj **Slutpunkt**.
   
    ![Knappen Lägg till slutpunkt][cdn-new-endpoint-button]
   
    Fönstret **Lägg till slutpunkt** visas.
   
    ![Fönstret Lägg till slutpunkt][cdn-add-endpoint]

3. I **Namn** anger du ett unikt namn för den nya CDN-slutpunkten. Det här namnet används för att komma åt dina cachelagrade resurser i domänen `<endpointname>.azureedge.net`.

4. I **Typ av ursprung** väljer du en typ av ursprung. Välj **Storage** för ett Azure Storage-konto, **Cloud Services** för ett Azure Cloud Services-konto, **Web Apps** för ett Azure Web Apps-konto eller **Anpassat ursprung** för ett annat ursprung i form av en offentligt tillgänglig webbserver (som finns i Azure eller någon annanstans).
   
    ![Ursprungstyp för CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. I **Ursprungets värdnamn** väljer du eller anger din ursprungsdomän. I listrutan visas en lista med alla tillgängliga ursprung av den typ du angav i steg 4. Om du väljer **Anpassat ursprung** som typ av ursprung anger du domänen för ditt anpassade ursprung.
    
6. I **Sökväg till ursprung** anger du sökvägen till de resurser du vill cachelagra, eller så lämnar du fältet tomt om du vill tillåta cachelagring av alla resurser i domänen du angav i steg 5.
    
7. I **Ursprungsvärdadress** anger du värdhuvudet som du vill att Azure CDN ska skicka med varje begäran, eller så lämnar du standardvärdet.
   
   > [!WARNING]
   > Vissa typer av ursprung, till exempel Azure Storage och Web Apps, kräver att värdhuvudet matchar ursprungets domän. Lämna standardvärdet såvida du inte har ett ursprung som kräver ett värdhuvud som skiljer sig från dess domän.
   > 
    
8. För **Protokoll** och **Ursprungsport** anger du protokollen och portarna som används för att komma åt resurser i ursprunget. Du måste välja minst ett protokoll (HTTP eller HTTPS). Använd den CDN-definierade domänen (`<endpointname>.azureedge.net`) för att komma åt HTTPS-innehåll. 
   
   > [!NOTE]
   > Värdet för **Ursprungsport** påverkar bara vilken port slutpunkten använder till att hämta information från ursprunget. Själva slutpunkten är bara tillgänglig för slutklienter via HTTP- och HTTPS-standardportarna (80 och 443), oavsett värdet för **Ursprungsport**.  
   > 
   > Slutpunkter i **Azure CDN from Akamai**-profiler tillåter inte hela TCP-portintervallet för ursprungsportar. En lista över ursprungsportar som inte tillåts finns i [Azure CDN från Akamai-tillåtna ursprungsportar](https://msdn.microsoft.com/library/mt757337.aspx).  
   > 
   > När du använder CDN-innehåll via HTTPS finns följande begränsningar:
   > 
   > * Använd det SSL-certifikatet som du får via CDN. Certifikat från tredje part stöds inte.
   > * HTTPS-stöd för anpassade Azure CDN-domäner är endast tillgängligt med produkten av typen **Azure CDN from Verizon** (Standard och Premium). Det stöds inte på **Azure CDN från Akamai**-produkter. Mer information finns i [Konfigurera HTTPS på en anpassad Azure CDN-domän](cdn-custom-ssl.md).
    
9. Välj **Lägg till** för att skapa den nya slutpunkten.
   
   När slutpunkten har skapats, visas den i en lista över slutpunkter för profilen.
    
   ![CDN-slutpunkt][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Slutpunkten kan inte användas direkt eftersom det tar tid för registreringen att sprida sig. För **Azure CDN från Akamai**-profiler, slutförs spridningen vanligtvis inom en minut. För **Azure CDN från Verizon**-profiler, slutförs spridningen vanligtvis inom 90 minuter, men i vissa fall kan det ta längre tid.
    > 
    > Om du försöker använda CDN-domännamnet innan konfigurationen av slutpunkten har spritts till POP:erna kan du få svarsstatusen HTTP 404. Om det har gått flera timmar sedan du skapade slutpunkten och du fortfarande får 404-svar kan du läsa [Felsöka CDN-slutpunkter som returnerar 404-statusar](cdn-troubleshoot-endpoint.md).
    > 
    > 

## <a name="see-also"></a>Se även
* [Kontrollera cachelagringsbeteendet för begäranden med frågesträngar](cdn-query-string.md)
* [Mappa CDN-innehåll till en anpassad domän](cdn-map-content-to-custom-domain.md)
* [Läsa in tillgångar för en Azure CDN-slutpunkt i förväg](cdn-preload-endpoint.md)
* [Rensa en Azure CDN-slutpunkt](cdn-purge-endpoint.md)
* [Felsöka CDN-slutpunkter som returnerar 404-status](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
