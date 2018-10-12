---
title: Acceleration av dynamisk webbplats via Azure CDN
description: Azure CDN stöder dynamic site acceleration (DSA) optimering för filer med dynamiskt innehåll.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: magattus
ms.openlocfilehash: 4fa681e800197ea241ba1c6cf2180ba04b6e565b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092600"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Acceleration av dynamisk webbplats via Azure CDN

Med expanderingen av sociala medier, elektronisk handel och web hyper personligt anpassade genereras en snabbt ökande procentandel av det innehåll som hanteras av slutanvändare i realtid. Användare förväntar sig en snabb, pålitlig och anpassade webbupplevelse, oberoende av deras webbläsare, plats, enhet eller nätverk. Dock mycket innovationer som gör dessa upplevelser som engagerar sig så också långsam sidan nedladdningar och medföra risker för kvaliteten på hur lösenordsåterställningen går till. 

Standard content delivery network (CDN)-funktionen ger möjlighet att cachefiler närmare för slutanvändare att snabba upp leveransen av statiska filer. Men med dynamiska webbprogram cachelagring av innehållet på edge-platser är inte möjligt eftersom servern genererar innehållet som svar på användarnas beteende. Snabbare leverans av innehåll är mer komplexa än traditionella edge cachelagring och kräver en lösning för slutpunkt till slutpunkt som finjusterar varje element längs hela vägen från Start till leverans. Med Azure CDN dynamic site acceleration (DSA) optimering bättre prestanda för webbsidor med dynamiskt innehåll mätbart.

**Azure CDN från Akamai** och **Azure CDN från Verizon** båda erbjuder chanserna att DSA den **optimerade för** menyn när slutpunkten skapades.

> [!Important]
> För **Azure CDN från Akamai** profiler, som du har behörighet att ändra optimering av en CDN-slutpunkt när den har skapats.
>   
> För **Azure CDN från Verizon**-profiler kan du inte ändra optimeringen av en CDN-slutpunkt när den väl har skapats.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfigurationen av CDN-slutpunkten att leveransen av de dynamiska filer

Om du vill konfigurera en CDN-slutpunkt för att optimera leveransen av dynamiskt filer, kan du antingen använda Azure-portalen, REST-API: er eller någon av klient-SDK: er för att göra samma sak programmässigt. 

**Konfigurera en CDN-slutpunkt för DSA optimering med hjälp av Azure portal:**

