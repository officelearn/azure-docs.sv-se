---
title: Dynamiska acceleration via Azure CDN
description: Azure CDN stöder dynamiska acceleration (DSA) optimering för filer med dynamiskt innehåll.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: rli; v-deasim
ms.openlocfilehash: 66032a68634be5b52bbfa544d0a3bcd3d1090652
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359993"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dynamiska acceleration via Azure CDN

Med nedbrytning av sociala medier, elektronisk handel och web hyper personliga skapas snabbt ökade procent av det innehåll som visas för slutanvändare i realtid. Användarna förväntar sig en snabb, tillförlitlig och anpassade webbupplevelse, oberoende av deras webbläsare, plats, enhet eller nätverk. Mycket innovationer som gör att dessa erfarenheter så att också långsamma sidan hämtningar och kvaliteten på användarfunktioner medföra risker. 

Standard content delivery network (CDN) kapaciteten inkluderar möjligheten att cachelagra filer närmare till slutanvändare påskynda överföringen av statiska filer. Men med dynamiska webbprogram cachelagring innehållet i platser är inte möjligt eftersom servern genererar innehållet som svar på användarnas beteende. Påskynda överföringen av innehållet är mer komplexa än traditionella edge cachelagring och kräver en slutpunkt till slutpunkt-lösning som finjusterar varje element längs hela sökvägen från Start till leverans. Med Azure CDN dynamiska acceleration (DSA) optimering bättre prestanda för web ålder med dynamiskt innehåll mätbart.

**Azure CDN från Akamai** och **Azure CDN från Verizon** båda har DSA optimering via den **optimerade för** menyn under skapande av slutpunkten.

> [!Important]
> För **Azure CDN från Akamai** profiler, du kan ändra optimering av CDN-slutpunkten efter att den har skapats.
>   
> För **Azure CDN från Verizon** profiler kan du inte ändra optimering av CDN-slutpunkten när den har skapats.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>CDN-slutpunktskonfiguration att påskynda överföringen av dynamiska filer

Om du vill konfigurera en CDN-slutpunkt för att optimera leverans av dynamiska filer kan du antingen använda Azure-portalen, REST-API: er eller något av klient-SDK: er för att göra det via programmering. 

**Konfigurera en CDN-slutpunkt för DSA-optimering med hjälp av Azure portal:**

