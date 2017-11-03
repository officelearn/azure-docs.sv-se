---
title: "Direktuppspelning optimering via Azure innehållsleveransnätverk"
description: "Optimera strömmande media-filer för smooth leverans"
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
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 02cd0fe30a2a14f42a16ed12f714d496bbb23b36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="media-streaming-optimization-via-the-azure-content-delivery-network"></a>Direktuppspelning optimering via Azure innehållsleveransnätverk 
 
Användning av video med hög definition ökar på Internet, vilket skapar svårigheter för effektiv överföringen av stora filer. Kunder räknar jämn uppspelning av video på begäran eller live video tillgångar på olika nätverk och klienter över hela världen. Snabba och effektiva leveransmekanismen för direktuppspelning filer är viktigt att säkerställa en smidig och roligare användarfunktioner.  

Live strömmande medier är särskilt svårt att leverera på grund av stora storlek och antalet samtidiga användare. Långa fördröjningar medföra användare lämnar. Eftersom levande dataströmmar inte kan cachelagras i förväg och stora svarstiderna inte går att behöriga levereras video fragment inom rimlig tid. 

Begäran mönster direktuppspelning av ger också några nya utmaningar. När en direktsänd dataström med populära eller en nya serie släpps video på begäran, kan tusentalsavgränsare till miljontals användare begära dataströmmen på samma gång. I det här fallet är smart begäran konsolidering viktigt att inte överbelasta ursprung servrar om tillgångarna inte cachelagras ännu.
 
Azure Content Delivery Network från Akamai erbjuder en funktion som levererar strömmande media tillgångar effektivt till användare världen i större skala. Funktionen minskar svarstiderna eftersom minskar belastningen på servrarna ursprung. Den här funktionen är tillgänglig med Standard Akamai prisnivån. 

Azure Content Delivery Network från Verizon levererar strömmande medier direkt i allmänna web optimering Leveranstyp.
 
## <a name="configure-an-endpoint-to-optimize-media-streaming-in-the-azure-content-delivery-network-from-akamai"></a>Konfigurera en slutpunkt för att optimera direktuppspelning i Azure Content Delivery Network från Akamai
 
Du kan konfigurera din content delivery network (CDN) slutpunkt för att optimera leverans för stora filer via Azure portal. Du kan också använda vår REST-API: er eller någon av klient-SDK: er för att göra detta. Följande steg visar processen via Azure portal:

1. Lägga till en ny slutpunkt på den **CDN-profilen** väljer **Endpoint**.
  
    ![Ny slutpunkt](./media/cdn-media-streaming-optimization/01_Adding.png)

2. I den **optimerade för** listrutan, Välj **Video på begäran direktuppspelning** för video-on-demand tillgångar. Om du gör en kombination av levande och video-on-demand strömning Välj **allmänna direktuppspelning**.

    ![Strömning markerat](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
När du har skapat slutpunkten gäller optimering för alla filer som matchar vissa villkor. I följande avsnitt beskrivs den här processen. 
 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-akamai"></a>Direktuppspelning optimeringar för Azure Content Delivery Network från Akamai
 
Media strömmande optimering från Akamai börjar gälla för live eller video-on-demand strömmande medier som använder enskilda media fragment för leverans. Den här processen skiljer sig från en enda stor tillgång överförs via progressiv hämtning eller genom att använda byteintervall begäranden. Information om formatet för leverans av media finns [optimering för stora filer](cdn-large-file-optimization.md).


Allmänna leverans eller video-on-demand media leverans optimering medietyper använder en CDN med backend-optimeringar för att leverera media tillgångar snabbare. De kan också använda konfigurationer för media tillgångar baserat på bästa praxis lärt dig över tid.

### <a name="caching"></a>Cachelagring

Om Azure Content Delivery Network från Akamai upptäcker att tillgången är en strömmande manifest eller fragment, använder olika tidpunkter för cachelagring upphör att gälla från Internet leverans. (Se en fullständig lista i följande tabell.) Som alltid funktion cache-control eller Expires-huvuden som skickas från ursprunget. Om tillgången inte är en tillgång med media, cachelagrar den med hjälp av förfallodatum gånger allmänna webben.

Kort negativ cachelagring tiden är användbart för ursprung avlastning när många användare begär ett fragment som ännu inte finns. Ett exempel är en direktsänd dataström där paketen inte är tillgängliga från ursprunget den andra. Längre cachelagring intervallet hjälper också till att förflytta begäranden från ursprunget eftersom videoinnehåll vanligtvis inte ändras.
 

|   | Allmän web leverans | Allmän direktuppspelning | Video-on-demand-direktuppspelning  
--- | --- | --- | ---
Cachelagring: positivt <br> HTTP 200, 203, 300, <br> 301, 302 och 410 | 7 dagar |365 dagar | 365 dagar   
Cachelagring: negativt <br> HTTP 204, 305, 404, <br> och 405 | Ingen | 1 sekund | 1 sekund
 
### <a name="deal-with-origin-failure"></a>Hantera ursprung fel  

Allmän media och leverans av video-on-demand media också ha ursprung timeout och ett försök logga baserat på bästa praxis för mönster av vanliga begäranden. Till exempel eftersom allmänna media levereras för live och leverans av video-on-demand media, använder en kortare timeout på grund av tidskritiska hur direktuppspelad.

När en anslutning timeout återförsök CDN flera gånger innan den skickar en ”504 - Gateway-Timeout”-fel till klienten. 

När en fil matchar typ och storlek villkor fillistan, använder CDN beteendet för direktuppspelning av media. I annat fall används allmänt web leverans.
   
### <a name="conditions-for-media-streaming-optimization"></a>Villkor för direktuppspelning optimering 

I följande tabell visas en uppsättning villkor är uppfyllda för direktuppspelning optimering: 
 
Strömmande typer som stöds | Filnamnstillägg  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, nyckel, ts, aac
Adobe hårddiskar | f4m, f4x, drmmeta, bootstrap, f4f,<br>Godtogs Fragm URL-struktur <br> (matchar regex: ^(/.*)Seq(\d+)-Frag(\d+)
STRECK | MPD, streck, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Smooth streaming | manifest-, fragment/QualityLevels / /
  

 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-verizon"></a>Direktuppspelning optimeringar för Azure Content Delivery Network från Verizon

Azure Content Delivery Network från Verizon levererar strömmande media tillgångar direkt med hjälp av allmänna web optimering Leveranstyp. Några funktioner på CDN att direkt leverera media tillgångar som standard.

### <a name="partial-cache-sharing"></a>Partiell cache delning

Partiell cache delar kan CDN att hantera delvis cachelagrat innehåll till nya begäranden. Om den första begäranden till CDN resulterar i en cache-miss, till exempel skickas begäran till ursprunget. Även om den här ofullständigt innehåll läses in i CDN-cachen, kan övriga förfrågningar till CDN börja hämta dessa data. 

### <a name="cache-fill-wait-time"></a>Väntetiden för cache fill

 Funktionen cache fill vänta tid tvingar edge-server för att rymma alla efterföljande förfrågningar för samma resurs förrän HTTP-svarshuvuden tas emot från den ursprungliga servern. Om HTTP-svarshuvuden från ursprunget anländer innan timern upphör att gälla, visas alla begäranden som har spärra ut ur cacheminnet växande. Cachen är full av data från ursprunget på samma gång. Väntetiden för cache fill är som standard till 3 000 millisekunder. 

