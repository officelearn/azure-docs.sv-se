---
title: Dynamiska Acceleration via Azure CDN
description: "Dynamiska acceleration ingående"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: v-semcev
ms.openlocfilehash: be2719e0e02c8bc69800ef4a3e7da3c3164cb9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dynamiska Acceleration via Azure CDN

Med nedbrytning av sociala medier, elektronisk handel och web hyper personliga skapas snabbt ökade procent av det innehåll som visas för slutanvändare i realtid. Användarna förväntar sig att snabb, tillförlitlig och anpassade web-upplevelser, oberoende av deras webbläsare, plats, enhet eller nätverk. Mycket innovationer som gör att dessa erfarenheter så att också långsamma sidan hämtningar och kvaliteten på användarfunktioner medföra risker. 

Standard CDN kapaciteten inkluderar möjligheten att cachelagra filer närmare till slutanvändare påskynda överföringen av statiska filer. Men med dynamiska webbprogram cachelagring innehållet i platser är inte möjligt eftersom servern genererar innehållet som svar på användarnas beteende. Påskynda överföringen av innehållet är mer komplexa än traditionella edge cachelagring och kräver en slutpunkt till slutpunkt-lösning som finjusterar varje element längs hela sökvägen från Start till leverans. Med Azure CDN dynamiska plats Acceleration (DSA), bättre den webbsidor med dynamiskt innehåll mätbart prestandan.

Azure CDN från Akamai och Verizon erbjuder DSA optimering via den **optimerade för** menyn under skapande av slutpunkten.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>Konfigurera CDN-slutpunkten för att påskynda överföringen av dynamiska filer

Du kan konfigurera CDN-slutpunkten för att optimera leverans av dynamiska filer via Azure-portalen genom att välja den **dynamiska acceleration** alternativ den **optimerade för** egenskap under den Skapa en slutpunkt. Du kan också använda vår REST-API: er eller någon av klient-SDK: er för att göra det via programmering. 

### <a name="probe-path"></a>Avsökningen sökväg
Avsökningen sökvägen är en särskild funktion för dynamisk plats Acceleration och ett giltigt krävs för att skapa. DSA använder en liten *avsökningen sökvägen* filen placeras på originalet att optimera routning nätverkskonfigurationer för CDN. Du kan ladda ned och ladda upp vår exempelfilen till din webbplats eller använda en befintlig tillgång på din ursprung är ungefär 10 KB för avsökning sökväg i stället om tillgången finns.

