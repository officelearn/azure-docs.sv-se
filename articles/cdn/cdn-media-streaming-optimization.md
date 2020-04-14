---
title: Optimering av direktuppspelning av media med Azure CDN
description: Optimera strömmande mediefiler för smidig leverans
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
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 2931dffaaab2d06b2c06f03770a66d78d6466787
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260487"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Optimering av direktuppspelning av media med Azure CDN 
 
Användningen av HD-video ökar på Internet, vilket skapar svårigheter för effektiv leverans av stora filer. Kunderna förväntar sig smidig uppspelning av video on demand eller live video tillgångar på en mängd olika nätverk och kunder över hela världen. En snabb och effektiv leveransmekanism för mediestreamingfiler är avgörande för att säkerställa en smidig och trevlig konsumentupplevelse.  

Live streaming media är särskilt svårt att leverera på grund av de stora storlekarna och antalet samtidiga tittare. Långa förseningar gör att användarna lämnar. Eftersom livestreamar inte kan cachelagras i förväg och stora fördröjningar inte är acceptabla för tittarna, måste videofragment levereras i tid. 

Förfrågemönstren ger också några nya utmaningar. När en populär livestream eller en ny serie släpps för video på begäran kan tusentals till miljontals tittare begära strömmen samtidigt. I det här fallet är konsolidering av smarta begäranden avgörande för att inte överbelasta ursprungsservrarna när tillgångarna inte cachelagras ännu.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Optimering av media för Azure CDN från Microsoft

**Azure CDN Standard från** Microsoft-slutpunkter levererar direktuppspelade medietillgångar med hjälp av den allmänna optimeringstypen för webbleverans. 

Optimering av media för **Azure CDN Standard från Microsoft** gäller för direktuppspelade media eller video på begäran som använder enskilda mediefragment för leverans. Den här processen skiljer sig från en enda stor tillgång som överförs via progressiv nedladdning eller genom att använda byte-range-begäranden. Information om den typen av medieleverans finns i [Stor optimering av filhämtning med Azure CDN](cdn-large-file-optimization.md).

De allmänna medieleverans- eller video-on-demand-optimeringstyperna för medieleverans använder Azure Content Delivery Network (CDN) med backend-optimering för att leverera medietillgångar snabbare. De använder också konfigurationer för medietillgångar baserat på bästa praxis som har lärts in över tid.

### <a name="partial-cache-sharing"></a>Partiell cachedelning
Partiell cachedelning gör att CDN kan betjäna delvis cachelagrat innehåll till nya begäranden. Om till exempel den första begäran till CDN resulterar i en cachemiss, skickas begäran till ursprunget. Även om det här ofullständiga innehållet läses in i CDN-cachen kan andra begäranden till CDN börja hämta dessa data. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimering av media för Azure CDN från Verizon

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** slutpunkter leverera strömmande medietillgångar direkt med hjälp av den allmänna webbleverans optimeringstypen. Några funktioner på CDN hjälper direkt till att leverera medietillgångar som standard.

### <a name="partial-cache-sharing"></a>Partiell cachedelning

Partiell cachedelning gör att CDN kan betjäna delvis cachelagrat innehåll till nya begäranden. Om till exempel den första begäran till CDN resulterar i en cachemiss, skickas begäran till ursprunget. Även om det här ofullständiga innehållet läses in i CDN-cachen kan andra begäranden till CDN börja hämta dessa data. 

### <a name="cache-fill-wait-time"></a>Väntetid för cachefyllning

 Funktionen för cachefyllningsberedskap tvingar kantservern att hålla alla efterföljande begäranden om samma resurs tills HTTP-svarshuvuden anländer från ursprungsservern. Om HTTP-svarshuvuden från ursprunget anländer innan timern upphör att gälla visas alla begäranden som spärrades av från den växande cachen. Samtidigt fylls cachen av data från ursprunget. Som standard är cachefyllningsvändtiden inställd på 3 000 millisekunder. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimering av media för Azure CDN från Akamai
 
**Azure CDN Standard från Akamai** erbjuder en funktion som levererar direktuppspelade medietillgångar effektivt till användare över hela världen i stor skala. Funktionen minskar fördröjningar eftersom det minskar belastningen på ursprungsservrarna. Den här funktionen är tillgänglig med standardnivån för Akamai-priser. 

