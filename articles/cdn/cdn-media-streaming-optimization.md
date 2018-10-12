---
title: Mediedirektuppspelning optimering med Azure CDN
description: Optimera strömning mediefiler för smidig leverans
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
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9802296170f07bb8599058e230798f647e900d4d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093705"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Mediedirektuppspelning optimering med Azure CDN 
 
Användning av högupplöst video ökar på internet, vilket skapar problem för effektiv leverans av stora filer. Kunderna förväntar sig smidig uppspelning av video på begäran eller live videotillgångar på en rad olika nätverk och klienter över hela världen. En snabb och effektiv leveransmekanism för direktuppspelning filer är viktigt att säkerställa en smidig och roligare användarfunktioner.  

Live strömmande media är särskilt svårt att leverera på grund av stora storlekar och antalet samtidiga användare. Långa fördröjningar orsaka användare att lämna. Eftersom direktsända strömmar inte kan cachelagras förbereds i förväg och stor fördröjning inte är acceptabel för visning, att video fragment leverera i tid. 

Begäran-mönster för strömning ger också några nya utmaningar. När en populära direktsänd dataström eller en ny serie släpps för video på begäran, begära tusentals till miljontals tittare dataströmmen på samma gång. I det här fallet är smart begäran konsolidering viktigt att inte överbelastar ursprungsservrar om resurserna som inte cachelagras ännu.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Mediedirektuppspelning optimeringar för Azure CDN från Microsoft

**Azure CDN Standard från Microsoft** slutpunkter leverera strömmande medietillgångar direkt med hjälp av allmän optimering Leveranstyp. 

Mediedirektuppspelning optimering för **Azure CDN Standard från Microsoft** är effektiva för live eller video på begäran strömmande media som använder enskilda media fragment för leverans. Den här processen skiljer sig från en enda stor tillgång som överförs via progressiv nedladdning eller med hjälp av begäranden om byte-intervall. Information om det formatet för leverans av media finns i [optimering för nedladdning av stora filer med Azure CDN](cdn-large-file-optimization.md).

Allmänna leverans- eller video på begäran media leverans optimering medietyper använda Azure Content Delivery Network (CDN) med backend-optimeringar för att leverera medieinnehåll snabbare. De kan också använda konfigurationer för medietillgångar baserat på bästa metoderna lärt dig över tid.

### <a name="partial-cache-sharing"></a>Partiell cache delning
Partiell cache innebär att CDN för att hantera delvis cachelagrat innehåll till nya begäranden. Om den första begäran till CDN resulterar i en cachemiss, till exempel skickas begäran till ursprunget. Även om det här ofullständig innehållet läses in i CDN-cachen, kan övriga förfrågningar till CDN börja hämta dessa data. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Mediedirektuppspelning optimeringar för Azure CDN från Verizon

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** slutpunkter leverera strömmande medietillgångar direkt med hjälp av allmän optimering Leveranstyp. Några funktioner i CDN kan direkt leverans av medieinnehåll som standard.

### <a name="partial-cache-sharing"></a>Partiell cache delning

Partiell cache innebär att CDN för att hantera delvis cachelagrat innehåll till nya begäranden. Om den första begäran till CDN resulterar i en cachemiss, till exempel skickas begäran till ursprunget. Även om det här ofullständig innehållet läses in i CDN-cachen, kan övriga förfrågningar till CDN börja hämta dessa data. 

### <a name="cache-fill-wait-time"></a>Väntetid för cache fyllning

 Funktionen cache fyllning vänta tid tvingar edge-servern för alla efterföljande begäranden för samma resurs tills HTTP-svarshuvuden tas emot från den ursprungliga servern. Om HTTP-svarshuvuden från ursprunget tas emot innan timern upphör att gälla, hanteras alla förfrågningar som har spärra från växande cachen. På samma gång fylls cachen med data från ursprunget. Väntetid för cache fyllning är som standard till 3 000 millisekunder. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Mediedirektuppspelning optimeringar för Azure CDN från Akamai
 
**Azure CDN Standard från Akamai** erbjuder en funktion som levererar media tillgångar effektivt till användare över hela världen i stor skala. Funktionen minskar svarstiderna eftersom det minskar belastningen på ursprungsservrar. Den här funktionen är tillgänglig med standard Akamai prisnivå. 