> [!Note]
> DSA debiteras extra. Mer information finns i [sida med priser](https://azure.microsoft.com/pricing/details/cdn/) för mer information.

Följande skärmbilderna illustrerar processen via Azure-portalen.
 
![Lägga till en ny CDN-slutpunkt](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

*Bild 1: Lägga till en ny CDN-slutpunkt från CDN-profilen*
 
![Skapa en ny CDN-slutpunkt med DSA](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

*Bild 2: Skapa en CDN-slutpunkt med dynamiska acceleration optimering markerat*

När CDN-slutpunkten har skapats gäller DSA-optimeringar för alla filer som matchar vissa villkor. I följande avsnitt beskrivs DSA optimering i detalj.

## <a name="dsa-optimization-using-azure-cdn"></a>DSA-optimering med hjälp av Azure CDN

Dynamisk plats Acceleration på Azure CDN snabbare leverans av dynamisk tillgångar med hjälp av följande metoder:

-   Väg optimering
-   TCP-optimeringar
-   Objektet Prefetch (Akamai)
-   Komprimering av mobila avbildningen (Akamai)

### <a name="route-optimization"></a>Väg optimering

Väg optimering är viktig eftersom Internet är en dynamisk plats där trafik och tillfälligt avbrott ändras ständigt nätverkets topologi. Border Gateway Protocol (BGP) är routing protocol på Internet, men det kan finnas snabbare vägar via mellanliggande av förekomst (PoP) servrar. 

Väg optimering väljer den mest optimala sökvägen till ursprunget så att en plats är kontinuerligt tillgängliga och dynamiskt innehåll levereras till slutanvändare via den snabbaste och mest tillförlitliga vägen som möjligt. 

Nätverkets Akamai använder tekniker för att samla in data i realtid och jämför olika sökvägar via olika noder i Akamai-server och som BGP standardvägen mellan öppna Internet för att fastställa den snabbaste vägen mellan ursprung och CDN kanten. Dessa tekniker undvika Internet överbelastning punkter och lång vägar. 

På liknande sätt Verizon nätverk använder en kombination av Anycast DNS hög kapacitet stöder POP och hälsokontroller, för att fastställa bästa gateway till den bästa vägen data från klienten till ursprunget.
 
Därför helt dynamiska och transaktionell innehållet levereras snabbare och mer tillförlitligt till slutanvändare, även när det uncacheable. 

### <a name="tcp-optimizations"></a>TCP-optimeringar

Transmission Control Protocol (TCP) är standard för Internet-protokollsviten som används för att leverera information mellan program på ett IP-nätverk.  Som standard finns flera fram och tillbaka begäranden för att konfigurera en TCP-anslutning, samt gränser att undvika att nätverket congestions, vilket resulterar i ineffektiviteter i större skala. Azure CDN från Akamai behandlar det här problemet genom att optimera tre områden: 

 - Ta bort långsam start
 - Utnyttja beständiga anslutningar
 - justering av TCP-parametrar för paketet (Akamai)

#### <a name="eliminating-slow-start"></a>Ta bort långsam start

*Långsamma start* är en del av TCP-protokollet som förhindrar överbelastning på nätverket genom att begränsa mängden data som skickas över nätverket. Den startar med små överbelastning fönsterstorlek mellan avsändare och mottagare tills högsta nås eller paketförlust har identifierats.

Azure CDN från Akamai och Verizon eliminerar långsam startar i tre steg:

1.  Både Akamai och Verizons nätverket använda hälsa och övervakning av bandbredd för att mäta bandbredden för anslutningar mellan edge PoP-servrar.
2. Mätvärdena som delas mellan edge PoP-servrar så att varje server är medveten om nätverkets tillstånd och Servertillstånd POP runtom.  
3. CDN edge-servrar kan nu göra antaganden om vissa överföring parametrar, till exempel vad optimal fönsterstorlek bör vara vid kommunikation med andra CDN edge-servrar i dess närhet. Det här steget innebär inledande överbelastning fönstrets storlek kan ökas om hälsotillståndet för anslutningen mellan CDN edge-servrarna kan högre paket dataöverföringar.  

#### <a name="leveraging-persistent-connections"></a>Utnyttja beständiga anslutningar

Använder en CDN ansluta färre unika datorer till ursprungsservern direkt jämfört med användare som ansluter direkt till din ursprung. Azure CDN från Akamai och Verizon pooler också användarförfrågningar tillsammans för att skapa färre anslutningar med ursprung.

Som tidigare nämnts är utför TCP-anslutningar flera begäranden fram och tillbaka i en handskakning att upprätta en ny anslutning. Beständiga anslutningar, även kallat ”HTTP Keep-Alive”, återanvända befintliga TCP-anslutningar för flera HTTP-begäranden att spara fram och åter gånger och snabba upp överföringen. 

Nätverkets Verizon skickar också periodiska keep alive-paket via TCP-anslutning för att förhindra att en öppen anslutning stängs.

#### <a name="tuning-tcp-packet-parameters"></a>Justera parametrarna för TCP-paket

Azure CDN från Akamai också låtar de parametrar som styr server till server-anslutningar och minskar mängden lång drag förfrågningar krävs för att hämta innehåll inbäddat i platsen med hjälp av följande metoder:

1.  Öka fönstret inledande överbelastning så att flera paket kan skickas utan att vänta på en bekräftelse.
2.  Minskar den inledande sändningsförsök tidsgränsen så att förlust identifieras och återöverföring sker snabbare.
3.  Minska timeout-värdet för lägsta och högsta sändningsförsök för att minska väntetiden innan förutsatt att paket gått förlorade under överföringen.

### <a name="object-prefetch-akamai-only"></a>Objektet Prefetch (Akamai)

De flesta webbplatser består av en HTML-sida som refererar till andra resurser, till exempel bilder och skript. Normalt när en klient begär en webbsida, webbläsaren först ned Parsar HTML-objekt och gör sedan ytterligare förfrågningar till länkade resurser som krävs för att läsa in sidan fullständigt. 

*Prefetch* är en teknik för att hämta bilder och skript inbäddat i HTML-sidan medan HTML hanteras till webbläsaren och innan webbläsaren gör även dessa begäranden för objektet. 

Med den **förhämtning** alternativet igång vid den tidpunkt då CDN fungerar HTML grundläggande sidan till klientens webbläsare CDN Parsar HTML-fil och kontrollera begäranden för alla länkade resurser och lagra den i sin cache. När klienten skickar begäranden för de länkade resurserna, har de begärda objekten CDN edge-server redan och kan hantera dem direkt utan onödig kommunikation till ursprunget. Denna optimering fördelar både Cacheable ställs och icke Cacheable ställs innehåll.

### <a name="adaptive-image-compression-akamai-only"></a>Komprimering av anpassningsbar avbildningen (Akamai)

Vissa enheter, särskilt mobila som får då långsammare nätverkshastigheter. Det är bättre för användaren ta emot mindre bilder i deras webbsida snabbare i stället för att vänta så länge för full upplösning bilder i dessa scenarier.

Den här funktionen automatiskt övervakar nätverket kvalitet och använder standardmetoder för JPEG-komprimering när nätverkshastigheter är långsammare att förbättra leveranstiden.

Komprimering av anpassningsbar avbildningen | Filnamnstillägg  
--- | ---  
JPEG-komprimering | JPG, JPEG, jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Cachelagring

Med DSA, är cachelagring inaktiverat som standard på CDN, även om ursprung inkluderar cache-control/upphör att gälla rubriker i svaret. Den här standardinställningen har inaktiverats eftersom DSA används vanligtvis för dynamiska tillgångar som inte ska cachelagras eftersom de är unik för varje klient och aktivera cachelagring som standard kan dela det här beteendet.

Om du har en webbplats med en blandning av statiska och dynamiska tillgångar, är det bäst att en hybrid-metod för att få bästa möjliga prestanda. 

Om du använder ADN med Verizon Premium kan aktivera du cachelagring igen för särskilda fall regler-motorn.  

Ett alternativ är att använda två CDN-slutpunkter. En med DSA att ge dynamiska tillgångar och en annan slutpunkt med en statisk optimering typ, till exempel Internet leverans till leverans Cacheable ställs tillgångar. För att kunna göra detta alternativ kommer det att ändra din webbsida URL: er att länka direkt till tillgången på CDN-slutpunkt som du tänker använda. 

Till exempel: `mydynamic.azureedge.net/index.html` är en dynamisk sida och har lästs in från DSA-slutpunkten.  HTML-sidan refererar till flera statiska resurser, t.ex JavaScript-bibliotek och bilder som har lästs in från statisk CDN-slutpunkten som `mystatic.azureedge.net/banner.jpg` och `mystatic.azureedge.net/scripts.js`. 

Du hittar ett exempel [här](https://docs.microsoft.com/azure/cdn/cdn-cloud-service-with-cdn#controller) om hur du använder domänkontrollanter i ASP.NET-webbprogram för att hantera innehåll via en specifik CDN-URL.




