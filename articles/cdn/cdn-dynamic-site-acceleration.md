---
title: Dynamisk webbplatsacceleration via Azure CDN
description: Azure CDN stöder DSA-optimering (Dynamic Site Acceleration) för filer med dynamiskt innehåll.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 26559adf183a5e008d77b87654a1bd4dabebbca0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253840"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dynamisk webbplatsacceleration via Azure CDN

Med explosionen av sociala medier, elektronisk handel, och hyper-personlig webben, en snabbt ökande andel av innehållet serveras till slutanvändare genereras i realtid. Användarna förväntar sig en snabb, tillförlitlig och personlig webbupplevelse, oberoende av deras webbläsare, plats, enhet eller nätverk. Men själva innovationer som gör dessa erfarenheter så engagerande också långsam sida nedladdningar och sätta kvaliteten på konsumenternas upplevelse i fara. 

Cdn-funktionen (Standard content delivery network) inkluderar möjligheten att cachelagra filer närmare slutanvändarna för att påskynda leveransen av statiska filer. Men med dynamiska webbprogram är cachelagring av innehåll på kantplatser inte möjligt eftersom servern genererar innehållet som svar på användarbeteende. Att påskynda leveransen av sådant innehåll är mer komplext än traditionell kantcachelagring och kräver en end-to-end-lösning som finjusterar varje element längs hela datasökvägen från start till leverans. Med Azure CDN dynamic site acceleration (DSA) optimering förbättras prestanda för webbsidor med dynamiskt innehåll mätbart.