1. I den **CDN-profilen** väljer **Endpoint**.

   ![Lägg till en ny CDN-slutpunkt](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Fönstret **Lägg till slutpunkt** visas.

2. Under **optimerade för**väljer **dynamiska acceleration**.

    ![Skapa en ny CDN-slutpunkt med DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. För **avsökningen sökvägen**, ange en giltig sökväg till en fil.

    Avsökningen sökvägen är en funktion som är specifika för DSA och en giltig sökväg krävs för att skapa. DSA använder en liten *avsökningen sökvägen* filen placeras på den ursprungliga servern för att optimera routning nätverkskonfigurationer för CDN. För filen avsökningen sökväg du ladda ned och ladda upp exempelfilen till din webbplats eller använda en befintlig tillgång på din ursprung är cirka 10 KB.

4. Ange andra nödvändiga endpoint-alternativ (Mer information finns i [skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) och välj **Lägg till**.

   När du har skapat CDN-slutpunkten gäller DSA-optimeringar för alla filer som matchar vissa villkor. 


**Så här konfigurerar du en befintlig slutpunkt för DSA (Azure CDN från Akamai-profiler):**

1. I den **CDN-profilen** markerar du den slutpunkt som du vill ändra.

2. I den vänstra rutan, Välj **optimering**. 

   Den **optimering** visas.

3. Under **optimerade för**väljer **dynamiska acceleration**och välj **spara**.

> [!Note]
> DSA debiteras extra. Mer information finns i [innehållsleveransnätverk priser](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>DSA-optimering med hjälp av Azure CDN

Dynamisk plats Acceleration på Azure CDN snabbare leverans av dynamisk tillgångar med hjälp av följande metoder:

-   [Väg optimering](#route-optimization)
-   [TCP-optimeringar](#tcp-optimizations)
-   [Objektet prefetch (Azure CDN från Akamai endast)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Komprimering av anpassningsbar avbildningen (Azure CDN från Akamai endast)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Väg optimering

Väg optimering är viktig eftersom Internet är en dynamisk plats där trafik och tillfälligt avbrott ändras ständigt nätverkets topologi. Border Gateway Protocol (BGP) är routing protocol på Internet, men det kan finnas snabbare vägar via mellanliggande av förekomst (PoP) servrar. 

Väg optimering väljer den mest optimala sökvägen till ursprunget så att en plats är kontinuerligt tillgängliga och dynamiskt innehåll levereras till slutanvändare via den snabbaste och mest tillförlitliga vägen som möjligt. 

Nätverkets Akamai använder tekniker för att samla in data i realtid och jämför olika sökvägar via olika noder i Akamai-server och som BGP standardvägen mellan öppna Internet för att fastställa den snabbaste vägen mellan ursprung och CDN kanten. Dessa tekniker undvika Internet överbelastning punkter och lång vägar. 

På liknande sätt Verizon nätverk använder en kombination av Anycast DNS hög kapacitet stöder POP och hälsokontroller, för att fastställa bästa gateway till den bästa vägen data från klienten till ursprunget.
 
Därför helt dynamiska och transaktionell innehållet levereras snabbare och mer tillförlitligt till slutanvändare, även när det uncacheable. 

### <a name="tcp-optimizations"></a>TCP-optimeringar

Transmission Control Protocol (TCP) är standard för Internet-protokollsviten som används för att leverera information mellan program på ett IP-nätverk.  Som standard är flera fram och tillbaka begäranden måste ställa in en TCP-anslutning, samt gränser för att undvika att nätverket congestions, vilket resulterar i ineffektiviteter i större skala. **Azure CDN från Akamai** hanterar det här problemet genom att optimera tre områden: 

 - [Ta bort TCP långsam start](#eliminating-tcp-slow-start)
 - [Utnyttja beständiga anslutningar](#leveraging-persistent-connections)
 - [Justera parametrarna för TCP-paket](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Ta bort TCP långsam start

TCP *långsamma start* är en algoritm för TCP-protokollet som förhindrar överbelastning på nätverket genom att begränsa mängden data som skickas över nätverket. Den startar med små överbelastning fönsterstorlek mellan avsändare och mottagare tills högsta nås eller paketförlust har identifierats.

 Båda **Azure CDN från Akamai** och **Azure CDN från Verizon** profiler eliminera TCP långsam starta med följande tre steg:

1. Hälsa och övervakning av bandbredden används för att mäta bandbredd för anslutningar mellan edge PoP-servrar.
    
2. Mått delas mellan edge PoP-servrar så att varje server är medveten om nätverkets tillstånd och Servertillstånd POP runtom.  
    
3. CDN edge servrar gör antaganden om vissa överföring parametrar, till exempel vad optimal fönsterstorlek bör vara vid kommunikation med andra CDN edge-servrar i dess närhet. Det här steget innebär inledande överbelastning fönstrets storlek kan ökas om hälsotillståndet för anslutningen mellan CDN edge-servrarna kan högre paket dataöverföringar.  

#### <a name="leveraging-persistent-connections"></a>Utnyttja beständiga anslutningar

Använder en CDN ansluta färre unika datorer till ursprungsservern direkt jämfört med användare som ansluter direkt till din ursprung. Azure CDN pooler också användarförfrågningar tillsammans för att skapa färre anslutningar med ursprung.

Som tidigare nämnts krävs flera handskakning begäranden för att upprätta en TCP-anslutning. Beständiga anslutningar som implementeras med den `Keep-Alive` HTTP-huvud, återanvända befintliga TCP-anslutningar för flera HTTP-begäranden att spara fram och åter gånger och snabba upp överföringen. 

**Azure CDN från Verizon** skickar också periodiska keep alive-paket via TCP-anslutning för att förhindra att en öppen anslutning stängs.

#### <a name="tuning-tcp-packet-parameters"></a>Justera parametrarna för TCP-paket

**Azure CDN från Akamai** justerar parametrarna som styr server till server-anslutningar och minskar mängden långa avstånd sändningar som krävs för att hämta innehåll som är inbäddad i platsen med hjälp av följande metoder:

- Öka fönstret inledande överbelastning så att flera paket kan skickas utan att vänta på en bekräftelse.
- Minskar den inledande sändningsförsök tidsgränsen så att förlust identifieras och återöverföring sker snabbare.
- Minska timeout-värdet för lägsta och högsta sändningsförsök för att minska väntetiden innan förutsatt att paket gått förlorade under överföringen.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objektet prefetch (Azure CDN från Akamai endast)

De flesta webbplatser består av en HTML-sida som refererar till andra resurser, till exempel bilder och skript. Normalt när en klient begär en webbsida, webbläsaren först ned Parsar HTML-objekt och gör sedan ytterligare förfrågningar till länkade resurser som krävs för att läsa in sidan fullständigt. 

*Prefetch* är en teknik för att hämta bilder och skript inbäddat i HTML-sidan medan HTML hanteras till webbläsaren och innan webbläsaren gör även dessa begäranden för objektet. 

Med alternativet prefetch aktiverat vid den tidpunkt då CDN fungerar den grundläggande HTML-sidan till klientens webbläsare CDN Parsar HTML-fil och begär ytterligare för länkade resurser, och lagra den i sin cache. När klienten skickar begäranden för de länkade resurserna, har de begärda objekten CDN edge-server redan och kan hantera dem direkt utan onödig kommunikation till ursprunget. Denna optimering fördelar både Cacheable ställs och icke Cacheable ställs innehåll.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Komprimering av anpassningsbar avbildningen (Azure CDN från Akamai endast)

Vissa enheter, särskilt mobila som får då långsammare nätverkshastigheter. Det är bättre för användaren ta emot mindre bilder i deras webbsida snabbare i stället för att vänta så länge för full upplösning bilder i dessa scenarier.

Den här funktionen automatiskt övervakar nätverket kvalitet och använder standardmetoder för JPEG-komprimering när nätverkshastigheter är långsammare att förbättra leveranstiden.

Komprimering av anpassningsbar avbildningen | Filnamnstillägg  
--- | ---  
JPEG-komprimering | JPG, JPEG, jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Cachelagring

Med DSA, cachelagring är inaktiverat som standard på CDN, även om ursprung innehåller `Cache-Control` eller `Expires` huvuden i svaret. DSA används vanligtvis för dynamiska tillgångar som inte ska cachelagras eftersom de är unik för varje klient. Cachelagring kan dela det här beteendet.

Om du har en webbplats med en blandning av statiska och dynamiska tillgångar, är det bäst att en hybrid-metod för att få bästa möjliga prestanda. 

För **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler, kan du aktivera cachelagring för specifika DSA-slutpunkter med hjälp av [cachelagring regler](cdn-caching-rules.md).

Öppna cachelagring regler:

1. Från den **CDN-profilen** sidan under inställningar, väljer **cachelagring regler**.  
    
    ![Knappen CDN-cachelagringsregler](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Den **cachelagring regler** öppnas.

2. Skapa en global eller anpassade cachelagring regel för att aktivera cachelagring för DSA-slutpunkten. 

För **Azure CDN Premium från Verizon** profiler kan du aktivera cachelagring för specifika DSA-slutpunkter med hjälp av den [regelmotor](cdn-rules-engine.md). Alla regler som har skapats påverkas endast slutpunkter som är optimerade för DSA för din profil. 

Öppna regelmotor:
    
1. Från den **CDN-profilen** väljer **hantera**.  
    
    ![CDN-profilen hantera knappen](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    CDN-hanteringsportalen öppnas.

2. CDN-hanteringsportalen, Välj **ADN**och välj **regelmotor**. 

    ![Regelmotor för DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Du kan också använda två CDN-slutpunkter: en slutpunkt som har optimerats med DSA att leverera dynamiskt tillgångar och en annan slutpunkt optimerats med en statisk optimering typ, till exempel allmänna web leverans till leverans Cacheable ställs tillgångar. Ändra din webbsida URL: er att länka direkt till tillgången på CDN-slutpunkt som du tänker använda. 

Till exempel: `mydynamic.azureedge.net/index.html` är en dynamisk sida och har lästs in från DSA-slutpunkten.  HTML-sidan refererar till flera statiska resurser, t.ex JavaScript-bibliotek och bilder som har lästs in från statisk CDN-slutpunkten som `mystatic.azureedge.net/banner.jpg` och `mystatic.azureedge.net/scripts.js`. 



