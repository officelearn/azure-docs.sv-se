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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 80e8e85f058a5cec2e3ae6a6cff5cb8a363370e1
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="getting-started-with-azure-cdn"></a>Komma igång med Azure CDN
Den här artikeln beskriver hur du aktiverar Azure CDN genom att skapa en ny profil och slutpunkt för CDN.

> [!IMPORTANT]
> En introduktion till CDN och en lista över funktioner finns i [CDN-översikt](cdn-overview.md).
> 
> 

## <a name="create-a-new-cdn-profile"></a>Skapa en ny CDN-profil
En CDN-profil är en samling CDN-slutpunkter.  Varje profil innehåller en eller flera CDN-slutpunkter.  Du kanske vill använda flera profiler för att organisera dina CDN-slutpunkter efter Internetdomän, webbapp eller andra kriterier.

> [!NOTE]
> En Azure-prenumeration har standardgränser för följande resurser:
> - Antalet CDN-profiler som kan skapas
> - Antalet slutpunkter som kan skapas i en CDN-profil 
> - Antal anpassade domäner som kan mappas till en slutpunkt
>
> Information om CDN-prenumerationsbegränsningar finns i [CDN limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits) (CDN-begränsningar).
>
> CDN-priserna tillämpas på CDN-profilnivå. Om du vill använda en kombination av olika Azure CDN-prisnivåer behöver du flera CDN-profiler.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Skapa en ny CDN-slutpunkt
**Så här skapar du en ny CDN-slutpunkt**

1. Gå till din CDN-profil på [Azure-portalen](https://portal.azure.com).  Du kanske fäste den på instrumentpanelen i föregående steg.  Om du inte hittar den klicka du på **Bläddra**, sedan på **CDN-profiler** och sedan på den profil som du vill lägga till slutpunkten till.
   
    Bladet för CDN-profilen visas.
   
    ![CDN-profil][cdn-profile-settings]
2. Klicka på knappen **Lägg till slutpunkt**.
   
    ![Knappen Lägg till slutpunkt][cdn-new-endpoint-button]
   
    Bladet **Lägg till slutpunkt** visas.
   
    ![Bladet Lägg till slutpunkt][cdn-add-endpoint]
3. Ange ett **namn** för den här CDN-slutpunkten.  Det här namnet används för att komma åt dina cachelagrade resurser i domänen `<endpointname>.azureedge.net`.
4. I listrutan **Typ av ursprung** väljer du typ av ursprung.  Välj **Storage** för ett Azure Storage-konto, **Cloud Services** för ett Azure Cloud Services-konto, **Web Apps** för ett Azure Web Apps-konto eller **Anpassat ursprung** för ett annat ursprung i form av en offentligt tillgänglig webbserver (som finns i Azure eller någon annanstans).
   
    ![Ursprungstyp för CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
5. I listrutan **Ursprungets värdnamn** väljer du eller skriver din ursprungliga domän.  Listrutan visar en lista över alla tillgängliga ursprung av den typ som du angav i steg 4.  Om du väljer *anpassat ursprung* som din **ursprungstyp** så anger du domänen för ditt anpassade ursprung.
6. I textrutan **Sökväg till ursprung** anger du sökvägen till de resurser som du vill cachelagra eller lämnar fältet tomt om du vill tillåta cachelagring av alla resurser i domänen som du angav i steg 5.
7. I **Ursprungsvärdadress** anger du värdhuvudet som du vill att CDN ska skicka med varje begäran, eller så lämnar du standardvärdet.
   
   > [!WARNING]
   > Vissa typer av ursprung, till exempel Azure Storage och Web Apps, kräver att värdhuvudet matchar ursprungets domän. Lämna standardvärdet såvida du inte har ett ursprung som kräver ett värdhuvud som skiljer sig från dess domän.
   > 
   > 
8. För **Protokoll** och **Ursprungsport** anger du protokollen och portarna som används för att komma åt resurser i ursprunget. Du måste välja minst ett protokoll (HTTP eller HTTPS). Använd den CDN-definierade domänen (`<endpointname>.azureedge.net`) för att komma åt HTTPS-innehåll. 
   
   > [!NOTE]
   > **Ursprungsporten** påverkar endast vilken port som slutpunkten använder för att hämta information från ursprunget.  Själva slutpunkten är bara tillgänglig för slutklienter via HTTP- och HTTPS-standardportarna (80 och 443), oavsett **ursprungsporten**.  
   > 
   > Slutpunkter av typen **Azure CDN från Akamai** tillåter inte det fullständiga TCP-portintervallet för ursprung.  En lista över ursprungsportar som inte tillåts finns i [Azure CDN från Akamai-tillåtna ursprungsportar](https://msdn.microsoft.com/library/mt757337.aspx).  
   > 
   > Följande begränsningar gäller för åtkomst till CDN-innehåll via HTTPS:
   > 
   > * Du måste använda SSL-certifikatet som tillhandahålls av CDN. Certifikat från tredje part stöds inte.
   > * HTTPS-stöd för anpassade Azure CDN-domäner är endast tillgängligt med produkten av typen **Azure CDN from Verizon** (Standard och Premium). Det stöds inte på **Azure CDN från Akamai**-produkter. Mer information finns i [Konfigurera HTTPS på en anpassad Azure CDN-domän](cdn-custom-ssl.md).
  
9. Klicka på knappen **Lägg till** för att skapa en ny slutpunkt.
   
   När slutpunkten har skapats, visas den i en lista över slutpunkter för profilen.
    
   ![CDN-slutpunkt][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Slutpunkten är inte direkt tillgänglig för användning eftersom det tar tid för registreringen att sprida sig.  För <b>Azure CDN från Akamai</b>-profiler, slutförs spridningen vanligtvis inom en minut. För <b>Azure CDN från Verizon</b>-profiler, slutförs spridningen vanligtvis inom 90 minuter, men i vissa fall kan det ta längre tid.
    > 
    > Användare som försöker använda CDN-domännamnet innan konfigurationen av slutpunkten har spritts till POP:erna kan få HTTP 404-svarskoder.  Om det har gått flera timmar sedan du skapade slutpunkten och du fortfarande får 404-svar, läs [felsök CDN-slutpunkter som returnerar 404-statusar](cdn-troubleshoot-endpoint.md).
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
