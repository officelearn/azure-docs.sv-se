---
title: Rensa en Azure CDN-slutpunkt | Microsoft Docs
description: Lär dig hur du tar bort allt cachelagrat innehåll från en Azure CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: magattus
ms.openlocfilehash: 76e7817be81a97c8d1a0b9ca2fea8378c3c733e1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916487"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Rensa en Azure CDN-slutpunkt
## <a name="overview"></a>Översikt
Azure CDN-gränsnoder cachelagrar tillgångar tills tillgångens time to live (TTL) upphör att gälla.  När tillgångens TTL upphör att gälla när en klient begär tillgången från edge-nod, gränsnoden hämtar en ny uppdaterad kopia av tillgången som fungerar klientbegäran och store uppdatera cachen.

Rekommenderade metoder för att se till att användarna alltid hämta den senaste kopian av dina tillgångar är att version tillgångarna för varje uppdatering och publicera dem som nya URL: er.  CDN kommer omedelbart att hämta nya tillgångar för nästa klientbegäranden.  Ibland kanske du vill rensa cachelagrat innehåll från alla kantnoder och tvinga dem alla att hämta nya uppdaterade tillgångar.  Detta kan bero på uppdateringar i ditt webbprogram eller för att snabbt uppdatera tillgångar som innehåller felaktig information.

> [!TIP]
> Observera att rensa endast tar bort det cachelagrade innehållet på CDN edge-servrar.  Alla underordnade cacheminnen, till exempel proxyservrar och webbläsarens lokala cacheminnen kan fortfarande håller en cachelagrad kopia av filen.  Det är viktigt att komma ihåg detta när du ställer in en fil time to live.  Du kan tvinga en underordnad klient för att begära den senaste versionen av filen genom att ge den ett unikt namn varje gång du uppdaterar den, eller genom att utnyttja [cachelagring av frågesträngar](cdn-query-string.md).  
> 
> 

Den här självstudien vägleder dig genom att rensa tillgångar från alla edge-noder i en slutpunkt.

## <a name="walkthrough"></a>Genomgång
1. I den [Azure-portalen](https://portal.azure.com), bläddra till CDN-profil som innehåller den slutpunkt som du vill ta bort.
2. Klicka på knappen Rensa från CDN-profilbladet.
   
    ![CDN-profilbladet](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Rensa-bladet öppnas.
   
    ![Bladet för CDN-rensning](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Välj tjänstadressen som du vill ta bort URL-listrutan på bladet Rensa.
   
    ![Rensa formulär](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Du kan också öppna bladet Rensa genom att klicka på den **Rensa** knappen på CDN-slutpunktsbladet.  I så fall den **URL** fält kommer att vara förifyllda med tjänstadressen för den specifika slutpunkten.
   > 
   > 
4. Välj vilka resurser som du vill ta bort från edge-noder.  Om du vill ta bort alla tillgångar, klickar du på den **tar bort alla** kryssrutan.  I annat fall skriver sökvägen för varje tillgång som du vill ta bort i den **sökväg** textrutan. Nedan format som stöds i sökvägen.
    1. **Den enda URL Rensa**: Ta bort enskilda tillgångar genom att ange en fullständig URL med eller utan filtillägget, t.ex.`/pictures/strasbourg.png`; `/pictures/strasbourg`
    2. **Jokerteckensrensning**: Asterisk (\*) kan användas som jokertecken. Rensa alla mappar, undermappar och filer på en slutpunkt med `/*` i sökvägen eller rensa alla undermappar och filer under en viss mapp genom att ange mappen följt av `/*`, t.ex`/pictures/*`.  Observera att jokerteckensrensning inte stöds för närvarande av Azure CDN från Akamai. 
    3. **Rot domän Rensa**: Rensa roten för slutpunkten med ”/” i sökvägen.
   
   > [!TIP]
   > Sökvägar måste anges för rensning och måste vara en relativ URL som passar följande [reguljärt uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Rensa alla** och **jokerteckensrensning** inte stöds av **Azure CDN från Akamai** för närvarande.
   > > Enkel URL-rensning `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Frågesträng `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Jokerteckensrensning `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Mer **sökväg** textrutor visas när du har angett texten så att du kan skapa en lista över flera tillgångar.  Du kan ta bort tillgångar i listan genom att klicka på knappen med tre punkter (...).
   > 
5. Klicka på den **Rensa** knappen.
   
    ![Rensa knappen](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Rensa begäranden tar cirka 2 – 3 minuter att bearbeta med **Azure CDN från Verizon** (standard och premium), och cirka 7 minuter med **Azure CDN från Akamai**.  Azure CDN har en gräns på 50 samtidiga Rensa förfrågningar vid en given tidpunkt på profilnivå. 
> 
> 

## <a name="see-also"></a>Se också
* [Läsa in tillgångar för en Azure CDN-slutpunkt i förväg](cdn-preload-endpoint.md)
* [Azure CDN REST API-referens - rensa eller läsa in en slutpunkt i förväg](/rest/api/cdn/endpoints)