Optimering av media för **Azure CDN Standard från Akamai** är effektiv för direktuppspelade media som använder enskilda mediefragment för leverans. Den här processen skiljer sig från en enda stor tillgång som överförs via progressiv nedladdning eller genom att använda byte-range-begäranden. Information om den typen av medieleverans finns i [Stor filoptimering](cdn-large-file-optimization.md).

De allmänna medieleveranstyperna eller optimeringstyperna för medieleverans på begäran använder ett CDN med backend-optimering för att leverera medietillgångar snabbare. De använder också konfigurationer för medietillgångar baserat på bästa praxis som har lärts in över tid.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurera en Akamai CDN-slutpunkt för att optimera direktuppspelning av media
 
Du kan konfigurera cdn-slutpunkten (Content Delivery Network) för att optimera leveransen för stora filer via Azure-portalen. Du kan också använda REST-API:erna eller någon av klient-SDK:erna för att göra detta. Följande steg visar processen via Azure-portalen för en **Azure CDN Standard från Akamai-profil:**

1. Om du vill lägga till en ny slutpunkt väljer du **Slutpunkt**på en Akamai **CDN-profilsida** .
  
    ![Ny slutpunkt](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. I **listrutan Optimerad för** listruta väljer du **Video on demand media streaming** för video-on-demand-tillgångar. Om du gör en kombination av direktuppspelning av live och video på begäran väljer du **Allmän mediestreaming**.

    ![Streaming markerat](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
När du har skapat slutpunkten tillämpas optimeringen för alla filer som matchar vissa villkor. I följande avsnitt beskrivs den här processen. 

### <a name="caching"></a>Caching

Om **Azure CDN Standard från Akamai** upptäcker att tillgången är ett streamingmanifest eller fragment, använder den olika cachelagringsförfallider från allmän webbleverans. (Se hela listan i följande tabell.) Som alltid, cache-kontroll eller upphör rubriker som skickas från ursprunget respekteras. Om tillgången inte är en medietillgång cachelagras den med hjälp av utgångsdatum för allmän webbleverans.

Den korta negativa cachelagringstiden är användbar för ursprungsladdning när många användare begär ett fragment som inte finns ännu. Ett exempel är en livestream där paketen inte är tillgängliga från det andra. Det längre cachelagringsintervallet hjälper också till att avlasta begäranden från ursprunget eftersom videoinnehåll vanligtvis inte ändras.
 

|   | Allmän webbleverans | Allmän medieströmning | Video-on-demand media streaming  
--- | --- | --- | ---
Caching: Positivt <br> HTTP 200, 203, 300, <br> 301, 302 och 410 | 7 dagar |365 dagar | 365 dagar   
Caching: Negativ <br> HTTP 204, 305, 404, <br> och 405 | Inget | 1 sekund | 1 sekund
 
### <a name="deal-with-origin-failure"></a>Hantera ursprungsfel  

Allmän medieleverans och video-on-demand media leverans har också ursprung timeout och en återförsök logg baserat på bästa praxis för typiska begäran mönster. Eftersom allmän medieleverans till exempel är för direkt- och video-on-demand-medieleverans, använder den en kortare tidsgränsen för anslutning på grund av den tidskänsliga karaktären hos livestreaming.

När en anslutning timeout försöker CDN igen ett antal gånger innan den skickar ett "504 - Gateway Timeout"-fel till klienten. 

När en fil matchar listan över filtyp och storleksvillkor används beteendet för direktuppspelning av media. Annars används allmän webbleverans.
   
### <a name="conditions-for-media-streaming-optimization"></a>Villkor för optimering av direktuppspelning av media 

I följande tabell visas de kriterier som ska uppfyllas för optimering av direktuppspelning av media: 
 
Strömmande typer som stöds | Filnamnstillägg  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, nyckel, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Url-struktur för Seg-Frag <br> (matchande regex: ^(/.*)Seq(\d+)-Frag(\d+)
Dash | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Jämn strömning | /manifest/, /QualityLevels/Fragment/
  
 
