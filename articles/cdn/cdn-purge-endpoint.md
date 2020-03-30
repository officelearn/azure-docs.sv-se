---
title: Rensa en Azure CDN-slutpunkt | Microsoft-dokument
description: Lär dig hur du rensar allt cachelagrat innehåll från en Azure CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: magattus
ms.openlocfilehash: 1bfbc1b730811e1111a08a957db3a747f90fb587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546204"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Rensa en Azure CDN-slutpunkt
## <a name="overview"></a>Översikt
Azure CDN-edge-noder cachelagrar tillgångar tills tillgångens time-to-live (TTL) upphör att gälla.  När tillgångens TTL har upphört att gälla, när en klient begär tillgången från kantnoden, hämtar kantnoden en ny uppdaterad kopia av tillgången för att betjäna klientbegäran och lagra uppdatering av cacheminnet.

Den bästa metoden för att se till att användarna alltid får den senaste kopian av dina tillgångar är att version dina tillgångar för varje uppdatering och publicera dem som nya webbadresser.  CDN hämtar omedelbart de nya tillgångarna för nästa klientbegäranden.  Ibland kanske du vill rensa cachelagrat innehåll från alla kantnoder och tvinga dem alla att hämta nya uppdaterade tillgångar.  Detta kan bero på uppdateringar av webbprogrammet eller på att resurser som innehåller felaktig information snabbt uppdateras.

> [!TIP]
> Observera att rensning endast rensar det cachelagrade innehållet på CDN-kantservrarna.  Alla nedströmscachen, till exempel proxyservrar och lokala webbläsarcacheminnen, kan fortfarande innehålla en cachelagd kopia av filen.  Det är viktigt att komma ihåg detta när du anger en fils tid att leva.  Du kan tvinga en underordnad klient att begära den senaste versionen av filen genom att ge den ett unikt namn varje gång du uppdaterar den, eller genom att dra nytta av [frågesträngcachening](cdn-query-string.md).  
> 
> 

Den här självstudien går igenom rensning av tillgångar från alla kantnoder i en slutpunkt.

## <a name="walkthrough"></a>Genomgång
1. I [Azure Portal](https://portal.azure.com)bläddrar du till CDN-profilen som innehåller den slutpunkt som du vill rensa.
2. Klicka på rensningsknappen från CDN-profilbladet.
   
    ![Cdn-profilblad](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Rensningsbladet öppnas.
   
    ![CDN-utrensningsblad](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. På rensningsbladet väljer du den serviceadress som du vill rensa från url-listrutan.
   
    ![Rensa formulär](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Du kan också komma åt rensningsbladet genom att klicka på **knappen Rensa** på CDN-slutpunktsbladet.  I så fall fylls **URL-fältet** i förinfylld med serviceadressen för den specifika slutpunkten.
   > 
   > 
4. Välj vilka tillgångar du vill rensa från kantnoderna.  Om du vill rensa alla tillgångar klickar du på kryssrutan **Rensa alla.**  Annars skriver du sökvägen för varje tillgång som du vill rensa i textrutan **Sökväg.** Nedanstående format stöds i sökvägen.
    1. **Enkel URL-rensning:** Rensa enskilda tillgångar genom att ange den fullständiga webbadressen,`/pictures/strasbourg.png`med eller utan filtillägget, t.ex.`/pictures/strasbourg`
    2. **Jokerteckenrensning:** Asterisk (\*) kan användas som jokertecken. Rensa alla mappar, undermappar och filer under `/*` en slutpunkt med i sökvägen eller rensa alla undermappar `/*`och filer under`/pictures/*`en viss mapp genom att ange mappen följt av , t.ex.  Observera att rensa jokertecken stöds inte av Azure CDN från Akamai för närvarande. 
    3. **Rotdomänrensning:** Rensa roten för slutpunkten med "/" i sökvägen.
   
   > [!TIP]
   > Sökvägar måste anges för rensning och måste vara en relativ URL som passar följande [reguljära uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Rensa alla** och **Jokerteckenrensning** som inte stöds av **Azure CDN från Akamai** för närvarande.
   > > Rensa en enda webbadress`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Frågesträngen`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Rensa ut `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`från jokertecken . 
   > 
   > Fler **sökvägstextrutor** visas när du har angett text så att du kan skapa en lista med flera resurser.  Du kan ta bort tillgångar från listan genom att klicka på ellipsknappen (...).
   > 
5. Klicka på knappen **Rensa.**
   
    ![Knappen Rensa](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Det tar cirka 10 minuter att rensa begäranden att bearbeta med **Azure CDN från Microsoft**, cirka 2 minuter med Azure **CDN från Verizon** (standard och premium) och cirka 10 sekunder med Azure **CDN från Akamai**.  Azure CDN har en gräns på 50 samtidiga rensningsbegäranden vid en given tidpunkt på profilnivå. 
> 
> 

## <a name="see-also"></a>Se även
* [Läsa in tillgångar för en Azure CDN-slutpunkt i förväg](cdn-preload-endpoint.md)
* [Azure CDN REST API-referens - Rensa eller förinläsninga en slutpunkt](/rest/api/cdn/endpoints)