1. I den **CDN-profil** väljer **Endpoint**.

   ![Lägg till en ny CDN-slutpunkt](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Fönstret **Lägg till slutpunkt** visas.

2. Under **optimerade för**väljer **acceleration av dynamisk webbplats**.

    ![Skapa en ny CDN-slutpunkt med DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. För **avsökningssökväg**, ange en giltig sökväg till en fil.

    Sökvägen för avsökning är en funktion som är specifika för DSA och det krävs en giltig sökväg för att skapa. DSA använder en liten *avsökningssökväg* fil placeras på den ursprungliga servern för att optimera routning nätverkskonfigurationer för CDN. Rubrik för filen avsökningen sökväg du ladda ned och ladda upp exempelfilen till din webbplats, eller använda en befintlig tillgång på din ursprungsserver som är ungefär 10 KB i storlek.

4. Ange de andra alternativen för krävs slutpunkten (Mer information finns i [skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) och välj sedan **Lägg till**.

   När CDN-slutpunkten har skapats, gäller DSA-Optimeringarna för alla filer som matchar vissa kriterier. 


**Så här konfigurerar du en befintlig slutpunkt för DSA (Azure CDN från Akamai-profiler):**

1. I den **CDN-profil** väljer du den slutpunkt som du vill ändra.

2. I den vänstra rutan, Välj **optimering**. 

   Den **optimering** visas.

3. Under **optimerade för**väljer **acceleration av dynamisk webbplats**och välj sedan **spara**.

> [!Note]
> DSA medför extra avgifter. Mer information finns i [prissättningen för CDN](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>DSA optimering med hjälp av Azure CDN

Acceleration av dynamisk webbplats på Azure CDN påskyndar leveransen av dynamiskt tillgångar med hjälp av följande metoder:

-   [Routningsoptimering](#route-optimization)
-   [TCP optimeringar](#tcp-optimizations)
-   [Objektet prefetch (Azure CDN från Akamai endast)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptiv bildkomprimering (Azure CDN från Akamai endast)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Routningsoptimering

Väg optimering är viktigt eftersom Internet är en dynamisk plats, där trafik och tillfälligt avbrott ständigt förändras nätverkets topologi. Border Gateway Protocol (BGP) är routningsprotokollet för Internet, men det kan finnas snabbare vägar via mellanliggande Point of Presence (PoP)-servrar. 

Väg optimering väljer den mest optimala sökvägen till ursprunget så att en plats är kontinuerligt tillgängliga och dynamiskt innehåll skickas till slutanvändare via den snabbaste och mest pålitliga vägen som möjligt. 

Akamai-nätverk använder tekniker för att samla in data i realtid och jämför olika vägar via olika noder i Akamai-server, samt BGP standardvägen mellan öppna Internet för att fastställa den snabbaste vägen mellan ursprung och CDN-gränsen. Dessa metoder undvika Internet överbelastning poäng och lång vägar. 

På samma sätt Verizon nätverk använder en kombination av Anycast DNS, hög kapacitet stöder POP och hälsokontroller, för att fastställa bästa gateway till den bästa vägen data från klienten till ursprunget.
 
Därför helt dynamisk och transaktionell innehållet levereras snabbare och mer tillförlitligt till slutanvändare, även när den är uncacheable. 

### <a name="tcp-optimizations"></a>TCP optimeringar

Transmission Control Protocol (TCP) är standard för Internet-protokollsviten som används för att leverera information mellan program på ett IP-nätverk.  Som standard krävs fram och tillbaka flera begäranden för att ställa in en TCP-anslutning, samt gränser för att undvika att nätverket congestions vilket leda till ineffektivitet i stor skala. **Azure CDN från Akamai** hanterar det här problemet genom att optimera tre områden: 

 - [Ta bort TCP långsam start](#eliminating-tcp-slow-start)
 - [Att använda beständiga anslutningar](#leveraging-persistent-connections)
 - [Justering av TCP-parametrar för paket](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Ta bort TCP långsam start

TCP *långsam start* är en algoritm för TCP-protokollet som förhindrar överbelastning på nätverket genom att begränsa mängden data som skickas över nätverket. Den startar med små överbelastning fönsterstorlek mellan sändare och mottagare tills högsta nås eller paketförlust har identifierats.

 Båda **Azure CDN från Akamai** och **Azure CDN från Verizon** profiler eliminera TCP långsam börjar med följande tre steg:

1. Hälsa och övervakning av bandbredden används för att mäta bandbredden för anslutningar mellan edge PoP-servrar.
    
2. Mått som delas mellan edge PoP-servrar så att varje server är medveten om nätverkets tillstånd och serverhälsa POP runtom.  
    
3. CDN edgeservrar göra antaganden om vissa överföring parametrar, till exempel vad optimal fönsterstorlek bör vara vid kommunikation med andra CDN edge-servrar i dess närhet. Det här steget innebär att inledande överbelastning fönstrets storlek kan ökas om hälsotillståndet för anslutningen mellan CDN edge-servrar kan högre paket dataöverföringar.  

#### <a name="leveraging-persistent-connections"></a>Att använda beständiga anslutningar

Använda CDN ansluta färre unika datorer till ursprungsservern direkt jämfört med användare som ansluter direkt till ditt ursprung. Azure CDN pooler också användarbegäranden tillsammans för att skapa färre anslutningar med ursprunget.

Som tidigare nämnts krävs flera handskakning begäranden för att upprätta en TCP-anslutning. Beständiga anslutningar som implementeras med den `Keep-Alive` HTTP-huvud, återanvändning befintliga TCP-anslutningar för flera HTTP-begäranden att spara fram och åter gånger och snabbare leverans. 

**Azure CDN från Verizon** skickar också periodiska keep-alive-paket via TCP-anslutning för att förhindra att en öppen anslutning stängs.

#### <a name="tuning-tcp-packet-parameters"></a>Justering av TCP-parametrar för paket

**Azure CDN från Akamai** adaptivt parametrarna som styr server till server-anslutningar och minskar mängden långsiktigt sändningar som krävs för att hämta innehåll som är inbäddad i platsen med hjälp av följande metoder:

- Öka fönstret inledande överbelastning så att flera paket kan skickas utan att behöva vänta en bekräftelse.
- Minskar den inledande sändningsförsök tidsgränsen så att förlust identifieras, och återöverföring inträffar snabbare.
- Minska timeout-värdet för lägsta och högsta sändningsförsök för att minska väntetiden innan förutsatt att paket gått förlorade under överföringen.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objektet prefetch (Azure CDN från Akamai endast)

De flesta webbplatser består av en HTML-sida som refererar till andra resurser, till exempel bilder och skript. Vanligtvis när en klient begär en webbsida, webbläsaren först hämtar och Parsar HTML-objekt och sedan gör ytterligare begäranden för länkade resurser som krävs för att läsa in sidan fullständigt. 

*Prefetch* är en teknik för att hämta bilder och skript inbäddad i HTML-sidan medan HTML betjänas till webbläsaren och innan webbläsaren gör även dessa objekt-begäranden. 

Med alternativet prefetch är aktiverat vid tidpunkten när CDN har den grundläggande HTML-sidan till klientens webbläsare CDN Parsar HTML-fil och begär ytterligare för länkade resurser, och lagra den i sin cache. När klienten skickar begäranden för de länkade tillgångarna, har de begärda objekten redan i CDN edge-servern och kan hantera dem direkt utan en tur och RETUR till ursprunget. Denna optimering fördelar både komma och icke webbtillämpningar innehåll.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptiv bildkomprimering (Azure CDN från Akamai endast)

Vissa enheter, särskilt mobila som uppleva långsammare nätverkshastigheter från tid till annan. Det är bättre för användaren ta emot mindre bilder i sina webbsidan snabbare i stället för att vänta så länge innan för full upplösning avbildningar i dessa scenarier.

Den här funktionen automatiskt övervakar nätverk kvalitet och använder standardmetoder för JPEG-komprimering när nätverkshastigheter är långsammare att förbättra leveranstid.

Adaptiv bildkomprimering | Filnamnstillägg  
--- | ---  
JPEG-komprimering | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Cachelagring

Med DSA, cachelagring är inaktiverat som standard på CDN-nätverket även om ursprunget innehåller `Cache-Control` eller `Expires` rubriker i svaret. DSA används vanligtvis för dynamisk tillgångar som inte ska cachelagras eftersom de är unika för varje klient. Cachelagring kan dela det här beteendet.

Om du har en webbplats med en blandning av statisk och dynamisk tillgångar, rekommenderas du att ta en hybridmetod för att få bästa möjliga prestanda. 

För **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler, kan du aktivera cachelagring för specifika DSA-slutpunkter med hjälp av [cachelagringsregler](cdn-caching-rules.md).

Öppna cachelagringsregler:

1. Från den **CDN-profil** sida under inställningar, Välj **Cachelagringsregler**.  
    
    ![Knappen CDN-cachelagringsregler](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Den **Cachelagringsregler** öppnas.

2. Skapa en global eller anpassade cachelagringsregeln aktivera cachelagring för DSA-slutpunkten. 

För **Azure CDN Premium från Verizon** profiler kan du aktivera cachelagring för specifika DSA-slutpunkter med hjälp av den [regelmotor](cdn-rules-engine.md). Alla regler som har skapats påverkas endast slutpunkter som är optimerade för DSA för din profil. 

Öppna regelmotorn:
    
1. Från den **CDN-profil** väljer **hantera**.  
    
    ![CDN-profil hantera knappen](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    CDN-hanteringsportalen öppnas.

2. CDN-hanteringsportalen, Välj **ADN**och välj sedan **regelmotor**. 

    ![Regelmotor för DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Du kan också använda två CDN-slutpunkter: en slutpunkt som är optimerad med DSA att leverera dynamiskt tillgångar och en annan slutpunkt som är optimerad med en statisk optimeringstyp, till exempel allmän webbleverans till leverans komma tillgångar. Ändra din webbsida URL: er att länka direkt till tillgången på CDN-slutpunkten som du planerar att använda. 

Till exempel: `mydynamic.azureedge.net/index.html` är en dynamisk sida och har lästs in från DSA-slutpunkten.  Html-sidan refererar till flera statiska resurser, till exempel JavaScript-bibliotek eller avbildningar som läses från statisk CDN-slutpunkten som `mystatic.azureedge.net/banner.jpg` och `mystatic.azureedge.net/scripts.js`. 



