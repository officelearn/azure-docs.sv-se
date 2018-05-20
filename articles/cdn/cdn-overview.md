---
title: Vad är CDN (Content Delivery Network)? - Azure | Microsoft Docs
description: Lär dig vad Azure CDN är och hur du använder det för att leverera innehåll med hög bandbredd.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/09/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 2f760c1b47da7273827ce2a980cdc9b4db943904
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="what-is-a-content-delivery-network-on-azure"></a>Vad är CDN på Azure?
Ett nätverk för innehållsleverans (CDN, Content Delivery Network) är ett distribuerat nätverk av servrar som effektivt kan leverera webbinnehåll till användare. CDN-nät cachelagrar innehåll på edge-servrar på POP-platser (Point-of-Presence) som är nära slutanvändarna för att minimera fördröjningen. 

Med CDN får utvecklare en global lösning för snabb innehållsleverans med hög bandbredd till användarna eftersom innehållet cachelagras på fysiska noder på strategiska platser runt om i världen. Azure CDN möjliggör också snabbare dynamiskt innehåll, som inte kan cachelagras, genom att utnyttja olika nätverksoptimeringar med CDN POP-platser. Till exempel routningsoptimering för att kringgå BGP (Border Gateway Protocol).

Här är några exempel på fördelarna med att tillhandahålla webbplatstillgångar med Azure CDN:

* Bättre prestanda och förbättrad upplevelse för slutanvändarna, särskilt när de använder program där flera turer krävs för att läsa in innehållet.
* Skalning för effektiv hantering av plötsliga belastningstoppar, t.ex. i början av en produktlansering.
* Genom att distribuera användarförfrågningar och hämta innehåll direkt från edge-servrar skickas mindre trafik till ursprungsservern.

En lista över aktuella CDN-nodplatser finns i [POP-platser för Azure CDN](cdn-pop-locations.md).

## <a name="how-it-works"></a>Hur det fungerar
![Översikt över CDN](./media/cdn-overview/cdn-overview.png)

1. En användare (Alice) begär en fil (även kallad tillgång eller resurs) med hjälp av en URL med ett särskilt domännamn, t.ex. _&lt;slutpunktsnamn&gt;_.azureedge.net. Det här namnet kan vara slutpunktens värdnamn eller en anpassad domän. DNS omdirigerar begäran till den bästa tillgängliga POP-platsen, som vanligtvis är den POP-plats som är geografiskt närmast användaren.
    
2. Om filen inte finns i cachelagringen i någon edge-server på POP-platsen begär POP filen från ursprungsservern. Ursprungsservern kan vara en Azure-webbapp, en Azure-molntjänst, ett Azure Storage-konto eller en offentligt tillgänglig webbserver.
   
3. Ursprungsservern returnerar filen till en edge-server på POP.
    
4. Edge-servern cachelagrar filen på POP och returnerar den till användaren som ursprungligen begärde den (Alice). Filen förblir cachelagrad på edge-servern på POP tills TTL-värdet som angetts av dess HTTP-huvuden går ut. Om ursprungsservern inte angav någon TTL är standardvärdet sju dagar.
    
5. Ytterligare användare kan sedan begära samma fil med hjälp av samma URL so Alice använde, och kan också dirigeras till samma POP.
    
6. Om filens TTL inte har gått ut returnerar POP edge-servern filen direkt från cachen. Den här processen resulterar i en snabbare och mer responsiv användarupplevelse.

## <a name="requirements"></a>Krav
Om du vill använda Azure CDN måste du äga åtminstone en Azure-prenumeration. Du måste också skapa minst en CDN-profil (d.v.s. en samling CDN-slutpunkter). Varje CDN-slutpunkt representerar en specifik konfiguration av leveransbeteende och åtkomst för innehåll. Du kan organisera dina CDN-slutpunkter efter internetdomän, webbapp eller något annat kriterium genom att använda flera profiler. Eftersom [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/) tillämpas på CDN-profilnivå måste du skapa flera CDN-profiler om du vill använda en blandning av prisnivåer. Information om Azure CDN-faktureringsstruktur finns i [Förstå Azure CDN-fakturering](cdn-billing.md).

### <a name="limitations"></a>Begränsningar
Varje Azure-prenumeration har standardgränser för följande resurser:
 - Antalet CDN-profiler som kan skapas.
 - Antalet slutpunkter som kan skapas i en CDN-profil. 
 - Antal anpassade domäner som kan mappas till en slutpunkt.

Mer information om CDN-prenumerationsbegränsningar finns i [CDN limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits) (CDN-begränsningar).
    
## <a name="azure-cdn-features"></a>Funktioner i Azure CDN
Azure CDN erbjuder följande huvudfunktioner:

- [Acceleration av dynamisk webbplats](cdn-dynamic-site-acceleration.md)
- [CDN-cachelagringsregler](cdn-caching-rules.md)
- [HTTPS-stöd för egna domäner](cdn-custom-ssl.md)
- [Azure-diagnostikloggar](cdn-azure-diagnostic-logs.md)
- [Filkomprimering](cdn-improve-performance.md)
- [Geo-filtrering](cdn-restrict-access-by-country.md)

En fullständig lista över vilka funktioner som varje Azure CDN-produkt stöder finns i avsnittet [Jämföra Azure CDN-produktfunktioner](cdn-features.md).

## <a name="next-steps"></a>Nästa steg
- Om du vill komma igång med CDN kan du läsa avsnittet om hur du [skapar en Azure CDN-profil och CDN-slutpunkt](cdn-create-new-endpoint.md).
- Hantera dina CDN-slutpunkter via [Microsoft Azure Portal](https://portal.azure.com) eller med [PowerShell](cdn-manage-powershell.md).
- Läs mer om hur man automatiserar Azure CDN med [.NET](cdn-app-dev-net.md) eller [Node.js](cdn-app-dev-node.md).
- Titta på [Azure CDN-videor](https://azure.microsoft.com/resources/videos/index/?services=cdn&sort=newest) om du vill se exempel på hur Azure CDN fungerar i praktiken.
- Information om de senaste funktionerna i Azure CDN finns i [Azure CDN-blogg](https://azure.microsoft.com/blog/tag/azure-cdn/).