Mediedirektuppspelning optimering för **Azure CDN Standard från Akamai** är effektiva för live eller video på begäran strömmande media som använder enskilda media fragment för leverans. Den här processen skiljer sig från en enda stor tillgång som överförs via progressiv nedladdning eller med hjälp av begäranden om byte-intervall. Information om det formatet för leverans av media finns i [optimering av stora filer](cdn-large-file-optimization.md).

Allmänna leverans- eller video på begäran media leverans optimering medietyper använda ett CDN med backend-optimeringar för att leverera medieinnehåll snabbare. De kan också använda konfigurationer för medietillgångar baserat på bästa metoderna lärt dig över tid.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Konfigurera en Akamai CDN-slutpunkt för att optimera mediedirektuppspelning
 
Du kan konfigurera din slutpunkt med content delivery network (CDN) för att optimera leverans av stora filer via Azure portal. Du kan också använda REST-API: er eller någon av klient-SDK: er för att göra detta. Följande steg visar hur via Azure portal för en **Azure CDN Standard från Akamai** profil:

1. Lägga till en ny slutpunkt på en Akamai **CDN-profil** väljer **Endpoint**.
  
    ![Ny slutpunkt](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. I den **optimerade för** listrutan, väljer **Video på begäran** för video på begäran tillgångar. Om du gör en kombination av live och strömning av video på begäran, Välj **allmän mediedirektuppspelning**.

    ![Strömning valt](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
När du har skapat slutpunkten gäller optimering för alla filer som matchar vissa kriterier. I följande avsnitt beskrivs den här processen. 

### <a name="caching"></a>Cachelagring

Om **Azure CDN Standard från Akamai** känner av att tillgången är ett manifest för direktuppspelning eller fragment den använder olika tidpunkter för cachelagring upphör att gälla från allmän webbleverans. (Se en fullständig lista i tabellen nedan.) Som alltid respekteras cache-control eller Expires-huvuden som skickas från ursprunget. Om tillgången inte är en tillgång med media, cachelagrar den med hjälp av förfallodatum tiderna för allmän webbleverans.

Kort negativ cachelagring tiden är användbart för ursprung avlastning när många användare begär ett fragment som inte finns ännu. Ett exempel är en direktsänd dataström där paketen inte är tillgängliga från ursprunget den andra. Längre cachelagring intervallet hjälper också till att förflytta begäranden från ursprunget eftersom videoinnehåll inte ändras normalt.
 

|   | Allmän webbleverans | Allmän mediedirektuppspelning | Direktuppspelning av video på begäran  
--- | --- | --- | ---
Cachelagring: positivt <br> HTTP 200, 203, 300, <br> 301, 302 och 410 | 7 dagar |365 dagar | 365 dagar   
Cachelagring: negativt <br> HTTP 204, 305, 404, <br> och 405 | Ingen | 1 sekund | 1 sekund
 
### <a name="deal-with-origin-failure"></a>Hantera ursprung fel  

Allmän och video på begäran media leverans har också ursprung tidsgränser och en återförsök logg baserat på bästa praxis för begäran om vanliga mönster. Till exempel eftersom allmän leveransen är för live och leverans av video på begäran media, använder en kortare tidsgräns för anslutning på grund av den tid natur liveuppspelning.

När en anslutning tidsgränsen återförsök CDN flera gånger innan den skickar ett ”504 - Gatewaytimeout”-fel till klienten. 

När en fil matchar typ och storlek villkor fillistan, använder CDN beteendet för direktuppspelning av media. I annat fall används allmän webbleverans.
   
### <a name="conditions-for-media-streaming-optimization"></a>Villkor för optimering av mediedirektuppspelning 

I följande tabell visas en uppsättning kriterier uppfyllas för optimering av mediedirektuppspelning: 
 
Strömmande typer som stöds | Filnamnstillägg  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, nyckel, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Godtogs Fragm URL-struktur <br> (matchar regex: ^(/.*)Seq(\d+)-Frag(\d+)
STRECK | MPD, streck, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Smooth streaming | / manifest//QualityLevels/fragment /
  
 
