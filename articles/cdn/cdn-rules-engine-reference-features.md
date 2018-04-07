---
title: Azure CDN regler motorn funktioner | Microsoft Docs
description: I referensdokumentationen för Azure CDN regler motorn matchar villkoren och funktioner.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 748cecbdf4c59469c9a56da03631dd04a819043b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cdn-rules-engine-features"></a>Azure CDN regler motorn funktioner
Den här artikeln innehåller detaljerade beskrivningar av tillgängliga funktioner för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Den tredje delen av en regel är en funktion. En funktion som definierar typ av åtgärd som tillämpas på den typ av begäran som identifieras av en uppsättning villkor för matchning.

## <a name="access-features"></a>Access-funktioner

Dessa funktioner är utformade för att styra åtkomsten till innehåll.

Namn | Syfte
-----|--------
[Neka åtkomst (403)](#deny-access-403) | Anger om alla begäranden avvisas med ett 403 förbjuden svar.
[Token Auth](#token-auth) | Anger om tokenbaserad autentisering används på en begäran.
[Token Auth DOS-kod](#token-auth-denial-code) | Anger typ av svar som returneras till en användare när en begäran nekades på grund av Token-baserad autentisering.
[Token Auth Ignorera skiftläge för URL](#token-auth-ignore-url-case) | Anger om URL: en jämförelser av tokenbaserad autentisering är skiftlägeskänsliga.
[Tokenparameter Auth](#token-auth-parameter) | Anger om tokenbaserad autentisering frågesträngparametern ska ändras.


## <a name="caching-features"></a>Cachelagring av funktioner

Dessa funktioner är utformade för att anpassa när och hur innehållet cachelagras.

Namn | Syfte
-----|--------
[Parametrar för bandbredd](#bandwidth-parameters) | Anger om bandbredd bandbreddsbegränsning parametrarna (till exempel ec_rate och ec_prebuf) är aktiva.
[Begränsning av bandbredd](#bandwidth-throttling) | Begränsar bandbredd för svar som tillhandahålls av punkt-för-förekomst (POP).
[Kringgå Cache](#bypass-cache) | Anger om begäran bör kringgå cachelagring.
[Cache-Control sidhuvud behandling](#cache-control-header-treatment) | Styr generering av `Cache-Control` huvuden av POP när externa maximal ålder funktionen är aktiv.
[Cachenyckel frågesträng](#cache-key-query-string) | Avgör om cache-nyckeln innehåller eller utesluter frågan string-parametrar som är associerade med en begäran.
[Omarbetning av cache-nyckel](#cache-key-rewrite) | Skriver om cache-nyckeln som associeras med en begäran.
[Slutföra Cache Fill](#complete-cache-fill) | Anger vad som händer när en begäran resulterar i en partiell cache-miss på en POP.
[Komprimera filtyper](#compress-file-types) | Definierar filformat för filer som är komprimerade på servern.
[Internt Max-åldern som standard](#default-internal-max-age) | Anger maximal ålder standardintervallet för POP till ursprunget server Cachevalidering.
[Upphör att gälla sidhuvud behandling](#expires-header-treatment) | Styr generering av `Expires` huvuden genom en POP när funktionen externa maximal ålder är aktiv.
[External Max-Age](#external-max-age) | Anger tidsintervallet maximal ålder för webbläsaren till POP Cachevalidering.
[Tvinga inre maximal ålder](#force-internal-max-age) | Anger intervallet för maximal ålder för POP till ursprung server Cachevalidering.
[H.264-stöd (http-progressiv nedladdning)](#h264-support-http-progressive-download) | Avgör vilka typer av H.264 som kan användas för att strömma innehåll.
[Kontrollera No-Cache-begäran](#honor-no-cache-request) | Anger om en HTTP-klientens no-cache-begäranden som vidarebefordras till den ursprungliga servern.
[Ignorera ursprung No-Cache](#ignore-origin-no-cache) | Anger om CDN ignorerar vissa direktiv som hanteras från en ursprungsservern.
[Ignorera Unsatisfiable intervall](#ignore-unsatisfiable-ranges) | Anger svaret som returneras till klienter när en förfrågan genererar statuskod 416 begärt intervall inte kunde hanteras.
[Internt Max-inaktuell](#internal-max-stale) | Styr hur länge förbi den normala förfallotiden cachelagrade tillgång kan hanteras från en POP när POP inte kan verifiera den cachelagra tillgången med den ursprungliga servern.
[Partiell Cache delning](#partial-cache-sharing) | Anger om en begäran kan generera delvis cachelagrat innehåll.
[Prevalidate cachelagrat innehåll](#prevalidate-cached-content) | Anger om cachelagrat innehåll är berättigad till tidig validering innan TTL-upphör att gälla.
[Uppdatera noll Byte cachefiler](#refresh-zero-byte-cache-files) | Anger hur en HTTP-klientens begäran för en tillgång 0 byte cache hanteras av POP.
[Ange Cacheable ställs statuskoder](#set-cacheable-status-codes) | Definierar de statuskoder som kan resultera i cachelagrat innehåll.
[Inaktuella leverans av innehåll vid fel](#stale-content-delivery-on-error) | Avgör om upphört att gälla cachelagrat innehåll levereras när ett fel uppstår under Cachevalidering eller när det begärda innehållet hämtas från ursprungsservern kunden.
[Inaktuella när Revalidate](#stale-while-revalidate) | Förbättrar prestandan genom att tillåta POP att hantera inaktuell klient till beställaren medan Omverifiering äger rum.

## <a name="comment-feature"></a>Kommentarfunktionen

Den här funktionen är utformad att ge ytterligare information i en regel.

Namn | Syfte
-----|--------
[Kommentar](#comment) | Gör en anteckning som ska läggas till i en regel.
 
## <a name="header-features"></a>Rubrik-funktioner

Dessa funktioner är utformade för att lägga till, ändra eller ta bort rubriker från begäran eller svar.

Namn | Syfte
-----|--------
[Ålder svarshuvud](#age-response-header) | Anger om en ålder svarshuvud inkluderas i svaret till beställaren.
[Felsöka Cache-svarshuvuden](#debug-cache-response-headers) | Anger om ett svar kan omfatta svarshuvud X EC Debug som innehåller information om cacheprincipen för den begärda tillgången.
[Ändra klienten huvudet i begäran](#modify-client-request-header) | Skriver över, lägger till eller tar bort en rubrik i en begäran.
[Ändra klienten svarshuvud](#modify-client-response-header) | Skriver över, lägger till eller tar bort ett sidhuvud från ett svar.
[Ange klientens IP-anpassad rubrik](#set-client-ip-custom-header) | Tillåter den IP-adressen för den begärande klienten som ska läggas till på begäran som en anpassad begärandehuvudet.


## <a name="logging-features"></a>Loggningsfunktioner

Dessa funktioner är utformade för att anpassa de data som lagras i raw loggfiler.

Namn | Syfte
-----|--------
[Anpassad logg fält 1](#custom-log-field-1) | Anger formatet och innehåll som är kopplat till fältet anpassad logg i en rå loggfil.
[Loggen frågesträng](#log-query-string) | Anger om en frågesträng lagras tillsammans med URL-Adressen i åtkomstloggar.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Funktioner för ursprung

Dessa funktioner är utformade för att styra hur CDN kommunicerar med en ursprungsservern.

Namn | Syfte
-----|--------
[Högsta antal Keep-Alive-begäranden](#maximum-keep-alive-requests) | Definierar det maximala antalet begäranden för en Keep-Alive-anslutning innan den är stängd.
[Proxy särskilda rubriker](#proxy-special-headers) | Definierar en uppsättning CDN-specifika begärandehuvuden som vidarebefordras från en POP till en ursprungsserver som.


## <a name="specialty-features"></a>Särskilda funktioner

De här funktionerna ger avancerade funktioner för avancerade användare.

Namn | Syfte
-----|--------
[Cacheable ställs http-metoder](#cacheable-http-methods) | Anger den ytterligare HTTP-metoder som kan cachelagras i nätverket.
[Textstorleken för Cacheable ställs begäran](#cacheable-request-body-size) | Definierar tröskelvärdet för att fastställa om en POST svaret kan cachelagras.
[Variabeln användare](#user-variable) | Endast för intern användning.

 
## <a name="url-features"></a>URL-funktioner

Dessa funktioner kan en begäran att omdirigerad eller skrivas om till en annan URL.

Namn | Syfte
-----|--------
[Följ omdirigeringar](#follow-redirects) | Anger om begäranden kan omdirigeras till värdnamn som definierats i plats-huvudet som returneras av en kund ursprungsservern.
[URL: en omdirigering](#url-redirect) | Omdirigerar begäranden via plats-huvudet.
[URL-omskrivning](#url-rewrite)  | Skriver om begärande-URL.



## <a name="azure-cdn-rules-engine-features-reference"></a>Azure CDN regler motorn funktioner referens

---
### <a name="age-response-header"></a>Ålder svarshuvud
**Syfte**: avgör om en ålder svarshuvud är med i svaret till beställaren.
Värde|Resultat
--|--
Enabled | Rubriken ålder ingår i svaret till beställaren.
Disabled | Rubriken ålder ingår inte i svaret till beställaren.

**Standardbeteende**: inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>Parametrar för bandbredd
**Syfte:** avgör om bandbredd bandbreddsbegränsning parametrarna (till exempel ec_rate och ec_prebuf) är aktiva.

Bandbredd bandbreddsbegränsning parametrarna avgör om hastigheten för dataöverföringen för en klientbegäran är begränsade till en egen takt.

Värde|Resultat
--|--
Enabled|Tillåter POP respektera bandbreddsbegränsning begäranden.
Disabled|Gör POP att ignorera bandbreddsbegränsning parametrar. Det begärda innehållet hanteras normalt (det vill säga utan begränsning av bandbredd).

**Standardbeteende:** aktiverat.
 
[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Begränsning av bandbredd
**Syfte:** begränsar bandbredd för svar som tillhandahålls av POP.

Båda av följande alternativ måste definieras för att ange korrekt bandbreddsbegränsning.

Alternativ|Beskrivning
--|--
Kilobyte per sekund|Ange maximal bandbredd (Kb per sekund) som kan användas för att leverera svaret.
Prebuf sekunder|Ange antalet sekunder för POP att vänta tills bandbredd begränsas. Syftet med den här tidsperioden obegränsad bandbredd är att förhindra att en media player har hackigt eller buffring problem på grund av begränsad bandbredd.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Kringgå Cache
**Syfte:** avgör om begäran bör kringgå cachelagring.

Värde|Resultat
--|--
Enabled|Gör att alla förfrågningar till faller till den ursprungliga servern, även om innehållet har tidigare cachelagrats på POP.
Disabled|Gör POP till cache tillgångar enligt princip som definierats i dess svarshuvuden.

**Standardbeteende:**

- **Stora HTTP:** inaktiverad

<!---
- **ADN:** Enabled

--->

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Cacheable ställs http-metoder
**Syfte:** anger den ytterligare HTTP-metoder som kan cachelagras i nätverket.

Viktig information:

- Den här funktionen förutsätter att GET-svar alltid ska cachelagras. Hämta HTTP-metoden ska därför inte tas med när du ställer in den här funktionen.
- Den här funktionen stöder endast HTTP POST-metoden. Cachelagra efter svar genom att ange den här funktionen `POST`.
- Som standard cachelagras bara begäranden vars brödtext är mindre än 14 Kb. Funktionen Cacheable ställs begäran brödtext storlek att ställa in textstorleken för maximal begäran.

**Standardbeteende:** endast GET-svar cachelagras.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Textstorleken för Cacheable ställs begäran
**Syfte:** definierar tröskelvärdet för att fastställa om en POST svaret kan cachelagras.

Den här tröskeln bestäms genom att ange en högsta begäran textstorleken. Begäranden som innehåller en större brödtext i begäran cachelagras inte.

Viktig information:

- Den här funktionen kan bara användas när POST-svar är berättigade för cachelagring. Funktionen Cacheable ställs http-metoder för att aktivera cachelagring av POST-begäran.
- Begärandetexten beaktas för:
    - x-www-formuläret-urlencoded värden
    - Säkerställa en unik cache-nyckel
- Definiera en stor maximala begäran textstorleken kan påverka prestanda för leverans av data.
    - **Rekommenderat värde:** 14 Kb
    - **Minsta värde:** 1 Kb

**Standardbeteende:** 14 Kb

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Cache-Control sidhuvud behandling
**Syfte:** styr generering av `Cache-Control` huvuden av POP när funktionen externa maximal ålder är aktiv.

Det enklaste sättet att uppnå den här typen av konfiguration är att placera den externa maximal åldern och funktionerna för Cache-Control sidhuvud behandling i samma instruktion.

Värde|Resultat
--|--
Skriv över|Garanterar att följande åtgärder:<br/> -Skriver över den `Cache-Control` huvud som genererats av den ursprungliga servern. <br/>-Lägger till den `Cache-Control` huvud som genereras av funktionen externa maximal ålder för svaret.
Genomströmning|Garanterar att den `Cache-Control` huvud som genereras av funktionen externa maximal ålder aldrig har lagts till i svaret. <br/> Om den ursprungliga servern producerar en `Cache-Control` sidhuvud, överförs via till slutanvändaren. <br/> Om den ursprungliga servern inte ger en `Cache-Control` sidhuvud och sedan det här alternativet kan orsaka svarshuvud inte innehålla en `Cache-Control` huvud.
Lägg till om de saknas|Om en `Cache-Control` huvud togs inte emot från den ursprungliga servern och sedan det här alternativet ökar den `Cache-Control` huvud som genereras av funktionen externa maximal ålder. Det här alternativet är användbart för att garantera att alla tillgångar tilldelas en `Cache-Control` huvud.
Ta bort| Det här alternativet ser till att en `Cache-Control` huvud ingår inte i sidhuvud-svaret. Om en `Cache-Control` huvud har redan tilldelats sedan den tas bort från huvud-svaret.

**Standardbeteende:** skrivas över.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Cachenyckel frågesträng
**Syfte:** avgör om cache-nyckeln innehåller eller utesluter frågan string-parametrar som är associerade med en begäran.

Viktig information:

- Ange en eller flera sträng parametern frågenamn och avgränsa varje parameternamn med ett blanksteg.
- Den här funktionen avgör om frågan string-parametrar är inkluderas eller uteslutas från cache-nyckeln. Ytterligare information har angetts för varje alternativ i följande tabell.

Typ|Beskrivning
--|--
 Ta med|  Anger att varje angiven parameter ska tas med i cache-nyckel. En unik cachenyckel genereras för varje begäran som innehåller ett unikt värde för en frågesträngsparameter som definierats i den här funktionen. 
 Omfatta alla  |Anger att en unik cachenyckel skapas för varje begäran till en tillgång som innehåller en unik frågesträng. Denna typ av konfiguration rekommenderas vanligtvis inte eftersom det kan leda till en liten procentandel cacheträffar. Ett lågt antal träffar i cache ökar belastningen på den ursprungliga servern eftersom den måste hantera fler begäranden. Den här konfigurationen duplicerar cachelagringsbeteendet kallas ”unik-cachen” på sidan cachelagring av frågesträng. 
 Uteslut | Anger att endast de angivna parametrarna är undantagen från cache-nyckeln. Alla andra sträng frågeparametrar ingår i cache-nyckel. 
 Undanta alla  |Anger att alla frågeparametrar sträng undantas från cache-nyckeln. Den här konfigurationen duplicerar ”standard-cache” standard cachelagring av frågesträngar på sidan cachelagring av frågesträng.  

Motorn regler kan du anpassa det sätt som cachelagring av frågesträngar i fråga har implementerats. Du kan till exempel ange att cachelagring av frågesträngar i frågan utförs endast på vissa platser eller filtyper.

Skapa en regel som innehåller en URL-frågan med jokertecken matchar villkor och en kringgå Cache-funktionen om du vill duplicera ”no-cache” frågesträngen cachelagring av frågesträngar på sidan cachelagring av frågesträng. Ange URL: en fråga med jokertecken matchar villkoret en asterisk (*).

>[!IMPORTANT] 
> Om token tillstånd är aktiverat för valfri sökväg för det här kontot, är standard-cache det enda läge som kan användas för cachelagring av frågesträngar i frågan. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Exempelscenarier

Följande exempel för den här funktionen ger ett exempel på begäran och cache--nyckel:

- **Exempel på begäran:** http://wpc.0001.&lt; Domän&gt;/800001/Origin/folder/asset.htm?sessionid=1234 och språk = EN & userid = 01
- **Standard cachenyckel:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Ta med

Exempel på konfiguration:

- **Typ:** inkluderar
- **Parametrar:** språk

Den här typen av konfiguration skulle generera följande fråga sträng parametern cache-nyckel:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Omfatta alla

Exempel på konfiguration:

- **Typ:** omfattar alla

Den här typen av konfiguration skulle generera följande fråga sträng parametern cache-nyckel:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Uteslut

Exempel på konfiguration:

- **Typ:** undanta
- **Parametrar:** sessionid användar-ID

Den här typen av konfiguration skulle generera följande fråga sträng parametern cache-nyckel:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Undanta alla

Exempel på konfiguration:

- **Typ:** utesluta alla

Den här typen av konfiguration skulle generera följande fråga sträng parametern cache-nyckel:

    /800001/Origin/folder/asset.htm

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Omarbetning av cache-nyckel
**Syfte:** skriver om cache-nyckeln som associeras med en begäran.

En cachenyckel måste den relativa sökvägen som identifierar en tillgång för cachelagring. Med andra ord Sök servrarna efter en cachelagrad version av en tillgång enligt dess sökväg som definierats av dess cache-nyckel.

Konfigurera den här funktionen genom att definiera båda av följande alternativ:

Alternativ|Beskrivning
--|--
Ursprungliga sökväg| Definiera den relativa sökvägen till typerna av begäranden vars cachenyckel skrivs om. En relativ sökväg kan definieras genom att välja en grundläggande ursprungssökväg och sedan definiera ett mönster för reguljärt uttryck.
Ny sökväg|Definiera den relativa sökvägen för den nya cache-nyckeln. En relativ sökväg kan definieras genom att välja en grundläggande ursprungssökväg och sedan definiera ett mönster för reguljärt uttryck. Den här relativa sökvägen kan konstrueras dynamiskt genom att använda HTTP-variabler
**Standardbeteende:** en begäran cachenyckel bestäms av URI-begäran.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Kommentar
**Syfte:** tillåter en kommentar som ska läggas till i en regel.

En används för den här funktionen för att tillhandahålla ytterligare information om generella av en regel eller varför en viss matchar villkoret eller funktionen har lagts till i regeln.

Viktig information:

- Högst 150 tecken kan anges.
- Använd endast alfanumeriska tecken.
- Den här funktionen påverkar inte beteendet för regeln. Det är endast avsedda att ge ett område där du kan ange information för framtida bruk eller som kan hjälpa vid felsökning av regeln.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Slutföra Cache Fill
**Syfte:** avgör vad som händer när en begäran resulterar i en partiell cache-miss på en POP.

En partiell cache-miss beskriver Cachestatus för en tillgång som inte hämtades helt till en POP. Om en tillgång endast delvis cachelagras på en POP, sedan vidarebefordras nästa begäran om tillgången igen till den ursprungliga servern.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
En partiell cache-miss inträffar vanligtvis när en användare avbryter en hämtning eller för tillgångar som begärs endast med hjälp av HTTP-begäranden för intervallet. Den här funktionen är mest användbara för stora tillgångar som normalt inte hämtas från början till slut (till exempel videoklipp). Därför kan aktiveras den här funktionen som standard på stora HTTP-plattformen. Den är inaktiverad på alla plattformar.

Behåll standardkonfigurationen för stora HTTP-plattformen, eftersom det minskar belastningen på din kund ursprungsservern och ökar då kunderna hämta ditt innehåll.

På grund av det sätt som i vilken cache inställningar spåras den här funktionen kan inte associeras med följande matchar: Edge Cname, begär sidhuvud Literal, begär huvud med jokertecken, URL-frågan Literal och URL: en fråga med jokertecken.

Värde|Resultat
--|--
Enabled|Återställer standardbeteendet. Standardinställningen är att tvinga POP för att initiera en bakgrundshämtning tillgångens från den ursprungliga servern. Efter som tillgången blir i lokalt cacheminne för den POP.
Disabled|Förhindrar att en POP utför en bakgrundshämtning för tillgången. Resultatet är att nästa begäran om tillgången från den regionen orsakar en POP begära från den ursprungliga servern för kunden.

**Standardbeteende:** aktiverat.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Komprimera filtyper
**Syfte:** definierar filformat för filer som är komprimerade på servern.

Ett filformat kan anges med hjälp av dess Internet medietyp (till exempel Content-Type). Internet medietyp är plattformsoberoende metadata som gör att servrar för att identifiera formatet för en viss tillgång. En lista över vanliga Internet medietyper finns nedan.

Internet-medietyp|Beskrivning
--|--
Text/plain|Filer med oformaterad text
text/html| HTML-filer
text/css|Sammanhängande formatmallar (CSS)
application/x-javascript|Javascript
application/javascript|Javascript
Viktig information:

- Ange flera Internet-medietyper genom att avgränsa dem med ett blanksteg. 
- Den här funktionen komprimerar bara tillgångar vars storlek är mindre än 1 MB. Större tillgångar komprimeras inte av servrar.
- Vissa typer av innehåll, till exempel bilder, video och ljud tillgångar (till exempel JPG, MP3, MP4, etc.) redan har komprimerats. Eftersom ytterligare komprimering på dessa typer av tillgångar inte avsevärt minska filstorleken rekommenderas att du inte aktivera komprimering på dessa.
- Jokertecken, till exempel asterisker stöds inte.
- Se till att du ställer in alternativet inaktiverat komprimering på sidan komprimering för den plattform som den här regeln används innan du lägger till den här funktionen till en regel.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>Anpassad logg fält 1
**Syfte:** anger formatet och det innehåll som ska tilldelas fältet anpassad logg i en rå loggfil.

Den här anpassade fält kan du bestämma vilka förfrågan och svar huvudvärden lagras i loggfilerna.

Som standard kallas anpassad loggfältet ”x-ec_custom-1”. Namnet på det här fältet kan anpassas från sidan rådata Logginställningar.

Format för att ange begärande- och svarshuvuden definieras enligt följande:

Rubriktyp|Format|Exempel
-|-|-
Huvudet i begäran|%{[RequestHeader]()}[jag]() | % {Acceptera-Encoding} jag <br/> {Referent} jag <br/> % {Auktorisering} i
Svarshuvud|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Viktig information:

- En anpassad logg-fältet kan innehålla en kombination av namn på huvudfält och oformaterad text.
- Giltiga tecken för det här fältet är följande: alfanumeriska (0-9, a-z eller A-Z), bindestreck, kolon, semikolon, apostrofer, kommatecken, punkter, understreck, likhetstecken, parenteser, hakparenteser och blanksteg. Procentandel symbolen och klammerparenteser tillåts endast när används för att ange en huvudfält.
- Stavningen för varje fält angivna huvudet måste matcha huvudnamnet som önskade begäran och svar.
- Om du vill ange flera huvuden kan du använda en avgränsare för att ange varje sidhuvud. Du kan till exempel använda en förkortning för varje:
    - AE: % {acceptera-Encoding} i A: % {auktorisering} i Datafält: % {Content-Type} o 

**Standardvärde:** -

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Felsöka Cache-svarshuvuden
**Syfte:** avgör om ett svar kan innehålla svarshuvud X EC Debug som innehåller information om cacheprincipen för den begärda tillgången.

Felsöka cachesvar huvuden som ska inkluderas i svaret när båda av följande villkor uppfylls:

- Felsöka Cache svar huvuden funktionen har aktiverats på den önskade begäranden.
- Begäran definierar en uppsättning debug cache-svarshuvuden som ska inkluderas i svaret.

Felsöka cachesvar huvuden kan begäras genom att inkludera följande huvud och önskade direktiven i förfrågan:

X-EC-Debug: _Directive1_,_Directive2_,_DirectiveN_

**Exempel:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Värde|Resultat
-|-
Enabled|Begäranden för debug cache-svarshuvuden returnerar ett svar som innehåller X EC Debug-huvudet.
Disabled|Rubriken X EC Debug utesluts från svaret.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Internt Max-åldern som standard
**Syfte:** Anger standardintervallet för maximal ålder för POP till ursprunget server Cachevalidering. Med andra ord, hur lång tid som ska gå innan en POP kontrollerar om en cachelagrad tillgång matchar den tillgång som lagras på den ursprungliga servern.

Viktig information:

- Den här åtgärden tar bara för svar från en ursprungsserver som inte tilldelar en maximal ålder indikation i den `Cache-Control` eller `Expires` huvud.
- Den här åtgärden kommer inte ske för tillgångar som inte anses Cacheable ställs.
- Den här åtgärden påverkar inte webbläsaren till POP cache revalidations. Dessa typer av revalidations bestäms av den `Cache-Control` eller `Expires` huvuden som skickas till webbläsaren, som kan anpassas med funktionen externa maximal ålder.
- Resultatet av den här åtgärden har inte synliga påverkar svarshuvuden och det innehåll som returnerades från POP för ditt innehåll, men det kan påverka mängden Omverifiering trafik som skickas från POP till den ursprungliga servern.
- Konfigurera den här funktionen genom att:
    - Att välja statuskoden som en intern max-åldern som standard kan tillämpas.
    - Ange ett heltalsvärde och sedan välja önskad tidsenhet (till exempel sekunder, minuter, timmar, etc.). Det här värdet definierar interna maximal ålder standardintervallet.

- Anger tidsenheten till ”av” tilldelas en intern maximal ålder standardintervallet 7 dagar för begäranden som inte har tilldelats en maximal ålder indikation i sina `Cache-Control` eller `Expires` huvud.
- Den här funktionen kan inte associeras med följande matchar villkor på grund av det sätt som i vilken cache inställningar spåras: 
    - Edge 
    - CNAME-post
    - Begäran sidhuvud Literal
    - Begäran huvud med jokertecken
    - Metod för begäran
    - URL-frågan Literal
    - URL: en fråga med jokertecken

**Standardvärde:** 7 dagar

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Neka åtkomst (403)
**Syfte**: Anger om alla begäranden avvisas med ett 403 förbjuden svar.

Värde | Resultat
------|-------
Enabled| Gör att alla begäranden som uppfyller kriterierna som matchande avvisas med ett 403 förbjuden svar.
Disabled| Återställer standardbeteendet. Standardinställningen är att den ursprungliga servern att avgöra vilken typ av svar som ska returneras.

**Standardbeteende**: inaktiverad

> [!TIP]
   > En möjlig användning för den här funktionen är att associera den med begäran sidhuvud matchar villkoret att blockera åtkomst till HTTP-referenter som använder infogade länkar till ditt innehåll.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Upphör att gälla sidhuvud behandling
**Syfte:** styr generering av `Expires` huvuden genom en POP när funktionen externa maximal ålder är aktiv.

Det enklaste sättet att uppnå den här typen av konfiguration är att placera den externa maximal åldern och upphör att gälla sidhuvud behandling funktioner i samma instruktion.

Värde|Resultat
--|--
Skriv över|Garanterar att följande åtgärder utförs:<br/>-Skriver över den `Expires` huvud som genererats av den ursprungliga servern.<br/>-Lägger till den `Expires` huvud som genereras av funktionen externa maximal ålder för svaret.
Genomströmning|Garanterar att den `Expires` huvud som genereras av funktionen externa maximal ålder aldrig har lagts till i svaret. <br/> Om den ursprungliga servern genererar ett `Expires` rubrik som är den ska passera till slutanvändaren. <br/>Om den ursprungliga servern inte ger ett `Expires` sidhuvud och sedan det här alternativet kan orsaka svarshuvud inte innehålla en `Expires` huvud.
Lägg till om de saknas| Om en `Expires` huvud togs inte emot från den ursprungliga servern och sedan det här alternativet ökar den `Expires` huvud som genereras av funktionen externa maximal ålder. Det här alternativet är användbart för att garantera att alla tillgångar tilldelas en `Expires` huvud.
Ta bort| Garanterar att en `Expires` huvud ingår inte i sidhuvud-svaret. Om en `Expires` huvud har redan tilldelats sedan den tas bort från huvud-svaret.

**Standardbeteende:** skriva över

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Externa maximal ålder
**Syfte:** bestämmer intervallet maximal ålder för webbläsaren till POP Cachevalidering. Hur lång tid som ska förflyta innan en webbläsare kan med andra ord att söka efter en ny version av en tillgång från en POP.

Den här funktionen aktiveras genererar `Cache-Control: max-age` och `Expires` meddelandehuvuden från POP och skicka dem till HTTP-klienten. Dessa huvuden skriver över de som skapats i den ursprungliga servern. Dock användas behandling för Cache-Control-huvudet och upphör att gälla sidhuvud behandling funktioner beteendet.

Viktig information:

- Den här åtgärden påverkar inte POP ursprung server cache revalidations. Dessa typer av revalidations bestäms av den `Cache-Control` och `Expires` sidhuvuden togs emot från den ursprungliga servern och kan anpassas med standard interna Max-ålder och funktionerna för inre Tvingad maximal-ålder.
- Konfigurera den här funktionen genom att ange ett heltalsvärde och välja önskad tidsenhet (till exempel sekunder, minuter, timmar, etc.).
- Ställa in den här funktionen med ett negativt värde leder POP att skicka en `Cache-Control: no-cache` och en `Expires` tid som anges i förflutna med varje svar till webbläsaren. Även om HTTP-klienter inte kommer cachelagrar svaret, påverkar inte den här inställningen på POP möjligheten att cachelagra svaret från den ursprungliga servern.
- Tidsenhet till ”av” inaktiveras den här funktionen. Den `Cache-Control` och `Expires` huvuden cachelagrat svar för den ursprungliga servern ska passera till webbläsaren.

**Standardbeteende:** ut

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Följ omdirigeringar
**Syfte:** avgör om begäranden kan omdirigeras till värdnamn som definierats i plats-huvudet som returneras av en kund ursprungsservern.

Viktig information:

- Begäranden kan endast omdirigeras till kanten skapa CNAME-poster för samma plattform.

Värde|Resultat
-|-
Enabled|Begäranden kan omdirigeras.
Disabled|Begäranden omdirigeras inte.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Tvinga inre maximal ålder
**Syfte:** anger intervallet för maximal ålder för POP till ursprunget server Cachevalidering. Med andra ord, hur lång tid som ska gå innan en POP kan kontrollera om en cachelagrad tillgång matchar den tillgång som lagras på den ursprungliga servern.

Viktig information:

- Den här funktionen åsidosätter maximal ålder intervallet som definierats i `Cache-Control` eller `Expires` huvuden som genereras från en ursprungsservern.
- Webbläsaren till POP cache revalidations påverkas inte av den här funktionen. Dessa typer av revalidations bestäms av den `Cache-Control` eller `Expires` huvuden som skickas till webbläsaren.
- Den här funktionen har inte en synliga effekt i svaret som levereras av en POP till beställaren. Det kan dock ha en effekt på mängden Omverifiering av trafik som skickas från POP till den ursprungliga servern.
- Konfigurera den här funktionen genom att:
    - Att välja statuskoden som en intern maximal ålder tillämpas.
    - Ange ett heltalsvärde och välja önskad tidsenhet (till exempel sekunder, minuter, timmar, etc.). Det här värdet definierar begäran maximal ålder intervall.

- Tidsenhet till ”av” inaktiveras den här funktionen. Ett internt maximal ålder intervall kommer inte att tilldela begärda tillgångar. Om det ursprungliga huvudet inte innehåller instruktioner för cachelagring, cachelagras tillgången enligt inställningen active i funktionen standard interna Max-ålder.
- Den här funktionen kan inte associeras med följande matchar villkor på grund av det sätt som i vilken cache inställningar spåras: 
    - Edge 
    - CNAME-post
    - Begäran sidhuvud Literal
    - Begäran huvud med jokertecken
    - Metod för begäran
    - URL-frågan Literal
    - URL: en fråga med jokertecken

**Standardbeteende:** ut

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>H.264-stöd (http-progressiv nedladdning)
**Syfte:** avgör vilka typer av H.264 filformat som kan användas för att strömma innehåll.

Viktig information:

- Definiera en blankstegsavgränsad tillåtna H.264 filename tillägg i alternativet filnamnstillägg. Alternativet filnamnstillägg att åsidosätta standardbeteendet. Att stöda MP4 och F4V genom att inkludera dessa filnamnstillägg när det här alternativet. 
- Se till att ta en period när du anger varje filnamnstillägget (till exempel .mp4 .f4v).

**Standardbeteende:** HTTP progressiv nedladdning stöder MP4 och F4V media som standard.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>Kontrollera No-Cache-begäran
**Syfte:** avgör om en HTTP-klient har no-cache begäranden ska vidarebefordras till den ursprungliga servern.

En begäran om no-cache inträffar när HTTP-klienten skickar en `Cache-Control: no-cache` och/eller `Pragma: no-cache` rubriken i HTTP-begäran.

Värde|Resultat
--|--
Enabled|Kan en HTTP-klientens no-cache-begäranden ska vidarebefordras till den ursprungliga servern och den ursprungliga servern returnerar svarshuvuden och brödtext via POP till HTTP-klienten.
Disabled|Återställer standardbeteendet. Standardinställningen är att förhindra no-cache-begäranden från att vidarebefordras till den ursprungliga servern.

För all trafik för produktion rekommenderas att lämna den här funktionen i inaktiverad standardtillståndet. Ursprungliga servrarna kommer annars inte att skärma från användare som oavsiktligt utlösa många no-cache-begäranden när du uppdaterar webbsidor eller från många vanliga mediespelare som är kodade för att skicka ett no-cache-huvud med varje video begäran. Den här funktionen kan dock vara praktiskt att tillämpas på vissa icke-produktion mellanlagring och testning kataloger, för att tillåta nytt innehåll som ska hämtas på begäran från den ursprungliga servern.

Cachestatus som rapporteras för en begäran ska kunna vidarebefordras till ett ursprungsservern på grund av den här funktionen är TCP_Client_Refresh_Miss. Rapporten Cache status som är tillgänglig i Core reporting modulen ger statistisk information genom att Cachestatus. På så sätt kan du spåra antalet och procentandelen förfrågningar som vidarebefordras till en ursprungsserver på grund av den här funktionen.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Ignorera ursprung No-Cache
**Syfte:** avgör om CDN ignoreras följande direktiv hanteras från en ursprungsservern:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Viktig information:

- Konfigurera den här funktionen genom att definiera en mellanslags-avgränsad lista över statuskoder som ovannämnda direktiv kommer att ignoreras.
- Uppsättningen koder för giltig status för den här funktionen är: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, och 505.
- Inaktivera funktionen genom att ange ett tomt värde.
- Den här funktionen kan inte associeras med följande matchar villkor på grund av det sätt som i vilken cache inställningar spåras: 
    - Edge 
    - CNAME-post
    - Begäran sidhuvud Literal
    - Begäran huvud med jokertecken
    - Metod för begäran
    - URL-frågan Literal
    - URL: en fråga med jokertecken

**Standardbeteende:** standardbeteendet är ta hänsyn till ovannämnda direktiv.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Ignorera Unsatisfiable intervall 
**Syfte:** bestämmer svaret som returneras till klienter när en förfrågan genererar statuskod 416 begärt intervall inte kunde hanteras.

Som standard returneras statuskoden när den angivna byte-intervall begäranden inte kan tillgodoses av en POP och huvudfältet ett intervall om begäran har inte angetts.

Värde|Resultat
-|-
Enabled|Förhindrar att POP svarar på en ogiltig byteintervall begäran med statuskod 416 begärt intervall inte kunde hanteras. Servrarna kommer i stället leverera begärda tillgången och returnera ett 200 OK till klienten.
Disabled|Återställer standardbeteendet. Standardinställningen är att respektera 416 begärt intervall inte kunde hanteras statuskoden.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Internt Max-inaktuell
**Syfte:** styr hur länge tidigare normala förfallotiden cachelagrade tillgång kan hanteras från en POP när POP inte kan verifiera den cachelagra tillgången med den ursprungliga servern.

Normalt när en tillgång maximal ålder tiden går ut skickar POP en begäran om förnyelse till den ursprungliga servern. Ursprung servern kommer sedan svara med antingen 304 inte har ändrats ge POP en ny låna ut på den cachelagra tillgången eller med 200 OK för att tillhandahålla POP med en uppdaterad version av cachelagrade tillgången.

Om POP kan inte upprätta en anslutning till den ursprungliga servern vid försök Omverifiering, och sedan interna Max-föråldrade funktionen styr om och hur länge fortsätta POP att fungera nu föråldrade tillgången.

Observera att detta tidsintervall startar när tillgångens max-ålder upphör att gälla, inte när misslyckade Omverifiering inträffar. Därför är den längsta tid under vilken en tillgång kan hanteras utan lyckad validering tidsperiod som angetts av kombinationen av maximal ålder plus max inaktuell. Till exempel om en tillgång cachelagrades 9:00 med en maximal ålder på 30 minuter och max-inaktuell 15 minuter skulle sedan Omverifiering av misslyckade försöket 9:44 leda till en användare tar emot inaktuella cachelagrade tillgången, medan en misslyckad validering försöket 9:46 skulle resultera i en d användare tar emot en 504 Gateway-Timeout.

Ett värde som konfigurerats för den här funktionen har ersatts av `Cache-Control: must-revalidate` eller `Cache-Control: proxy-revalidate` huvuden som togs emot från den ursprungliga servern. Om någon av dessa huvuden tas emot från den ursprungliga servern när en tillgång till en början cachelagras, kommer POP inte att uppfylla en inaktuella cachelagrade tillgång. I sådana fall returnerar POP en 504 Gateway-Timeout-fel om POP inte kan verifiera med ursprung när tillgångens maximal ålder intervall har upphört att gälla.

Viktig information:

- Konfigurera den här funktionen genom att:
    - Att välja statuskoden som en aktuell max tillämpas.
    - Ange ett heltalsvärde och sedan välja önskad tidsenhet (till exempel sekunder, minuter, timmar, etc.). Det här värdet anger den interna max aktuell som ska användas.

- Tidsenhet till ”av” inaktiveras den här funktionen. En cachelagrad tillgång hanteras inte utöver dess normala förfallotid.
- Den här funktionen kan inte associeras med följande matchar villkor på grund av det sätt som i vilken cache inställningar spåras: 
    - Edge 
    - CNAME-post
    - Begäran sidhuvud Literal
    - Begäran huvud med jokertecken
    - Metod för begäran
    - URL-frågan Literal
    - URL: en fråga med jokertecken

**Standardbeteende:** två minuter

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Loggen frågesträng
**Syfte:** avgör om en frågesträng lagras tillsammans med URL-Adressen i åtkomstloggar.

Värde|Resultat
-|-
Enabled|Tillåter lagring av frågesträngar när du registrerar URL: er i en åtkomst-loggen. Om en URL inte innehåller en frågesträng, har en effekt inte i det här alternativet.
Disabled|Återställer standardbeteendet. Standardinställningen är att ignorera frågesträngar när du registrerar URL: er i en åtkomst-loggen.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Högsta antal Keep-Alive-begäranden
**Syfte:** definierar det maximala antalet begäranden för en Keep-Alive-anslutning innan den är stängd.

Ange det maximala antalet begäranden till ett lågt värde rekommenderas inte och kan resultera i försämrade prestanda.

Viktig information:

- Ange det värdet hela heltal.
- Ta inte med kommatecken eller punkter i det angivna värdet.

**Standardvärde:** 10 000 begäranden

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Ändra klienten huvudet i begäran
**Syfte:** varje begäran innehåller en uppsättning begärandehuvuden som beskriver den. Den här funktionen kan antingen:

- Lägg till eller skriva över värdet som tilldelas ett huvud. Om det angivna begärandehuvudet inte finns, sedan den här funktionen lägger till det på begäran.
- Ta bort ett huvud i begäran.

Begäranden som vidarebefordras till en ursprungsservern visar de ändringar som gjorts av den här funktionen.

En av följande åtgärder kan utföras på ett huvud:

Alternativ|Beskrivning|Exempel
-|-|-
Lägg till|Det angivna värdet läggs till i slutet av det befintliga begäran huvudvärdet.|**Begära huvudvärde (klient):**Value1 <br/> **Begära huvudvärde (regelmotor HTTP):** Value2 <br/>**Ny begäran huvudvärde:** Value1Value2
Skriv över|Begäran huvudvärde sätts till det angivna värdet.|**Begära huvudvärde (klient):**Value1 <br/>**Begära huvudvärde (regelmotor HTTP):** Value2 <br/>**Ny begäran huvudvärde:** Value2 <br/>
Ta bort|Tar bort det angivna begärandehuvudet.|**Begära huvudvärde (klient):**Value1 <br/> **Ändra konfigurationen för klienten begärandehuvudet:** ta bort begärandehuvudet i fråga. <br/>**Resultat:** det angivna begärandehuvudet inte vidarebefordras till den ursprungliga servern.

Viktig information:

- Kontrollera att värdet som anges i alternativet är en exakt matchning för begärandehuvudet önskade.
- Om beaktas inte vid identifiering av ett sidhuvud. Till exempel någon av följande variationer av den `Cache-Control` huvudets namn kan användas för att identifiera den:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- När du anger ett namn för sidhuvud, Använd endast alfanumeriska tecken, bindestreck och understreck.
- Om du tar bort ett sidhuvud förhindras från att vidarebefordras till en ursprungsserver som av POP.
- Följande huvuden är reserverade och kan inte ändras av den här funktionen:
    - vidarebefordras
    - värd
    - via
    - Varning
    - x-forwarded-for
    - Alla huvud-namn som börjar med ”x ec” är reserverade.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Ändra klienten svarshuvud
Varje svaret innehåller en uppsättning svarshuvuden som beskriver den. Den här funktionen kan antingen:

- Lägg till eller skriva över värdet som tilldelas en Svarsrubrik. Om det angivna svarshuvudet inte finns, sedan den här funktionen lägger till det i svaret.
- Ta bort en svarshuvud från svaret.

Som standard definieras svar huvudvärden genom en ursprungsservern och POP.

En av följande åtgärder kan utföras på en svarshuvud:

Alternativ|Beskrivning|Exempel
-|-|-
Lägg till|Det angivna värdet läggs till i slutet av det befintliga värdet för huvudet svar.|**Svaret huvudvärde (klient):**Value1 <br/> **Svaret huvudvärde (regelmotor HTTP):** Value2 <br/>**Ny svar huvudvärde:** Value1Value2
Skriv över|Svaret huvudvärde sätts till det angivna värdet.|**Svaret huvudvärde (klient):**Value1 <br/>**Svaret huvudvärde (regelmotor HTTP):** Value2 <br/>**Ny svar huvudvärde:** Value2 <br/>
Ta bort|Tar bort det angivna svarshuvudet.|**Svaret huvudvärde (klient):** Value1 <br/> **Ändra konfigurationen för klienten svarshuvud:** ta bort rubriken i fråga. <br/>**Resultat:** det angivna svarshuvudet inte vidarebefordras till beställaren.

Viktig information:

- Kontrollera att värdet som anges i alternativet är en exakt matchning för det önskade svarshuvudet. 
- Om beaktas inte vid identifiering av ett sidhuvud. Till exempel någon av följande variationer av den `Cache-Control` huvudets namn kan användas för att identifiera den:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Tar bort ett sidhuvud kan den vidarebefordras till beställaren.
- Följande huvuden är reserverade och kan inte ändras av den här funktionen:
    - accept-encoding
    - ålder
    - anslutning
    - content-encoding
    - content-length
    - content-range
    - datum
    - server
    - släpvagn
    - transfer-encoding
    - Uppgradera
    - variera
    - via
    - Varning
    - Alla huvud-namn som börjar med ”x ec” är reserverade.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Partiell Cache delning
**Syfte:** avgör om en begäran kan generera delvis cachelagrat innehåll.

Partiell cacheminnet kan sedan användas för att uppfylla nya begäranden för innehållet tills det begärda innehållet cachelagras fullständigt.

Värde|Resultat
-|-
Enabled|Begäranden kan generera delvis cachelagrat innehåll.
Disabled|Begäranden kan bara skapa en fullständigt cachelagrad version av det begärda innehållet.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>Prevalidate cachelagrat innehåll
**Syfte:** anger om cachelagrat innehåll ska vara tillämplig för tidig validering innan TTL-upphör att gälla.

Ange hur lång tid innan den upphör att gälla för det begärda innehållet TTL under vilken den är aktuell för tidig validering.

Viktig information:

- Att välja ”Off” som tidsenheten kräver validering efter det cachelagrade innehållet TTL-värde har gått ut. Tid anges inte och ignoreras.

**Standardbeteende:** ut. Validering kan endast utföras när det cachelagrade innehållet TTL har gått ut.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Proxy särskilda rubriker
**Syfte:** definierar en uppsättning CDN-specifika begärandehuvuden som vidarebefordras från en POP till en ursprungsservern.

Viktig information:

- Varje CDN-specifika begärandehuvudet som definierats i den här funktionen ska vidarebefordras till en ursprungsservern.
- Förhindra att ett CDN-specifikt huvud som vidarebefordras till en ursprungsservern genom att ta bort den från den här listan.

**Standardbeteende:** alla CDN-specifika begärandehuvuden vidarebefordras till den ursprungliga servern.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Uppdatera noll Byte cachefiler
**Syfte:** bestämmer hur ett HTTP-klientens begäran om en 0 byte cache tillgång hanteras av POP.

Giltiga värden är:

Värde|Resultat
--|--
Enabled|Gör POP hämtar tillgången från den ursprungliga servern.
Disabled|Återställer standardbeteendet. Standardbeteendet är att hantera upp giltig cache tillgångar på begäran.
Den här funktionen krävs inte för rätt cachelagring och leverans av innehåll, men kan användas som en lösning. Dynamiskt innehåll generatorer på ursprung servrar kan till exempel oavsiktligt resultera i 0 byte-svar som skickas till POP. Dessa typer av svar cachelagras vanligtvis av POP. Om du vet att 0 byte svar inte är ett giltigt svar 

för sådana innehåll, sedan den här funktionen kan förhindra att dessa typer av tillgångar hanteras till dina klienter.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Ange Cacheable ställs statuskoder
**Syfte:** definierar en uppsättning statuskoder som kan resultera i cachelagrat innehåll.

Som standard är cachelagring endast aktiverad för 200 OK svar.

Definiera en blankstegsavgränsad önskade statuskoder.

Viktig information:

- Aktivera funktionen Ignorera ursprung No-Cache. Om den här funktionen inte är aktiverad kan inte 200 OK svar cachelagras.
- Uppsättningen koder för giltig status för den här funktionen är: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, och 505.
- Den här funktionen kan inte användas för att inaktivera cachelagring för svar som genererar en 200 OK statuskod.

**Standardbeteende:** cachelagring är aktiverat för svar som genererar en 200 OK statuskod.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Ange klientens IP-anpassad rubrik
**Syfte:** lägger till en anpassad rubrik som identifierar den begärande klienten med IP-adress på begäran.

Alternativet huvudet definierar namnet på anpassade begärandehuvudet där klientens IP-adress lagras.

Den här funktionen kan en kund ursprungsservern ta reda på klient-IP-adresser via ett anpassat huvud. Om begäran skickades från cache, kommer den ursprungliga servern inte informeras om klientens IP-adress. Därför rekommenderas det att den här funktionen kan användas med objekt som inte är cachelagrade.

Se till att angivna huvudets namn inte matchar något av följande namn:

- Standard begäran sidhuvud namn. En lista över standard sidhuvud namn finns i [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Namn på reserverade huvud:
    - forwarded-for
    - värd
    - variera
    - via
    - Varning
    - x-forwarded-for
    - Alla huvud-namn som börjar med ”x ec” är reserverade.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Inaktuella leverans av innehåll vid fel
**Syfte:** avgör om innehåll som förfallit cachelagrade levereras när ett fel uppstår under Cachevalidering eller när det begärda innehållet hämtas från ursprungsservern kunden.

Värde|Resultat
-|-
Enabled|Inaktuella innehållet skickades till beställaren när ett fel uppstår under en anslutning till en ursprungsserver som.
Disabled|Den ursprungliga servern fel vidarebefordras till beställaren.

**Standardbeteende:** inaktiverad

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Inaktuella när Revalidate
**Syfte:** förbättrar prestanda genom att tillåta POP att hantera inaktuella innehåll till beställaren medan Omverifiering äger rum.

Viktig information:

- Den här funktionen fungerar beror enligt den valda enheten.
    - **Tidsenhet:** anger hur lång tid och välja en tidsenhet (till exempel sekunder, minuter, timmar, etc.) så att inaktuella leverans av innehåll. Den här typen av installationsprogrammet tillåter CDN att utöka tidslängd som det kan leverera innehåll innan du kräver verifiering enligt följande formel: **TTL** + **inaktuella medan verifiera tid** 
    - **Av:** väljer ”av” till att kräva validering innan en begäran för inaktuella innehåll kan hanteras.
        - Ange hur lång tid eftersom den är och kommer att ignoreras.

**Standardbeteende:** ut. Validering måste utföras innan det begärda innehållet kan hanteras.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Token Auth
**Syfte:** avgör om tokenbaserad autentisering tillämpas på en begäran.

Om Token-baserad autentisering är aktiverad hanteras endast begäranden som tillhandahåller en krypterad token och uppfyller de krav som anges av säkerhetstoken.

Den krypteringsnyckel som används för att kryptera och dekryptera token värden bestäms av den primärnyckeln och alternativ för säkerhetskopiering nyckel på sidan Token Auth. Tänk på att krypteringsnycklarna är plattformsspecifika.

Värde | Resultat
------|---------
Enabled | Skyddar det begärda innehållet med Token-baserad autentisering. Endast begäranden från klienter som anger en giltig token och uppfyller dess krav att användas. FTP-transaktioner är undantagna från Token-baserad autentisering.
Disabled| Återställer standardbeteendet. Standardinställningen är att konfigurationen tokenbaserad autentisering att avgöra om en begäran ska skyddas.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Token Auth DOS-kod
**Syfte:** avgör vilken typ av svar som returneras till en användare när en begäran nekades på grund av token-baserad autentisering.

Token Auth DOS-kod kan inte användas med en alltid matchar villkoret. Använd i stället den **anpassad DOS-hantering** i avsnittet den **Token Auth** sida av den **hantera** portal. Mer information finns i [skydda Azure CDN tillgångar med tokenautentisering](cdn-token-auth.md).

I följande tabell visas tillgängliga svarskoder.

Svarskod|Svaret namn|Beskrivning
-------------|-------------|--------
301|Flytta permanent|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i huvudet plats.
302|Hittad|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i huvudet plats. Den här statuskoden är branschstandard standardmetoden för att utföra en omdirigering.
307|Tillfällig omdirigering|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i huvudet plats.
401|Behörighet saknas|Kombinera den här statuskoden med de svar WWW-Authenticate-huvud kan du be en användare för autentisering.
403|Förbjudna|Detta är standard 403 förbjuden statusmeddelandet som en obehörig användare kan se när du försöker komma åt skyddat innehåll.
404|Filen hittades inte|Den här statusen indikerar att HTTP-klienten kunde kommunicera med servern, men det gick inte att hitta det begärda innehållet.

#### <a name="url-redirection"></a>URL-omdirigering

Den här funktionen har stöd för URL: en omdirigering till en användardefinierad URL när den är konfigurerad för att returnera en 3xx statuskod. URL: en användardefinierad kan anges genom att utföra följande steg:

1. Välj en 3xx svarskod för funktionen Token Auth DOS-kod.
2. Välj alternativet för valfritt namn för huvudet ”plats”.
3. Ange valfria huvudvärde till önskad URL.

Om en URL inte har definierats för en 3xx statuskod, returneras sidan standard svar för en 3xx statuskod till användaren.

URL-omdirigering gäller endast för 3xx svarskoder.

Alternativet valfritt huvudvärde stöder alfanumeriska tecken, citattecken och blanksteg.

#### <a name="authentication"></a>Autentisering

Den här funktionen stöder möjligheten att inkludera WWW-Authenticate-huvud när svarar på en obehörig begäran för innehåll som skyddas av Token-baserad autentisering. Om WWW-Authenticate-huvud har ställts in på ”basic” i konfigurationen, och sedan en obehörig användare tillfrågas om autentiseringsuppgifter.

Konfigurationen ovan kan uppnås genom att utföra följande steg:

1. Välj ”401” som svarskoden för funktionen Token Auth DOS-kod.
2. Välj ”WWW autentisera” alternativet valfria huvudets namn.
3. Ange alternativet valfria huvudvärde till ”basic”.

WWW-Authenticate-huvud gäller endast för 401 svarskoder.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Token Auth Ignorera skiftläge för URL
**Syfte:** avgör om URL: en jämförelser av tokenbaserad autentisering är skiftlägeskänsliga.

De parametrar som påverkas av den här funktionen är:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Giltiga värden är:

Värde|Resultat
---|----
Enabled|Gör POP för att Ignorera skiftläge vid jämförelse av URL: er för tokenbaserad autentiseringsparametrar.
Disabled|Återställer standardbeteendet. Standardinställningen är för URL: en jämförelser för Token-autentisering ska vara skiftlägeskänslig.

**Standardbeteende:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Tokenparameter Auth
**Syfte:** avgör om tokenbaserad autentisering frågesträngparametern ska ändras.

Viktig information:

- Alternativet värde definierar den parameternamn för frågesträng som en token kan anges.
- Alternativet värde kan inte anges till ”ec_token”.
- Kontrollera att namnet som definierats i alternativet värde innehåller endast giltiga tecken i URL: en.

Värde|Resultat
----|----
Enabled|Alternativet värde definierar den parameternamn för frågesträng som token ska definieras.
Disabled|En token kan anges som ett odefinierat frågesträngparametern i begärande-URL.

**Standardbeteende:** inaktiverad. En token kan anges som ett odefinierat frågesträngparametern i begärande-URL.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>URL Redirect
**Syfte:** omdirigerar begäranden via plats-huvudet.

Konfigurationen för den här funktionen kräver att ange följande alternativ:

Alternativ|Beskrivning
-|-
Kod|Välj svarskoden returneras till beställaren.
Källan & mönster| Dessa inställningar definierar ett mönster för begäran URI som identifierar typ av begäranden kan omdirigeras. Omdirigeras endast begäranden vars URL uppfyller båda av följande kriterier: <br/> <br/> **Källa (eller innehålls åtkomstpunkt):** Välj en relativ sökväg som identifierar en ursprungsservern. Detta är avsnittet ”/XXXX/” och namnet på slutpunkten. <br/> **Källa (mönster):** ett mönster som identifierar begäranden av relativ sökväg måste anges. Det här mönstret för reguljära uttryck måste ange en sökväg som startar direkt efter den tidigare valda innehållsåtkomst peka (se ovan). <br/> -Kontrollera att begäran URI villkoren (det vill säga källa & mönster) tidigare inte krockar med några matchar villkoren för den här funktionen. <br/> -Ange ett mönster; Om du använder ett tomt värde som mönstret matchar alla strängar.
Mål| Definiera den URL som ovan begäranden ska omdirigeras. <br/> Konstruera dynamiskt med den här URL: <br/> -Ett mönster för reguljärt uttryck <br/>-HTTP variabler <br/> Ersätta de värden som hämtats i mönstret för källan till målet mönstret med $_n_ där _n_ identifierar ett värde i den ordning som den hämtades. Till exempel representerar 1 USD det första värdet som avbildas i käll-mönster, medan $2 representerar det andra värdet. <br/> 
Vi rekommenderar starkt att använda en absolut URL. Användning av en relativ URL kan omdirigera CDN URL: er till en ogiltig sökväg.

**Exempelscenario**

Det här exemplet visar hur du omdirigera en kant CNAME-URL som motsvarar denna grundläggande CDN-URL: http://marketing.azureedge.net/brochures

Kvalificerade begäranden omdirigeras till den här grundläggande edge CNAME-URL: http://cdn.mydomain.com/resources

Den här URL: en omdirigering kan uppnås genom följande konfiguration: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Viktiga punkter:**

- Funktionen omdirigerings-URL: en definierar begäran URL: er som omdirigeras. Därför krävs inte ytterligare matchar villkoren. Även om det matchar villkoret har angetts som ”Always”, omdirigeras begäranden som pekar på mappen ”broschyrer” på ”marknadsföring” kund ursprung. 
- Alla matchande begäranden omdirigeras till gränsen CNAME-URL som definierats i alternativet mål. 
    - Exempelscenario #1: 
        - Exempel på begäran (CDN URL): http://marketing.azureedge.net/brochures/widgets.pdf 
        - URL-begäran (efter omdirigering): http://cdn.mydomain.com/resources/widgets.pdf  
    - Exempelscenario #2: 
        - Exempel på begäran (kant CNAME URL): http://marketing.mydomain.com/brochures/widgets.pdf 
        - URL-begäran (efter omdirigering): http://cdn.mydomain.com/resources/widgets.pdf Exempelscenario
    - Exempelscenario #3: 
        - Exempel på begäran (kant CNAME URL): http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - URL-begäran (efter omdirigering): http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- Variabeln begära schemat (% {schema}) har utnyttjas i alternativet mål. Detta säkerställer att begäran schemat förblir oförändrad efter omdirigeringen.
- URL-segment som har hämtats från begäran läggs till den nya URL via ”$1”.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>URL-omskrivning
**Syfte:** skriver om begärande-URL.

Viktig information:

- Konfigurationen för den här funktionen kräver att ange följande alternativ:

Alternativ|Beskrivning
-|-
 Källan & mönster | Dessa inställningar definierar ett mönster för begäran URI som identifierar typ av begäranden som kan skrivas. Kommer att skrivas om endast begäranden vars URL uppfyller båda av följande kriterier: <br/>     - **Källa (eller innehålls åtkomstpunkt):** Välj en relativ sökväg som identifierar en ursprungsservern. Detta är avsnittet ”/XXXX/” och namnet på slutpunkten. <br/> - **Källa (mönster):** ett mönster som identifierar begäranden av relativ sökväg måste anges. Det här mönstret för reguljära uttryck måste ange en sökväg som startar direkt efter den tidigare valda innehållsåtkomst peka (se ovan). <br/> Kontrollera att begäran URI villkoren (det vill säga källa & mönster) tidigare inte krockar med något av matchar villkoren som har definierats för den här funktionen. Ange ett mönster; Om du använder ett tomt värde som mönstret matchar alla strängar. 
 Mål  |Definiera relativ URL som ovan begäranden ska skrivas med: <br/>    1. Att välja en innehålls åtkomstpunkt som identifierar en ursprungsservern. <br/>    2. Definiera en relativ sökväg med hjälp av: <br/>        -Ett mönster för reguljärt uttryck <br/>        -HTTP variabler <br/> <br/> Ersätta de värden som hämtats i mönstret för källan till målet mönstret med $_n_ där _n_ identifierar ett värde i den ordning som den hämtades. Till exempel representerar 1 USD det första värdet som avbildas i käll-mönster, medan $2 representerar det andra värdet. 
 Den här funktionen gör POP att skriva om URL: en utan att utföra en traditionell omdirigering. Detta innebär att beställaren får samma svarskoden som om omskrivet URL: en hade begärts.

**Exempelscenario 1**

Det här exemplet visar hur du omdirigera en kant CNAME-URL som motsvarar denna grundläggande CDN-URL: http://marketing.azureedge.net/brochures/

Kvalificerade begäranden omdirigeras till den här grundläggande edge CNAME-URL: http://MyOrigin.azureedge.net/resources/

Den här URL: en omdirigering kan uppnås genom följande konfiguration: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Exempelscenario 2**

Det här exemplet visar hur du omdirigera en kant CNAME-URL från versaler till gemener med reguljära uttryck.

Den här URL: en omdirigering kan uppnås genom följande konfiguration: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Viktiga punkter:**

- Funktionen URL-omskrivning om definierar begäran URL: er som ska skrivas. Därför krävs inte ytterligare matchar villkoren. Även om det matchar villkoret har angetts som ”Always”, ska begäranden som pekar på mappen ”broschyrer” på ”marknadsföring” kund ursprung skrivas.

- URL-segment som har hämtats från begäran läggs till den nya URL via ”$1”.

#### <a name="compatibility"></a>Efterlevnad

Den här funktionen innehåller matchar villkoren som måste uppfyllas innan den kan tillämpas på en begäran. Den här funktionen är inte kompatibel med följande matchar villkor för att förhindra att ställa in motstridiga matchningsvillkor:

- SOM tal
- CDN ursprung
- IP-adress för klient
- Kunden ursprung
- Schemat för begäran
- URL-sökväg-katalog
- URL-sökväg-tillägget
- URL-sökväg filnamn
- URL-sökväg Literal
- URL-sökväg Regex
- URL-sökväg med jokertecken
- URL-frågan Literal
- Frågeparametern för URL
- URL-frågan Regex
- URL: en fråga med jokertecken

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Variabeln användare
**Syfte:** endast för intern användning.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>Nästa steg
* [Regelmotor – referens](cdn-rules-engine-reference.md)
* [Regelmotor – villkorliga uttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regelmotor – matchningsvillkor](cdn-rules-engine-reference-match-conditions.md)
* [Åsidosätta HTTP beteende regler-motorn](cdn-rules-engine.md)
* [Översikt över Azure CDN](cdn-overview.md)
