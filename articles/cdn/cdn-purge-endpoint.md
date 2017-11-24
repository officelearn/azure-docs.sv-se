---
title: Rensa en Azure CDN-slutpunkt | Microsoft Docs
description: "Lär dig mer om att rensa allt cachelagrat innehåll från en Azure CDN-slutpunkt."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b6157ddaf320f942a704d32f066b821425596308
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="purge-an-azure-cdn-endpoint"></a>Rensa en Azure CDN-slutpunkt
## <a name="overview"></a>Översikt
Azure CDN edge noder cachelagras tillgångar tills tillgångens time to live (TTL) upphör att gälla.  När tillgångens TTL upphör att gälla, när en klient begär tillgången från kantnoden, kantnoden hämtar en ny uppdaterad kopia av tillgången som fungerar klientbegäran och store uppdatera cachen.

Bäst att kontrollera att användarna hämta alltid den senaste kopian av dina tillgångar är att version dina tillgångar för varje uppdatering och publicera dem som nya URL: er.  CDN kommer omedelbart att hämta nya tillgångar efter nästa klientbegäranden.  Ibland kanske du vill ta bort cachelagrat innehåll från alla noder i kant och tvinga att alla ska hämta nya uppdaterade tillgångar.  Detta kan bero på uppdateringar att ditt webbprogram eller för att snabbt uppdatera tillgångar som innehåller felaktig information.

> [!TIP]
> Observera att rensa endast tar bort cachelagrat innehåll på CDN edge-servrar.  Alla underordnade cacheminnen, till exempel proxyservrar och lokala browser cacheminnen kan fortfarande håller en cachelagrad kopia av filen.  Det är viktigt att komma ihåg detta när du ställer in en fil time-to-live.  Du kan tvinga en underordnad klienten begära den senaste versionen av filen genom att ge den ett unikt namn varje gång du uppdaterar den eller genom att utnyttja [fråga cachelagring av frågesträngar](cdn-query-string.md).  
> 
> 

Den här självstudiekursen vägleder dig genom att rensa tillgångar från alla noder i kanten av en slutpunkt.

## <a name="walkthrough"></a>Genomgång
1. I den [Azure Portal](https://portal.azure.com), bläddra till CDN-profilen som innehåller den slutpunkt som du vill rensa.
2. Klicka på Rensa i bladet CDN-profilen.
   
    ![CDN-profilbladet](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Rensa blad öppnas.
   
    ![CDN Rensa bladet](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Välj serviceadressen som du vill rensa i listrutan URL på bladet Rensa.
   
    ![Rensa formulär](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Du kan också öppna bladet Rensa genom att klicka på den **Rensa** knappen på bladet CDN-slutpunkten.  I så fall den **URL** fältet kommer att vara förifyllda med serviceadressen för den specifika slutpunkten.
   > 
   > 
4. Välj vilka resurser som du vill ta bort från noderna kant.  Om du vill rensa alla tillgångar, klickar du på den **Rensa alla** kryssrutan.  Annars anger du sökvägen till varje tillgång som du vill rensa i den **sökväg** textruta. Under format stöds i sökvägen.
    1. **Den enda URL Rensa**: Rensa enskilda tillgångar genom att ange den fullständiga URL, med eller utan filtillägget, t.ex.`/pictures/strasbourg.png`;`/pictures/strasbourg`
    2. **Jokertecken Rensa**: Asterisk (\*) kan användas som jokertecken. Rensa alla mappar, undermappar och filer på en slutpunkt med `/*` i sökvägen eller rensa alla undermappar och filer under en viss mapp genom att ange mappen följt av `/*`, t.ex`/pictures/*`.  Observera att rensa jokertecken inte stöds av Azure CDN från Akamai för närvarande. 
    3. **Roten domän Rensa**: Rensa roten för slutpunkten med ”/” i sökvägen.
   
   > [!TIP]
   > Sökvägar måste anges för rensning och måste vara en relativ URL som passar följande [reguljärt uttryck](https://msdn.microsoft.com/library/az24scfc.aspx). **Rensa alla** och **jokertecken Rensa** stöds inte av **Azure CDN från Akamai** för närvarande.
   > > Enstaka URL-rensning`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Frågesträng`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Jokertecken Rensa `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`. 
   > 
   > Flera **sökväg** textrutor visas när du har angett texten så att du kan skapa en lista över flera resurser.  Du kan ta bort tillgångar i listan genom att klicka på knappen med ellipstecknet (...).
   > 
5. Klicka på den **Rensa** knappen.
   
    ![Rensa knappen](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Rensa begäranden tar cirka 2-3 minuter att bearbeta med **Azure CDN från Verizon** (Standard och Premium) och cirka 7: e minut med **Azure CDN från Akamai**.  Azure CDN är begränsad till 50 samtidiga Rensa begäranden vid en given tidpunkt på nivån profil. 
> 
> 

## <a name="see-also"></a>Se även
* [Läsa in tillgångar för en Azure CDN-slutpunkt i förväg](cdn-preload-endpoint.md)
* [Azure CDN REST API-referens - rensa eller i förväg läsa in en slutpunkt](https://msdn.microsoft.com/library/mt634451.aspx)