**Azure CDN från Akamai** och **Azure CDN från Verizon** erbjuder båda DSA-optimering via menyn **Optimerad för-menyn** när slutpunkt skapas. Dynamisk webbplatsacceleration från Microsoft erbjuds via [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

> [!Important]
> För **Azure CDN från Akamai-profiler** kan du ändra optimeringen av en CDN-slutpunkt när den har skapats.
>   
> För **Azure CDN från Verizon**-profiler kan du inte ändra optimeringen av en CDN-slutpunkt när den väl har skapats.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>CDN-slutpunktskonfiguration för att påskynda leveransen av dynamiska filer

Om du vill konfigurera en CDN-slutpunkt för att optimera leveransen av dynamiska filer kan du antingen använda Azure-portalen, REST-API:erna eller någon av klient-SDK:erna för att göra samma sak programmässigt. 

**Så här konfigurerar du en CDN-slutpunkt för DSA-optimering med hjälp av Azure-portalen:**

1. Välj **Slutpunkt**på **CDN-profilsidan** .

   ![Lägga till en ny CDN-slutpunkt](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Fönstret **Lägg till slutpunkt** visas.

2. Under **Optimerad för**väljer du **Dynamisk platsacceleration**.

    ![Skapa en ny CDN-slutpunkt med DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. För **Avsökningssökväg**anger du en giltig sökväg till en fil.

    Avsökningssökväg är en funktion som är specifik för DSA och en giltig sökväg krävs för att skapas. DSA använder en liten *avsökningssökvägsfil* som placeras på ursprungsservern för att optimera nätverksdirigeringskonfigurationer för CDN. För söksökfilen kan du hämta och ladda upp exempelfilen till webbplatsen eller använda en befintlig tillgång för ditt ursprung som är cirka 10 kB i storlek.

4. Ange de andra nödvändiga slutpunktsalternativen (mer information finns i [Skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) och välj sedan **Lägg till**.

   När CDN-slutpunkten har skapats tillämpas DSA-optimeringarna för alla filer som matchar vissa villkor. 


**Så här konfigurerar du en befintlig slutpunkt för DSA (endast Azure CDN från Akamai-profiler):**

1. På **CDN-profilsidan** väljer du den slutpunkt som du vill ändra.

2. Välj **Optimering**i den vänstra rutan . 

   **Optimeringssidan** visas.

3. Under **Optimerad för**väljer du **Dynamisk platsacceleration**och väljer sedan **Spara**.

> [!Note]
> DSA medför extra avgifter. Mer information finns i [priser för Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>DSA-optimering med Azure CDN

Dynamisk platsacceleration på Azure CDN snabbar upp leveransen av dynamiska tillgångar med hjälp av följande tekniker:

-   [Ruttoptimering](#route-optimization)
-   [TCP-optimeringar](#tcp-optimizations)
-   [Objektförfetch (endast Azure CDN från Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptiv bildkomprimering (endast Azure CDN från Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Ruttoptimering

Ruttoptimering är viktigt eftersom Internet är en dynamisk plats, där trafik och tillfälligt avbrott ständigt ändrar nätverkstopologin. BGP (Border Gateway Protocol) är Routningsprotokollet för Internet, men det kan finnas snabbare vägar via PoP-servrar (Point of Presence). 

Ruttoptimering väljer den mest optimala vägen till ursprunget så att en webbplats är kontinuerligt tillgänglig och dynamiskt innehåll levereras till slutanvändare via den snabbaste och mest tillförlitliga vägen som möjligt. 

Akamai-nätverket använder tekniker för att samla in realtidsdata och jämföra olika sökvägar genom olika noder i Akamai-servern, samt bgp-standardvägen över det öppna Internet för att bestämma den snabbaste vägen mellan ursprunget och CDN-kanten. Dessa tekniker undviker överbelastningspunkter på Internet och långa rutter. 

På samma sätt använder Verizon-nätverket en kombination av Anycast DNS, högkapacitetsstödbehållarbehåll och hälsokontroller för att bestämma de bästa gatewayerna för att bäst dirigera data från klienten till ursprunget.
 
Som ett resultat levereras helt dynamiskt och transaktionsinnehåll snabbare och mer tillförlitligt till slutanvändarna, även när det inte går attachebara. 

### <a name="tcp-optimizations"></a>TCP-optimeringar

TCP (Transmission Control Protocol) är standarden på internetprotokollpaketet som används för att leverera information mellan program i ett IP-nätverk.  Som standard krävs flera fram- och tillbaka-begäranden för att ställa in en TCP-anslutning, samt begränsningar för att undvika överbelastning i nätverket, vilket resulterar i ineffektivitet i stor skala. **Azure CDN från Akamai** hanterar det här problemet genom att optimera inom tre områden: 

 - [Eliminera TCP långsam start](#eliminating-tcp-slow-start)
 - [Utnyttja beständiga anslutningar](#leveraging-persistent-connections)
 - [Justering av TCP-paketparametrar](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminera TCP långsam start

TCP *långsam start* är en algoritm för TCP-protokollet som förhindrar överbelastning i nätverket genom att begränsa mängden data som skickas över nätverket. Det börjar med små överbelastning fönsterstorlekar mellan avsändare och mottagare tills det maximala uppnås eller paketförlust upptäcks.

 Både **Azure CDN från Akamai** och **Azure CDN från Verizon-profiler** eliminerar TCP långsam start med följande tre steg:

1. Hälso- och bandbreddsövervakning används för att mäta bandbredden för anslutningar mellan pop-servrar.
    
2. Mått delas mellan kant PoP-servrar så att varje server är medveten om nätverksvillkoren och serverns hälsa för de andra popsna runt dem.  
    
3. CDN-kantservrarna gör antaganden om vissa överföringsparametrar, till exempel vad den optimala fönsterstorleken ska vara när du kommunicerar med andra CDN-kantservrar i närheten. Det här steget innebär att den ursprungliga överbelastningsfönstrets storlek kan ökas om hälsotillståndet för anslutningen mellan CDN-kantservrarna kan hantera högre paketdataöverföringar.  

#### <a name="leveraging-persistent-connections"></a>Utnyttja beständiga anslutningar

Med hjälp av ett CDN ansluter färre unika datorer till din ursprungsserver direkt jämfört med användare som ansluter direkt till ditt ursprung. Azure CDN samlar också användarbegäranden tillsammans för att upprätta färre anslutningar med ursprunget.

Som tidigare nämnts krävs flera handskakningsbegäranden för att upprätta en TCP-anslutning. Beständiga anslutningar, som implementeras av `Keep-Alive` HTTP-huvudet, återanvända befintliga TCP-anslutningar för flera HTTP-begäranden för att spara tur-och-retur-tider och påskynda leveransen. 

**Azure CDN från Verizon** skickar också periodiska keep-alive-paket via TCP-anslutningen för att förhindra att en öppen anslutning stängs.

#### <a name="tuning-tcp-packet-parameters"></a>Justering av TCP-paketparametrar

**Azure CDN från Akamai** ställer in de parametrar som styr server-till-server-anslutningar och minskar mängden långdistansresor som krävs för att hämta innehåll som är inbäddat på webbplatsen med hjälp av följande tekniker:

- Öka det första överbelastningsfönstret så att fler paket kan skickas utan att vänta på en bekräftelse.
- Minska den första återsända tidsgränsen så att en förlust upptäcks och återsändning sker snabbare.
- Minska den minsta och högsta tidsgränsen för återsändning för att minska väntetiden innan du antar att paket gick förlorade i överföringen.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Objektförfetch (endast Azure CDN från Akamai)

De flesta webbplatser består av en HTML-sida, som refererar till olika andra resurser såsom bilder och skript. När en klient begär en webbsida hämtar och tolkar webbläsaren först HTML-objektet och gör sedan ytterligare begäranden till länkade resurser som krävs för att läsa in sidan helt. 

*Prefetch* är en teknik för att hämta bilder och skript inbäddade i HTML-sidan medan HTML-koden serveras till webbläsaren, och innan webbläsaren ens gör dessa objektbegäranden. 

När alternativet prefetch är aktiverat när CDN betjänar HTML-bassidan till klientens webbläsare tolkar CDN HTML-filen och gör ytterligare begäranden om länkade resurser och lagrar den i cacheminnet. När klienten gör begäranden om de länkade tillgångarna har CDN-kantservern redan de begärda objekten och kan betjäna dem omedelbart utan en tur och retur till ursprunget. Den här optimeringen gynnar både cachelagbart och icke-cachelagbart innehåll.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptiv bildkomprimering (endast Azure CDN från Akamai)

Vissa enheter, särskilt mobila enheter, upplever långsammare nätverkshastigheter då och då. I dessa scenarier är det mer fördelaktigt för användaren att ta emot mindre bilder på sin webbsida snabbare än att vänta länge på bilder med full upplösning.

Den här funktionen övervakar automatiskt nätverkskvaliteten och använder vanliga JPEG-komprimeringsmetoder när nätverkshastigheterna är långsammare för att förbättra leveranstiden.

Adaptiv bildkomprimering | Filtillägg  
--- | ---  
JPEG-komprimering | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Caching

Med DSA är cachelagring inaktiverad som standard på CDN, `Cache-Control` även `Expires` när ursprunget innehåller eller rubriker i svaret. DSA används vanligtvis för dynamiska tillgångar som inte bör cachelagras eftersom de är unika för varje klient. Cachelagring kan bryta detta beteende.

Om du har en webbplats med en blandning av statiska och dynamiska tillgångar, är det bäst att ta en hybrid strategi för att få bästa prestanda. 

För **Azure CDN Standard från Verizon** och Azure **CDN Standard från Akamai-profiler** kan du aktivera cachelagring för specifika DSA-slutpunkter med hjälp av [cachelagringsregler](cdn-caching-rules.md).

Så här kommer du åt cachelagringsregler:

1. Välj **Cachelagringsregler**under inställningar på **CDN-profilsidan.**  
    
    ![Knappen CDN-cachelagringsregler](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Sidan **Cachelagringsregler** öppnas.

2. Skapa en global eller anpassad cachelagringsregel för att aktivera cachelagring för DSA-slutpunkten. 

Endast för **Azure CDN Premium från Verizon-profiler** aktiverar du cachelagring för specifika DSA-slutpunkter med hjälp av [regelmotorn](cdn-rules-engine.md). Alla regler som skapas påverkar endast slutpunkterna i din profil som är optimerade för DSA. 

Så här kommer du åt regelmotorn:
    
1. Välj **Hantera**på **CDN-profilsidan** .  
    
    ![Knappen Hantera CDN-profil](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    CDN-hanteringsportalen öppnas.

2. Välj **ADN**på CDN-hanteringsportalen och välj sedan **Regelmotor**. 

    ![Regler motor för DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternativt kan du använda två CDN-slutpunkter: en slutpunkt som är optimerad med DSA för att leverera dynamiska tillgångar och en annan slutpunkt som är optimerad med en statisk optimeringstyp, till exempel allmän webbleverans, till leveranscachebara tillgångar. Ändra webbadresserna till webbsidan om du vill länka direkt till tillgången på cdn-slutpunkten som du tänker använda. 

Till exempel: `mydynamic.azureedge.net/index.html` är en dynamisk sida och läses in från DSA-ändpunkten.Html-sidan refererar till flera statiska resurser, till exempel JavaScript-bibliotek eller bilder `mystatic.azureedge.net/banner.jpg` `mystatic.azureedge.net/scripts.js`som läses in från den statiska CDN-slutpunkten, till exempel och . 



