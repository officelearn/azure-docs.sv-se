---
title: Azure CDN regelmotor – funktioner | Microsoft Docs
description: Referensdokumentation för Azure CDN regelmotor – funktioner.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: d5be292c66a07f43b40d12e10e4939d9d91559e1
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395249"
---
# <a name="azure-cdn-rules-engine-features"></a>Azure CDN regelmotor – funktioner
Den här artikeln innehåller detaljerade beskrivningar av tillgängliga funktioner för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Den tredje delen av en regel är funktionen. En funktion som definierar vilken åtgärd som tillämpas på typ av begäran som har identifierats av en uppsättning matchningsvillkor.

## <a name="access-features"></a>Access-funktioner

De här funktionerna är utformade för att styra åtkomsten till innehåll.

Namn | Syfte
-----|--------
[Neka åtkomst (403)](#deny-access-403) | Anger om alla begäranden avvisas med ett 403 Forbidden-svar.
[Token Auth](#token-auth) | Avgör om tokenbaserad autentisering tillämpas på en begäran.
[Token Auth DOS-kod](#token-auth-denial-code) | Anger typ av svar som returneras till en användare när en begäran nekas på grund av tokenbaserad autentisering.
[Token Auth Ignorera skiftläge för URL: en](#token-auth-ignore-url-case) | Anger om URL-jämförelser som gjorts av tokenbaserad autentisering är skiftlägeskänsliga.
[Parameter-token Auth](#token-auth-parameter) | Anger om frågesträngparametern för tokenbaserad autentisering ska ändras.


## <a name="caching-features"></a>Cachelagring av funktioner

De här funktionerna är utformade för att anpassa när och hur innehåll cachelagras.

Namn | Syfte
-----|--------
[Parametrar för bandbredd](#bandwidth-parameters) | Anger om bandbredd begränsning parametrarna (till exempel ec_rate och ec_prebuf) är aktiva.
[Bandbreddsbegränsning](#bandwidth-throttling) | Begränsar bandbredd för svaret från den point-of-presence (POP).
[Kringgå Cache](#bypass-cache) | Anger om begäran bör kringgå cachelagring.
[Cache-Control-huvud behandling](#cache-control-header-treatment) | Styr generering av `Cache-Control` rubriker av POP när externa Max-Age-funktionen är aktiv.
[Cachenyckel frågesträng](#cache-key-query-string) | Avgör om cachenyckel innehåller eller utesluter parametrar för frågesträngen som är associerade med en begäran.
[Cachenyckel omskrivning](#cache-key-rewrite) | Skriver om cache-nyckeln som associeras med en begäran.
[Slutför Cache fyllning](#complete-cache-fill) | Anger vad som händer när en begäran resulterar i en partiell cachemiss på en POP.
[Komprimera filtyper](#compress-file-types) | Definierar filformat för filer som är komprimerade på servern.
[Standard interna maxåldern](#default-internal-max-age) | Anger max-age standardintervallet för POP till ursprunget server cache omverifieringen.
[Upphör att gälla rubrik behandling](#expires-header-treatment) | Styr generering av `Expires` huvuden genom en POP när externa Max-Age-funktionen är aktiv.
[Externa maxåldern](#external-max-age) | Anger tidsintervallet maxåldern för webbläsaren till POP cache omverifieringen.
[Tvinga interna maxåldern](#force-internal-max-age) | Anger max-age-intervall för POP till ursprunget server cache omverifieringen.
[H.264-stöd (HTTP progressiv nedladdning)](#h264-support-http-progressive-download) | Avgör vilka typer av H.264-filformat som kan användas för att strömma innehåll.
[Begäran om äran No-Cache](#honor-no-cache-request) | Anger om en HTTP-klient Nej-cachebegäranden vidarebefordras till den ursprungliga servern.
[Ignorera ursprung No-Cache](#ignore-origin-no-cache) | Anger om CDN ignorerar vissa direktiv som hanteras från en ursprungsservern.
[Ignorera Unsatisfiable intervall](#ignore-unsatisfiable-ranges) | Anger svaret som returneras till klienter när en förfrågan genererar statuskod 416 begärda intervallet inte Satisfiable.
[Intern Max-inaktuell](#internal-max-stale) | Kontrollerar hur länge de senaste normala förfallotiden en cachelagrad tillgång kan hanteras från en POP när POP inte kan verifiera den cachelagrade tillgången med den ursprungliga servern.
[Partiell Cache delning](#partial-cache-sharing) | Anger om en begäran kan generera delvis cachelagrat innehåll.
[Prevalidate cachelagrat innehåll](#prevalidate-cached-content) | Anger om cachelagrat innehåll är berättigade tidig Omverifiering innan dess TTL upphör att gälla.
[Uppdatera filer i Internetcachen noll Byte](#refresh-zero-byte-cache-files) | Anger hur en HTTP-klientens begäran för en tillgång på 0 byte-cache hanteras av POP: erna.
[Ange komma statuskoder](#set-cacheable-status-codes) | Definierar uppsättningen med statuskoder som kan resultera i cachelagrat innehåll.
[Inaktuella leverans av innehåll vid fel](#stale-content-delivery-on-error) | Avgör om upphört att gälla cachelagrat innehåll skickas när ett fel uppstår under cache omverifieringen eller vid hämtning av det begärda innehållet från den ursprungliga servern för kunden.
[Föråldrade vid Revalidate](#stale-while-revalidate) | Förbättrar prestanda genom att låta POP: erna att hantera inaktuell klient till den som begär medan omverifieringen genomförs.

## <a name="comment-feature"></a>Kommentarfunktionen

Den här funktionen är utformad att ge ytterligare information i en regel.

Namn | Syfte
-----|--------
[Kommentar](#comment) | Gör en anteckning som ska läggas till i en regel.
 
## <a name="header-features"></a>Huvud-funktioner

De här funktionerna är utformade för att lägga till, ändra eller ta bort rubriker från på begäran eller ett svar.

Namn | Syfte
-----|--------
[Ålder-svarshuvud](#age-response-header) | Anger om en ålder svarshuvud ska tas med i svaret skickas till den som begär.
[Felsöka Cache-svarshuvuden](#debug-cache-response-headers) | Anger om ett svar kan omfatta svarshuvudet X-EG-Debug, som innehåller information om cache-principen för den begärda tillgången.
[Ändra begärandehuvud i klienten](#modify-client-request-header) | Skriver över, lägger till eller tar bort en rubrik från en begäran.
[Ändra klienten svarshuvud](#modify-client-response-header) | Skriver över, lägger till eller tar bort en rubrik från ett svar.
[Ange anpassade klientens IP-huvud](#set-client-ip-custom-header) | Tillåter den IP-adressen för den begärande klienten som ska läggas till på begäran som en rubrik för anpassad begäran.


## <a name="logging-features"></a>Loggningsfunktioner

De här funktionerna är utformade för att anpassa de data som lagras i råa loggfiler.

Namn | Syfte
-----|--------
[Den anpassade loggen fält 1](#custom-log-field-1) | Anger formatet och innehåll som är kopplat till den anpassade loggen fält i en rå loggfil.
[Log frågesträng](#log-query-string) | Anger om en frågesträng lagras tillsammans med URL: en i åtkomstloggar.


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

## <a name="origin-features"></a>Ursprung funktioner

De här funktionerna är utformade för att styra hur CDN kommunicerar med en ursprungsservern.

Namn | Syfte
-----|--------
[Maximalt antal Keep-Alive förfrågningar](#maximum-keep-alive-requests) | Definierar det maximala antalet förfrågningar för Keep-Alive-anslutningen innan den är stängd.
[Proxy särskilda rubriker](#proxy-special-headers) | Definierar uppsättningen med CDN-specifika begärandehuvuden som vidarebefordras från en POP till en ursprungsservern.


## <a name="specialty-features"></a>Dessa funktioner

De här funktionerna ger avancerade funktioner för avancerade användare.

Namn | Syfte
-----|--------
[Komma HTTP-metoder](#cacheable-http-methods) | Anger uppsättningen ytterligare HTTP-metoder som kan cachelagras i nätverket.
[Textstorleken för komma begäran](#cacheable-request-body-size) | Definierar tröskelvärdet för att fastställa om ett INLÄGG svar kan cachelagras.
[Användarvariabeln](#user-variable) | Endast för internt bruk.

 
## <a name="url-features"></a>URL-funktioner

Dessa funktioner kan en begäran om att omdirigeras eller har skrivits till en annan URL.

Namn | Syfte
-----|--------
[Följ omdirigeringar](#follow-redirects) | Anger om begäranden kan omdirigeras till värdnamn som definierats i Location-huvudet som returneras av en kund ursprungsservern.
[URL-omdirigering](#url-redirect) | Omdirigerar förfrågningar via Location-huvudet.
[URL-omskrivning](#url-rewrite)  | Skriver om fråge-URL.



## <a name="azure-cdn-rules-engine-features-reference"></a>Azure CDN-regelmotor – referens för funktioner

---
### <a name="age-response-header"></a>Ålder-svarshuvud
**Syfte**: avgör om en ålder svarshuvud ska tas med i svaret skickas till den som begär.
Värde|Resultat
--|--
Enabled | Rubriken ålder ingår i svar som skickas till den som begär.
Disabled | Rubriken ålder är undantagen från de svar som skickas till den som begär.

**Standardbeteende**: inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>Parametrar för bandbredd
**Syfte:** avgör om bandbredd begränsning parametrarna (till exempel ec_rate och ec_prebuf) är aktiva.

Strypning parametrar för bandbredd avgöra om dataöverföringshastighet för en klientbegäran är begränsad till en anpassad taxa.

Värde|Resultat
--|--
Enabled|Tillåter POP: erna måste respektera bandbreddsbegränsning begäranden.
Disabled|Leder till POP: erna att ignorera bandbreddsbegränsning parametrar. Det begärda innehållet visas normalt (det vill säga utan begränsning av bandbredd).

**Standardbeteendet:** aktiverat.
 
[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Bandbreddsbegränsning
**Syfte:** begränsar bandbredd för svar som tillhandahålls av POP: erna.

Båda av följande alternativ måste definieras för att korrekt ställa in bandbreddsbegränsning.

Alternativ|Beskrivning
--|--
KB per sekund|Ange det här alternativet för att maximal bandbredd (Kb per sekund) som kan användas för att leverera svaret.
Prebuf sekunder|Ange detta alternativ för hur många sekunder för POP: erna ska vänta tills bandbredd är begränsad. Syftet med den här tidsperioden obegränsad bandbredd är att förhindra att en mediaspelare problem hackigt eller buffring av bandbreddsbegränsning.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Kringgå Cache
**Syfte:** avgör om begäran bör kringgå cachelagring.

Värde|Resultat
--|--
Enabled|Kommer alla begäranden till faller mellan till den ursprungliga servern, även om innehållet har tidigare cachelagrats på POP.
Disabled|Gör POP till cache tillgångar enligt cache-principer som definierats i dess svarshuvuden.

**Standardbeteende:**

- **HTTP stora:** inaktiverad

<!---
- **ADN:** Enabled

--->

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Komma HTTP-metoder
**Syfte:** anger den ytterligare HTTP-metoder som kan cachelagras i nätverket.

Viktig information:

- Den här funktionen förutsätter att GET-svar alltid ska cachelagras. Hämta HTTP-metoden ska därför inte inkluderas när du ställer in den här funktionen.
- Den här funktionen stöder bara efter HTTP-metoden. Aktivera cachelagring av INLÄGGET svar genom att ställa in den här funktionen `POST`.
- Som standard cachelagras endast begäranden vars brödtexten är mindre än 14 Kb. Använda funktionen komma begäran för brödtext storlek för att ange textstorleken tillåtna.

**Standardbeteendet:** endast GET-svar cachelagras.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Textstorleken för komma begäran
**Syfte:** definierar tröskelvärdet för att fastställa om ett INLÄGG svar kan cachelagras.

Det här tröskelvärdet bestäms genom att ange högsta brödtext storlek. Förfrågningar som innehåller en större begärandetext cachelagras inte.

Viktig information:

- Den här funktionen gäller endast när efter svar är berättigade för cachelagring. Använda funktionen komma http-metoder för att aktivera cachelagring av POST-begäran.
- Begärandetexten beaktas för:
    - x--www-form-urlencoded värden
    - Att säkerställa en unik cache-nyckel
- Definiera en stor maximala begäran textstorleken kan påverka prestanda för leverans av data.
    - **Rekommenderat värde:** 14 Kb
    - **Minsta värde:** 1 Kb

**Standardbeteendet:** 14 Kb

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Cache-Control-huvud behandling
**Syfte:** styr generering av `Cache-Control` rubriker av POP när den externa Max-Age-funktionen är aktiv.

Det enklaste sättet att uppnå den här typen av konfiguration är att placera den externa Max-Age och behandling av Cache-Control-huvud-funktioner i samma instruktion.

Värde|Resultat
--|--
Skriv över|Garanterar att det utförs följande åtgärder:<br/> -Skriver över den `Cache-Control` rubrik som genererats av den ursprungliga servern. <br/>-Lägger till den `Cache-Control` rubrik som genereras av funktionen externa Max-Age till svaret.
Genomströmning|Säkerställer att den `Cache-Control` rubrik som produceras av externa Max-Age-funktionen aldrig har lagts till i svaret. <br/> Om ursprungsservern producerar en `Cache-Control` rubrik som är den genomgår för slutanvändaren. <br/> Om ursprungsservern inte ger en `Cache-Control` rubrik och sedan det här alternativet kan orsaka svarshuvudet ska inte innehålla en `Cache-Control` rubrik.
Lägg till om det saknas|Om en `Cache-Control` rubrik togs inte emot från den ursprungliga servern och sedan det här alternativet lägger till den `Cache-Control` rubrik som produceras av externa Max-Age-funktionen. Det här alternativet är användbart för att säkerställa att alla resurser är tilldelade en `Cache-Control` rubrik.
Ta bort| Det här alternativet ser till att en `Cache-Control` rubrik ingår inte i sidhuvud-svaret. Om en `Cache-Control` huvud har redan tilldelats och sedan tas den bort från svaret som rubrik.

**Standardbeteendet:** skrivs över.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Cachenyckel frågesträng
**Syfte:** avgör om cachenyckel innehåller eller utesluter parametrar för frågesträngen som är associerade med en begäran.

Viktig information:

- Ange en eller flera parameternamn för frågan sträng och avgränsa varje parameternamn med ett enda blanksteg.
- Den här funktionen anger om parametrar för frågesträngen är inkluderas eller uteslutas från cache-nyckeln. Ytterligare information har angetts för varje alternativ i följande tabell.

Typ|Beskrivning
--|--
 Ta med|  Anger att varje angiven parameter ska ingå i cache-nyckeln. En unik cache-nyckel genereras för varje begäran som innehåller ett unikt värde för en frågesträngsparameter som definierats i den här funktionen. 
 Omfatta alla  |Anger att en unik cache-nyckel har skapats för varje begäran till en tillgång som innehåller en unik frågesträng. Den här typen av konfiguration rekommenderas vanligtvis inte eftersom det kan leda till en liten andel av cacheträffar. Ett lågt antal cacheträffar ökar belastningen på den ursprungliga servern, eftersom den måste hantera fler begäranden. Den här konfigurationen duplicerar ett cachelagringsfunktionssätt som kallas ”unika-cache” på sidan cachelagring av frågesträng. 
 Uteslut | Anger att endast de angivna parametrarna är exkluderad från cache-nyckeln. Alla andra parametrar för frågesträngen som ingår i cache-nyckeln. 
 Undanta alla  |Anger att alla parametrar för frågesträngen undantas från cache-nyckeln. Den här konfigurationen dubbletter ”standard-cache” standardinställningarna för cachelagring beteende på sidan cachelagring av frågesträng.  

Regelmotorn kan du anpassa det sätt som cachelagring av frågesträngar i fråga har implementerats. Du kan till exempel ange att cachelagring av frågesträngar i frågan utförs endast på vissa platser eller filtyper.

Om du vill duplicera ”no-cache” cachelagring av frågesträngar beteende på sidan cachelagring av frågesträng, skapa en regel som innehåller ett matchningsvillkor för URL: en fråga med jokertecken och en kringgå Cache-funktion. Ange URL: en fråga med jokertecken matchar villkoret en asterisk (*).

>[!IMPORTANT] 
> Om token auktorisering har aktiverats för valfri sökväg på det här kontot, är standard-cache-läge det enda läge som kan användas för cachelagring av frågesträngar i fråga. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Exempelscenarier

Följande exempel för den här funktionen tillhandahåller en exempelförfrågan och standard-cachenyckel:

- **Exempel på begäran:** http://wpc.0001.&lt; Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234 och språk = sv & userid = 01
- **Standard cachenyckel:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Ta med

Exempel på konfiguration:

- **Typ:** inkluderar
- **Parametrar:** språk

Den här typen av konfiguration skulle generera följande sträng parametern cache-Frågenyckeln:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Omfatta alla

Exempel på konfiguration:

- **Typ:** omfattar alla

Den här typen av konfiguration skulle generera följande sträng parametern cache-Frågenyckeln:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Uteslut

Exempel på konfiguration:

- **Typ:** undanta
- **Parametrar:** sessionid användar-ID

Den här typen av konfiguration skulle generera följande sträng parametern cache-Frågenyckeln:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Undanta alla

Exempel på konfiguration:

- **Typ:** utesluta alla

Den här typen av konfiguration skulle generera följande sträng parametern cache-Frågenyckeln:

    /800001/Origin/folder/asset.htm

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Cachenyckel omskrivning
**Syfte:** skriver om cache-nyckeln som associeras med en begäran.

En cachenyckel är den relativa sökvägen som identifierar en tillgång för cachelagring. Med andra ord söka servrarna efter en cachelagrad version av en tillgång enligt dess sökväg som definieras av dess cachenyckel.

Konfigurera den här funktionen genom att definiera båda av följande alternativ:

Alternativ|Beskrivning
--|--
Ursprungliga sökväg| Definiera den relativa sökvägen till typerna av begäranden vars cachenyckel skrivs om. En relativ sökväg kan definieras genom att välja en grundläggande originalsökväg och sedan definiera ett mönster för reguljärt uttryck.
Ny sökväg|Definiera den relativa sökvägen för den nya cache-nyckeln. En relativ sökväg kan definieras genom att välja en grundläggande originalsökväg och sedan definiera ett mönster för reguljärt uttryck. Den här relativa sökvägen kan skapas dynamiskt med [HTTP variabler](cdn-http-variables.md).
**Standardbeteendet:** en begäran cachenyckel bestäms av begärande-URI.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Kommentar
**Syfte:** gör en anteckning som ska läggas till i en regel.

Ett sätt att använda för den här funktionen är att ge ytterligare information om generell användning av en regel eller varför en viss matchningsvillkor eller funktionen har lagts till i regeln.

Viktig information:

- Högst 150 tecken kan anges.
- Använd endast alfanumeriska tecken.
- Den här funktionen påverkar inte beteendet för regeln. Det är bara avsedd att tillhandahålla ett område där du kan ange information för framtida bruk eller som kan hjälpa att när du felsöker regeln.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Slutför Cache fyllning
**Syfte:** anger vad som händer när en begäran resulterar i en partiell cachemiss på en POP.

En partiell cachemiss beskriver Cachestatus för en tillgång som inte helt har laddats ned till en POP. Om en tillgång cachelagras endast delvis på POP och sedan vidarebefordras nästa begäran om tillgången igen till den ursprungliga servern.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
En partiell cachemiss inträffar vanligtvis när en användare avbryter en nedladdning eller för tillgångar som enbart har begärts med hjälp av HTTP-begäranden för intervallet. Den här funktionen fungerar bäst för stora tillgångar som vanligtvis inte hämtas från början till slut (t.ex, videor). Därför kan aktiveras den här funktionen som standard på stora HTTP-plattformen. Den är inaktiverad på alla plattformar.

Behåll standardkonfigurationen för stor HTTP-plattformen, eftersom den minskar belastningen på ursprungsservern kund och ökar hastigheten som dina kunder hämta ditt innehåll.

Värde|Resultat
--|--
Enabled|Återställer standardbeteendet. Standardinställningen är att tvinga POP för att initiera en bakgrundshämtning tillgångens från den ursprungliga servern. Därefter, tillgången kommer att finnas i lokalt cacheminne för den POP-plats.
Disabled|Förhindrar att en POP utför en bakgrundshämtning för tillgången. Resultatet är att en POP begär från ursprungsservern kund gör att nästa begäran om tillgången från den regionen.

**Standardbeteendet:** aktiverat.

#### <a name="compatibility"></a>Efterlevnad
Den här funktionen kan inte associeras med följande matchningsvillkor på grund av det sätt som i vilket cacheminne inställningar spåras: 
- AS-nummer
- IP-adress för klient
- Cookie-Parameter
- Cookie parametern Regex
- Land/region
- Enhet
- Edge Cname
- Refererande domän
- Begära huvud Literal
- Begära huvud Regex
- Begära huvud med jokertecken
- Begärandemetod
- Begäran-schema
- URL: en fråga Literal
- URL: en fråga Regex
- URL: en fråga med jokertecken
- URL: en frågeparameter

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Komprimera filtyper
**Syfte:** definierar filformat för filer som är komprimerade på servern.

Ett filformat som kan anges med hjälp av dess Internet medietyp (till exempel Content-Type). Medietypen för Internet är plattformsoberoende metadata som gör att servrarna som ska identifiera formatet för en viss tillgång. En lista över vanliga Internet-medietyper finns nedan.

Internet-medietyp|Beskrivning
--|--
text/plain|Filer med oformaterad text
text/html| HTML-filer
text/css|Cascading Style ark (CSS)
Application/x-javascript|Javascript
program-och javascript|Javascript
Viktig information:

- Ange flera Internet-medietyper genom att avgränsa dem med ett enda blanksteg. 
- Den här funktionen komprimerar bara tillgångar vars storlek är mindre än 1 MB. Större tillgångar komprimeras inte av servrar.
- Vissa typer av innehåll som bilder, video och ljud medietillgångar (till exempel JPG, MP3, MP4 o.s.v.), redan har komprimerats. Eftersom ytterligare komprimering på dessa typer av tillgångar inte försämras avsevärt, filstorlek, rekommenderas att du inte aktiverar komprimering på dem.
- Jokertecken, till exempel asterisker stöds inte.
- Se till att du ställer in alternativet komprimering inaktiverat på sidan komprimering för den plattform som den här regeln används innan du lägger till den här funktionen på en regel.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>Den anpassade loggen fält 1
**Syfte:** anger formatet och det innehåll som ska tilldelas till den anpassade loggfältet i en rå loggfil.

Den här anpassade fält kan du avgöra vilka begäranden och svar huvudvärden lagras i loggfilerna.

Som standard kallas den anpassade loggfältet ”x-ec_custom-1”. Namnet på det här fältet kan anpassas från sidan Raw Logginställningar.

Format för att ange sidhuvuden för begäran och svar definieras enligt följande:

Rubriktyp|Format|Exempel
-|-|-
Begärandehuvud|`%{[RequestHeader]()}[i]()` | % {Acceptera-Encoding} jag <br/> {Referer} jag <br/> % {Auktorisering} i
Svarshuvud|`%{[ResponseHeader]()}[o]()`| % {Ålder} o <br/> % {Content-Type}-o <br/> % {Cookie} o

Viktig information:

- En anpassad logg-fältet kan innehålla en kombination av namn på huvudfält och oformaterad text.
- Giltiga tecken för det här fältet är följande: alfanumeriska (0-9, a – z och A-Z), bindestreck, kolon, semikolon, apostrofer, kommatecken, punkter, understreck, likhetstecknen, parenteser, hakparenteser och blanksteg. Procentandel symbolen och klammerparenteser tillåts endast när används för att ange ett huvud-fält.
- Stavningen för varje fält för angivna huvudet måste matcha önskat begäran/svar-huvudnamn.
- Om du vill ange flera huvuden kan du använda en avgränsare för att ange varje sidhuvud. Du kan till exempel använda en förkortning för varje sidhuvud:
    - AE: % {acceptera-Encoding} i % s: {auktorisering} i CT: % {Content-Type}-o 

**Standardvärde:** -

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Felsöka Cache-svarshuvuden
**Syfte:** avgör om ett svar kan innehålla [X-EG-Debug svarshuvuden](cdn-http-debug-headers.md), som innehåller information om cache-principen för den begärda tillgången.

Felsöka cachesvar rubriker ska tas med i svaret när båda av följande stämmer:

- Felsöka svarshuvuden för Cache-funktionen har aktiverats på den angivna begäran.
- Den angivna begäran definierar de svarshuvuden för debug cache som kommer att inkluderas i svaret.

Felsöka cachesvar huvuden kan begäras genom att inkludera följande huvud och de angivna direktiv i begäran:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Exempel:**

X-EG-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Värde|Resultat
-|-
Enabled|Begäranden för debug cache-svarshuvuden returnerar ett svar som innehåller rubriken X-EG-Debug.
Disabled|Rubriken X-EG-Debug kommer att uteslutas från svaret.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Standard interna maxåldern
**Syfte:** anger max-age standardintervallet för POP till ursprunget server cache omverifieringen. Med andra ord, hur lång tid som ska förflyta innan en POP kontrollerar om en cachelagrad tillgång matchar den tillgång som lagras på den ursprungliga servern.

Viktig information:

- Den här åtgärden tar bara för svar från en ursprungsserver som inte tilldelar en maximal ålder indikation i den `Cache-Control` eller `Expires` rubrik.
- Den här åtgärden kommer inte ske för tillgångar som inte bedöms som komma.
- Den här åtgärden påverkar inte webbläsaren till POP cache revalidations. Dessa typer av revalidations bestäms av den `Cache-Control` eller `Expires` -huvuden som skickas till webbläsaren, som kan anpassas med externa Max-Age-funktionen.
- Resultatet av den här åtgärden har inte synliga påverkar svarshuvuden och det innehåll som returneras från POP för ditt innehåll, men det kan påverka mängden omverifieringen trafik som skickas från POP till ursprungsservern.
- Konfigurera den här funktionen genom att:
    - Att välja den statuskod som en standard interna maxåldern kan tillämpas.
    - Ange ett heltalsvärde och sedan välja den önskade tidsenheten (till exempel sekunder, minuter, timmar, osv.). Det här värdet anger interna maxåldern standardintervallet.

- Ange tidsenhet på ”av” tilldelar en intern maxåldern standardintervallet 7 dagar för begäranden som inte har tilldelats en maximal ålder indikation i sina `Cache-Control` eller `Expires` rubrik.

**Standardvärde:** 7 dagar

#### <a name="compatibility"></a>Efterlevnad
Den här funktionen kan inte associeras med följande matchningsvillkor på grund av det sätt som i vilket cacheminne inställningar spåras: 
- AS-nummer
- IP-adress för klient
- Cookie-Parameter
- Cookie parametern Regex
- Land/region
- Enhet
- Edge Cname
- Refererande domän
- Begära huvud Literal
- Begära huvud Regex
- Begära huvud med jokertecken
- Begärandemetod
- Begäran-schema
- URL: en fråga Literal
- URL: en fråga Regex
- URL: en fråga med jokertecken
- URL: en frågeparameter

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Neka åtkomst (403)
**Syfte**: Anger om alla begäranden avvisas med ett 403 Forbidden-svar.

Värde | Resultat
------|-------
Enabled| Kommer alla begäranden som uppfyller de matchande kriterierna avvisas med ett 403 Forbidden-svar.
Disabled| Återställer standardbeteendet. Standardinställningen är att den ursprungliga servern att avgöra vilken typ av svar som ska returneras.

**Standardbeteende**: inaktiverad

> [!TIP]
   > En möjlig användning för den här funktionen är att associera det med ett matchningsvillkor för begära huvud för att blockera åtkomsten till HTTP-referenter som använder infogade länkar till ditt innehåll.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Upphör att gälla rubrik behandling
**Syfte:** styr generering av `Expires` huvuden genom en POP när externa Max-Age-funktionen är aktiv.

Det enklaste sättet att uppnå den här typen av konfiguration är att placera externa Max-Age och funktioner som upphör att gälla rubrik behandling i samma instruktion.

Värde|Resultat
--|--
Skriv över|Ser till att följande åtgärder utförs:<br/>-Skriver över den `Expires` rubrik som genererats av den ursprungliga servern.<br/>-Lägger till den `Expires` rubrik som genereras av funktionen externa Max-Age till svaret.
Genomströmning|Säkerställer att den `Expires` rubrik som produceras av externa Max-Age-funktionen aldrig har lagts till i svaret. <br/> Om ursprungsservern producerar en `Expires` rubrik, egenskapsrutan till slutanvändaren. <br/>Om ursprungsservern inte ger en `Expires` rubrik och sedan det här alternativet kan orsaka svarshuvudet ska inte innehålla en `Expires` rubrik.
Lägg till om det saknas| Om en `Expires` rubrik togs inte emot från den ursprungliga servern och sedan det här alternativet lägger till den `Expires` rubrik som produceras av externa Max-Age-funktionen. Det här alternativet är användbart för att säkerställa att alla tillgångar kommer att tilldelas en `Expires` rubrik.
Ta bort| Säkerställer att en `Expires` rubrik ingår inte i sidhuvud-svaret. Om en `Expires` huvud har redan tilldelats och sedan tas den bort från svaret som rubrik.

**Standardbeteendet:** skriva över

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Externa maxåldern
**Syfte:** bestämmer maxåldern intervallet för webbläsaren till POP cache omverifieringen. Med andra ord, hur lång tid som ska förflyta innan en webbläsare kan söka efter en ny version av en tillgång från en POP.

Den här funktionen aktiveras genererar `Cache-Control: max-age` och `Expires` rubriker från POP: erna och skicka dem till HTTP-klienten. Som standard skrivs dessa huvuden dessa rubriker som skapats av den ursprungliga servern. Cache-Control-huvud behandling och funktioner som upphör att gälla rubrik behandling kan dock användas för beteendet.

Viktig information:

- Den här åtgärden påverkar inte POP till ursprunget server cache revalidations. Dessa typer av revalidations bestäms av den `Cache-Control` och `Expires` rubriker togs emot från den ursprungliga servern och kan anpassas med standard interna Max-Age och kraft interna Max-Age-funktioner.
- Konfigurera den här funktionen genom att ange ett heltalsvärde och markera den önskade tidsenheten (till exempel sekunder, minuter, timmar, osv.).
- Ange den här funktionen till ett negativt värde leder till POP: erna att skicka en `Cache-Control: no-cache` och en `Expires` tid som anges i förflutna med varje svarsinnehåll till webbläsaren. Även om HTTP-klienter inte kommer cachelagrar svaret, påverkar inte den här inställningen på POP möjligheten att cachelagra svaret från den ursprungliga servern.
- Ange tidsenhet på ”av” inaktiverar den här funktionen. Den `Cache-Control` och `Expires` rubriker som cachelagras med svaret på den ursprungliga servern passerar genom till webbläsaren.

**Standardbeteendet:** av

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Följ omdirigeringar
**Syfte:** avgör om begäranden kan omdirigeras till värdnamn som definierats i Location-huvudet som returneras av en kund ursprungsservern.

Viktig information:

- Begäranden kan endast omdirigeras till edge CNAME-poster för samma plattform.

Värde|Resultat
-|-
Enabled|Begäranden kan omdirigeras.
Disabled|Begäranden dirigeras inte.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Tvinga interna maxåldern
**Syfte:** anger max-age tidsintervallet för POP till ursprunget server cache omverifieringen. Med andra ord, hur lång tid som ska förflyta innan en POP kan kontrollera om en cachelagrad tillgång matchar den tillgång som lagras på den ursprungliga servern.

Viktig information:

- Den här funktionen åsidosätts maxåldern tider som anges i `Cache-Control` eller `Expires` rubriker som genereras från en ursprungsservern.
- Den här funktionen påverkar inte webbläsaren till POP cache revalidations. Dessa typer av revalidations bestäms av den `Cache-Control` eller `Expires` -huvuden som skickas till webbläsaren.
- Den här funktionen har inte en synliga effekt på ett svar som levereras av en POP till frågeställaren. Men kan det påverka mängden omverifieringen trafik som skickas från POP: erna till ursprungsservern.
- Konfigurera den här funktionen genom att:
    - Att välja statuskoden som en intern maxåldern kommer att tillämpas.
    - Ange ett heltalsvärde och markera den önskade tidsenheten (till exempel sekunder, minuter, timmar, osv.). Det här värdet anger begärandets maxåldern intervall.

- Tidsenhet på ”av” inaktiveras den här funktionen. Ett internt max-age-intervall ska inte tilldelas begärda tillgångar. Om det ursprungliga huvudet inte innehåller instruktioner för cachelagring, cachelagras tillgången enligt inställningen active i standard interna Max-Age-funktionen.

**Standardbeteendet:** av

#### <a name="compatibility"></a>Efterlevnad
Den här funktionen kan inte associeras med följande matchningsvillkor på grund av det sätt som i vilket cacheminne inställningar spåras: 
- AS-nummer
- IP-adress för klient
- Cookie-Parameter
- Cookie parametern Regex
- Land/region
- Enhet
- Edge Cname
- Refererande domän
- Begära huvud Literal
- Begära huvud Regex
- Begära huvud med jokertecken
- Begärandemetod
- Begäran-schema
- URL: en fråga Literal
- URL: en fråga Regex
- URL: en fråga med jokertecken
- URL: en frågeparameter

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>H.264-stöd (HTTP progressiv nedladdning)
**Syfte:** avgör vilka typer av H.264-filformat som kan användas för att strömma innehåll.

Viktig information:

- Definiera en blankstegsavgränsad tillåtna H.264-filnamnstillägg i alternativet filnamnstillägg. Alternativet filnamnstillägg åsidosätter standardbeteendet. Underhålla MP4 och F4V support genom att inkludera dessa filnamnstillägg när det här alternativet. 
- Inkludera en period när du anger varje filnamnstillägg (till exempel _.mp4_, _.f4v_).

**Standardbeteendet:** HTTP progressiv nedladdning stöder MP4 och F4V media som standard.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>Begäran om äran No-Cache
**Syfte:** avgör om en HTTP-klient är no-cache begäranden ska vidarebefordras till den ursprungliga servern.

En begäran om no-cache inträffar när HTTP-klienten skickar en `Cache-Control: no-cache` och/eller `Pragma: no-cache` rubriken i HTTP-begäran.

Värde|Resultat
--|--
Enabled|Ger en HTTP-klient no-cache-begäranden ska vidarebefordras till den ursprungliga servern, och ursprungsservern returnerar svarsrubriker och brödtext via POP tillbaka till HTTP-klienten.
Disabled|Återställer standardbeteendet. Standardinställningen är att förhindra att Nej cachebegäranden kan vidarebefordras till den ursprungliga servern.

För all produktionstrafik rekommenderas att lämna den här funktionen i inaktiverat standardtillståndet. Ursprungsservrar kommer annars inte att skärma från slutanvändare som kan oavsiktligt utlösa många no-cache-begäranden när du uppdaterar webbsidor eller från många populära mediespelare som är kodade för att skicka ett no-cache-huvud med varje video begäran. Den här funktionen kan dock vara praktiskt att tillämpa vissa icke-produktion mellanlagring eller testa kataloger, för att tillåta att nytt innehåll hämtas på begäran från den ursprungliga servern.

Cachestatus som rapporteras för en begäran som kan vidarebefordras till en ursprungsserver på grund av den här funktionen är `TCP_Client_Refresh_Miss`. Rapporten status för cachelagring, som är tillgänglig i Core reporting modulen, visar statistisk information Cachestatus. Den här rapporten kan du spåra antalet och procentandelen förfrågningar som vidarebefordras till en ursprungsserver på grund av den här funktionen.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Ignorera ursprung No-Cache
**Syfte:** avgör om CDN ignorerar följande direktiv hanteras från en ursprungsserver:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Viktig information:

- Konfigurera den här funktionen genom att definiera en blankstegsavgränsad lista över statuskoder som ovan direktiv kommer att ignoreras.
- Uppsättning giltiga statuskoder för den här funktionen: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, och 505.
- Inaktivera den här funktionen genom att ange den till ett tomt värde.

**Standardbeteendet:** standardbeteendet är att respektera ovannämnda direktiv.

#### <a name="compatibility"></a>Efterlevnad
Den här funktionen kan inte associeras med följande matchningsvillkor på grund av det sätt som i vilket cacheminne inställningar spåras: 
- AS-nummer
- IP-adress för klient
- Cookie-Parameter
- Cookie parametern Regex
- Land/region
- Enhet
- Edge Cname
- Refererande domän
- Begära huvud Literal
- Begära huvud Regex
- Begära huvud med jokertecken
- Begärandemetod
- Begäran-schema
- URL: en fråga Literal
- URL: en fråga Regex
- URL: en fråga med jokertecken
- URL: en frågeparameter

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Ignorera Unsatisfiable intervall 
**Syfte:** bestämmer svaret som returneras till klienter när en förfrågan genererar statuskod 416 begärda intervallet inte Satisfiable.

Som standard är den här Statuskoden returneras när angivna byte-intervall begäran inte uppfylls av en POP och ett If-Range-begäran header-fält angavs inte.

Värde|Resultat
-|-
Enabled|Förhindrar att svara på en begäran om ogiltig byte-intervall med en 416 begärda intervallet inte Satisfiable statuskod POP: erna. Servrarna ska i stället leverera den begärda tillgången och returnera ett 200 OK till klienten.
Disabled|Återställer standardbeteendet. Standardinställningen är att respektera 416 begärda intervallet inte Satisfiable statuskoden.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Intern Max-inaktuell
**Syfte:** kontrollerar hur länge förbi den normala upphör att gälla en cachelagrad tillgång kan hanteras från en POP när POP inte kan verifiera den cachelagrade tillgången med den ursprungliga servern.

Vanligtvis när en tillgång maxåldern tiden går ut skickar POP en begäran om förnyelse till den ursprungliga servern. Den ursprungliga servern visas sedan svara med antingen 304 inte ändras för att ge den POP-plats till en ny låna ut för cachelagrade tillgång eller med 200 OK för att tillhandahålla den POP-plats med en uppdaterad version av den cachelagra tillgången.

Om POP kan inte upprätta en anslutning med den ursprungliga servern vid försök omverifieringen, och sedan på den här interna Max-föråldrade funktionen styr om och hur länge, kan POP fortsätta att fungera nu föråldrade tillgången.

Observera att det här tidsintervallet startar när tillgångens maxåldern upphör inte när den misslyckade omverifieringen inträffar. Därför är den längsta tid under vilken en tillgång kan hanteras utan lyckad omverifieringen mängden tid som anges av kombinationen av maxåldern plus max inaktuell. Till exempel om en tillgång cachelagrades kl. 09:00 med en maximal ålder på 30 minuter och max-inaktuell högst 15 minuter, skulle sedan en misslyckad omverifieringen försöket 9:44 leda till en användare ta emot inaktuella cachelagrade tillgången, medan en misslyckad omverifieringen försöket 9:46 skulle resultera i en d användaren får en 504 Gateway-Timeout.

Ett värde som konfigurerats för den här funktionen har ersatts av `Cache-Control: must-revalidate` eller `Cache-Control: proxy-revalidate` rubriker som togs emot från den ursprungliga servern. Om någon av dessa huvuden tas emot från den ursprungliga servern när en tillgång cachelagras först, sedan fungerar POP inte en inaktuella cachelagrade tillgång. I sådana fall returnerar POP ett 504 Gateway-Timeout-fel om POP inte kan verifiera med ursprunget när tillgångens maxåldern intervall har upphört att gälla.

Viktig information:

- Konfigurera den här funktionen genom att:
    - Att välja statuskoden som en max-inaktuell kommer att tillämpas.
    - Ange ett heltalsvärde och sedan välja den önskade tidsenheten (till exempel sekunder, minuter, timmar, osv.). Det här värdet anger den interna max-inaktuell som kommer att tillämpas.

- Ange tidsenhet på ”av” inaktiverar den här funktionen. En cachelagrad tillgång hanteras inte utöver dess normala upphör att gälla.

**Standardbeteendet:** två minuter

#### <a name="compatibility"></a>Efterlevnad
Den här funktionen kan inte associeras med följande matchningsvillkor på grund av det sätt som i vilket cacheminne inställningar spåras: 
- AS-nummer
- IP-adress för klient
- Cookie-Parameter
- Cookie parametern Regex
- Land/region
- Enhet
- Edge Cname
- Refererande domän
- Begära huvud Literal
- Begära huvud Regex
- Begära huvud med jokertecken
- Begärandemetod
- Begäran-schema
- URL: en fråga Literal
- URL: en fråga Regex
- URL: en fråga med jokertecken
- URL: en frågeparameter

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Log frågesträng
**Syfte:** avgör om en frågesträng kommer att lagras tillsammans med URL: en i åtkomstloggar.

Värde|Resultat
-|-
Enabled|Tillåter lagring av frågesträngar när du registrerar URL: er i en åtkomstlogg. Om en URL inte innehåller en frågesträng, har en effekt inte i det här alternativet.
Disabled|Återställer standardbeteendet. Standardinställningen är att ignorera frågesträngar när du registrerar URL: er i en åtkomstlogg.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Maximalt antal Keep-Alive förfrågningar
**Syfte:** definierar det maximala antalet förfrågningar för Keep-Alive-anslutningen innan den är stängd.

Ange det maximala antalet förfrågningar till ett lågt värde inte och kan resultera i försämrade prestanda.

Viktig information:

- Ange det här värdet som ett hela heltal.
- Inkludera inte kommatecken eller punkter i det angivna värdet.

**Standardvärde:** 10 000 begäranden

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Ändra begärandehuvud i klienten
**Syfte:** varje begäran innehåller en uppsättning begärandehuvuden som beskriver den. Den här funktionen kan antingen:

- Lägg till eller skriva över värdet som tilldelas en rubrik för begäran. Om det angivna begärandehuvudet inte finns, sedan den här funktionen till det på begäran.
- Ta bort ett förfrågnings-huvud i begäran.

Begäranden som vidarebefordras till en ursprungsserver avspeglar ändringar som gjorts av den här funktionen.

En av följande åtgärder kan utföras på en rubrik för begäran:

Alternativ|Beskrivning|Exempel
-|-|-
Lägg till|Det angivna värdet läggs till i slutet av den befintliga begäran huvudvärde.|**Begär huvudvärde (klient):**<br/>Value1<br/>**Begär huvudvärde (regelmotor):**<br/>Value2 <br/>**Ny begäran huvudvärde:** <br/>Value1Value2
Skriv över|Begäran huvudets värde sätts till det angivna värdet.|**Begär huvudvärde (klient):**<br/>Value1<br/>**Begär huvudvärde (regelmotor):**<br/>Value2<br/>**Ny begäran huvudvärde:**<br/> Value2 <br/>
Ta bort|Tar bort det angivna begärandehuvudet.|**Begär huvudvärde (klient):**<br/>Value1<br/>**Ändra klientkonfigurationen begäran-huvud:**<br/>Ta bort rubriken i fråga.<br/>**Resultat:**<br/>Det angivna begärandehuvudet kommer inte att vidarebefordras till den ursprungliga servern.

Viktig information:

- Kontrollera att värdet som anges i alternativet namn är en exakt matchning för önskad begäranshuvudet.
- Om beaktas inte i syfte att identifiera en rubrik. Till exempel något av följande variationer av den `Cache-Control` rubriknamn kan användas för att identifiera den:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- När du anger ett namn på sidhuvud, Använd endast alfanumeriska tecken, bindestreck eller understreck.
- Tar bort ett sidhuvud förhindras från att vidarebefordras till en ursprungsserver av POP-servrar.
- Följande huvuden är reserverat och kan inte ändras av den här funktionen:
    - vidarebefordrat
    - värd
    - via
    - Varning
    - x-vidarebefordrade-för
    - Alla huvud-namn som börjar med ”x-EG” är reserverade.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Ändra klienten svarshuvud
Varje svar innehåller en uppsättning svarshuvuden som beskriver den. Den här funktionen kan antingen:

- Lägg till eller skriva över värdet som tilldelas en svarshuvudet. Om det angivna svarshuvudet inte finns, sedan den här funktionen till det till svaret.
- Ta bort ett svarshuvud från svaret.

Som standard definieras svar huvudvärden genom en ursprungsserver och POP: erna.

En av följande åtgärder kan utföras på ett svarshuvud:

Alternativ|Beskrivning|Exempel
-|-|-
Lägg till|Det angivna värdet läggs till i slutet av befintliga svar huvudets värde.|**Svaret huvudvärde (klient):**<br />Value1<br/>**Svaret huvudvärde (regelmotor):**<br/>Value2<br/>**Nytt värde för svaret-huvud:**<br/>Value1Value2
Skriv över|Huvudvärde svar sätts till det angivna värdet.|**Svaret huvudvärde (klient):**<br/>Value1<br/>**Svaret huvudvärde (regelmotor):**<br/>Value2 <br/>**Nytt värde för svaret-huvud:**<br/>Value2 <br/>
Ta bort|Tar bort det angivna svarshuvudet.|**Svaret huvudvärde (klient):**<br/>Value1<br/>**Ändra klientkonfigurationen svar-huvud:**<br/>Ta bort rubriken i fråga.<br/>**Resultat:**<br/>Det angivna svarshuvudet kommer inte att vidarebefordras till den som begär.

Viktig information:

- Kontrollera att värdet som anges i alternativet namn är en exakt matchning för önskad svarshuvudet. 
- Om beaktas inte i syfte att identifiera en rubrik. Till exempel något av följande variationer av den `Cache-Control` rubriknamn kan användas för att identifiera den:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Tar bort ett sidhuvud kan den kan vidarebefordras till den som begär.
- Följande huvuden är reserverat och kan inte ändras av den här funktionen:
    - Acceptera-kodning
    - Ålder
    - anslutning
    - Innehållskodning
    - innehållslängd
    - innehåll-intervall
    - datum
    - server
    - trailer
    - Transfer-encoding
    - uppgradering
    - variera
    - via
    - Varning
    - Alla huvud-namn som börjar med ”x-EG” är reserverade.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Partiell Cache delning
**Syfte:** avgör om en begäran kan generera delvis cachelagrat innehåll.

Den här partiella cache kan sedan användas för att uppfylla nya begäranden för innehållet tills det begärda innehållet cachelagras fullständigt.

Värde|Resultat
-|-
Enabled|Begäranden kan generera delvis cachelagrat innehåll.
Disabled|Begäranden kan bara skapa ett fullständigt cachelagrade versionen av det begärda innehållet.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>Prevalidate cachelagrat innehåll
**Syfte:** avgör om cachelagrat innehåll ska vara berättigade tidig Omverifiering innan dess TTL upphör att gälla.

Definiera hur lång tid innan det begärda innehållet TTL då blir det kvalificerade tidig Omverifiering upphör att gälla.

Viktig information:

- Att välja ”Off” eftersom tidsenheten kräver omverifieringen efter det cachelagrade innehållet TTL har gått ut. Tid ska inte anges och ignoreras.

**Standardbeteendet:** av. Omverifieringen kan endast ske när det cachelagrade innehållet TTL har gått ut.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Proxy särskilda rubriker
**Syfte:** definierar en uppsättning [Verizon-specifika HTTP-begärans sidhuvud](cdn-verizon-http-headers.md) som vidarebefordras från en POP till en ursprungsservern.

Viktig information:

- Varje CDN-specifika begärandehuvudet som definierats i den här funktionen vidarebefordras till en ursprungsservern. Exkluderade rubriker vidarebefordras inte.
- Om du vill förhindra att en rubrik för CDN-specifika begäran vidarebefordras genom att ta bort den från blankstegsavgränsad lista fältet huvud lista.

Följande HTTP-huvuden som ingår i listan:
- Via
- X-vidarebefordrade-för
- X-vidarebefordrade-protokoll
- X-värden
- X-Midgress
- X-Gateway-List
- X-EG-Name
- Värd

**Standardbeteendet:** alla CDN-specifika begärandehuvuden vidarebefordras till den ursprungliga servern.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Uppdatera filer i Internetcachen noll Byte
**Syfte:** bestämmer hur ett HTTP-klientens begäran för en tillgång på 0 byte-cache hanteras av POP-servrar.

Giltiga värden är:

Värde|Resultat
--|--
Enabled|Gör POP återigen hämta tillgången från den ursprungliga servern.
Disabled|Återställer standardbeteendet. Standardinställningen är att leverera innehåll giltig cache tillgångar på begäran.
Den här funktionen krävs inte för rätt cachelagring och leverans av innehåll, men kan användas som en tillfällig lösning. Dynamiskt innehåll generatorer på ursprungsservrar kan exempelvis oavsiktligt resultera i 0 byte-svar som skickas till POP: erna. Dessa typer av svar cachelagras vanligtvis av POP-servrar. Om du vet att ett svar på 0 byte aldrig är ett giltigt svar 

för sådana innehåll, sedan den här funktionen kan förhindra att dessa typer av resurser som hanteras till dina klienter.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Ange komma statuskoder
**Syfte:** definierar de statuskoder som kan resultera i cachelagrat innehåll.

Som standard har cachelagring endast aktiverats för 200 OK-svar.

Definiera en blankstegsavgränsad önskade statuskoder.

Viktig information:

- Aktivera funktionen Ignorera ursprung No-Cache. Om den här funktionen inte är aktiverad kan inte 200 OK-svar cachelagras.
- Uppsättning giltiga statuskoder för den här funktionen: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, och 505.
- Den här funktionen kan inte användas för att inaktivera cachelagring för svar som genererar en 200 OK statuskod.

**Standardbeteendet:** cachelagring är bara aktiverat för svar som genererar en 200 OK statuskod.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Ange anpassade klientens IP-huvud
**Syfte:** lägger till en anpassad rubrik som identifierar den begärande klienten med IP-adress på begäran.

Alternativet huvud namn definierar namnet på den anpassade begärandehuvudet där klientens IP-adress lagras.

Den här funktionen kan en kund ursprungsservern att ta reda på klient-IP-adresser via ett anpassat huvud. Om begäran har behandlats från cacheminnet sedan informeras den ursprungliga servern inte om klientens IP-adress. Vi rekommenderar därför att den här funktionen kan användas med resurser som inte är cachelagrade.

Se till att den angivna huvudnamn inte matchar någon av följande namn:

- Standard begäran rubriknamn. En lista över standard rubriknamn finns i [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Reserverade rubriknamn:
    - vidarebefordras för
    - värd
    - variera
    - via
    - Varning
    - x-vidarebefordrade-för
    - Alla huvud-namn som börjar med ”x-EG” är reserverade.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Inaktuella leverans av innehåll vid fel
**Syfte:** avgör om innehåll som förfallit cachelagrade levereras när ett fel uppstår under cache omverifieringen eller vid hämtning av det begärda innehållet från den ursprungliga servern för kunden.

Värde|Resultat
-|-
Enabled|Inaktuella innehållet visas att beställaren när ett fel inträffar under en anslutning till en ursprungsservern.
Disabled|Den ursprungliga servern fel vidarebefordras till den som begär.

**Standardbeteendet:** inaktiverad

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Föråldrade vid Revalidate
**Syfte:** förbättrar prestanda genom att låta POP att fungera inaktuella innehåll till den som begär medan omverifieringen genomförs.

Viktig information:

- Den här funktionen fungerar beror enligt den valda enheten.
    - **Tidsenhet:** anger hur lång tid och väljer en tidsenhet (till exempel sekunder, minuter, timmar, etc.) så att inaktuella innehållsleverans. Den här typen av installation tillåter CDN för att utöka tidslängd som det kan leverera innehåll innan du kräver verifiering enligt följande formel: **TTL** + **inaktuella medan verifiera tid** 
    - **Av:** väljer ”av” att kräva omverifieringen innan en begäran för inaktuella innehåll kan betjänas.
        - Ange hur lång tid eftersom det är och kommer att ignoreras.

**Standardbeteendet:** av. Omverifieringen måste ske innan det begärda innehållet kan hanteras.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Token Auth
**Syfte:** avgör om tokenbaserad autentisering kommer att tillämpas på en begäran.

Om tokenbaserad autentisering är aktiverad, hanteras endast begäranden som tillhandahåller en krypterad token och uppfyller kraven som anges av den token.

Den krypteringsnyckel som används för att kryptera och dekryptera token värden bestäms av den primärnyckeln och säkerhetskopiering nyckel alternativen på sidan Token-autentisering. Tänk på att krypteringsnycklarna är plattformsspecifika.

**Standardbeteendet:** inaktiverad.

Den här funktionen har företräde framför de flesta funktioner med undantag av URL-Omskrivningsregler-funktionen.

Värde | Resultat
------|---------
Enabled | Skyddar det begärda innehållet med tokenbaserad autentisering. Att kommer användas endast begäranden från klienter som tillhandahåller en giltig token och uppfyller dess krav. FTP-transaktioner undantas från tokenbaserad autentisering.
Disabled| Återställer standardbeteendet. Standardinställningen är att konfigurationen tokenbaserad autentisering att avgöra om en begäran ska skyddas.

#### <a name="compatibility"></a>Efterlevnad
Använd inte Token Authentication med ett matchningsvillkor för alltid. 

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Token Auth DOS-kod
**Syfte:** avgör vilken typ av svar som ska returneras till en användare när en begäran nekas på grund av tokenbaserad autentisering.

I följande tabell visas tillgängliga svarskoder.

Svarskod|Svarsnamnet|Beskrivning
-------------|-------------|--------
301|Flyttas permanent|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i Location-huvudet.
302|Hittad|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i Location-huvudet. Den här statuskoden är bransch standardmetoden för att utföra en omdirigering.
307|Tillfällig omdirigering|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i Location-huvudet.
401|Behörighet saknas|Kombinera den här statuskoden med rubriken WWW-autentisering kan du be en användare för autentisering.
403|Förbjudna|Det här meddelandet är standard 403 Forbidden statusmeddelandet som en obehörig användare ser när de försöker komma åt skyddat innehåll.
404|Filen hittades inte|Den här statusen indikerar att HTTP-klienten kunde kommunicera med servern, men det gick inte att hitta det begärda innehållet.

#### <a name="compatibility"></a>Efterlevnad
Använd inte Tokenkod för Auth-datorn med ett matchningsvillkor för alltid. Använd i stället de **anpassad datorn för hantering av** i avsnittet den **Token Auth** för den **hantera** portal. Mer information finns i [skydda Azure CDN-resurser med tokenautentisering](cdn-token-auth.md).

#### <a name="url-redirection"></a>URL-omdirigering

Den här funktionen stöder URL-omdirigering till en användardefinierad URL när den är konfigurerad för att returnera statuskod 3xx. URL: en användardefinierad kan anges genom att utföra följande steg:

1. Välj en 3xx svarskod för funktionen Auth Denial Tokenkod.
2. Välj alternativet för valfritt namn för rubriken ”plats”.
3. Ange alternativet valfritt huvudvärde till önskad URL.

Om en URL inte har definierats för statuskoden 3xx, returneras sidan standardsvar för en 3xx statuskod till användaren.

URL-omdirigering kan bara användas för 3xx svarskoder.

Alternativet valfritt huvudvärde stöder alfanumeriska tecken, citattecken och blanksteg.

#### <a name="authentication"></a>Autentisering

Den här funktionen stöder möjligheten att ta med WWW-autentisera huvudet när svarar på en obehörig begäran om innehåll som skyddas av tokenbaserad autentisering. Om rubriken WWW-autentisera har ställts in till ”grundläggande” i din konfiguration, uppmanas obehörig användare för autentiseringsuppgifter.

Konfigurationen ovan kan uppnås genom att utföra följande steg:

1. Välj ”401” som funktionen Auth Denial Tokenkod svarskod.
2. Välj ”WWW-autentisera” alternativet valfritt rubriknamn.
3. Ange alternativet valfritt huvudvärde till ”basic”.

Autentisera WWW-rubriken gäller endast för 401 svarskoder.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Token Auth Ignorera skiftläge för URL: en
**Syfte:** avgör om URL-jämförelser som gjorts av tokenbaserad autentisering är skiftlägeskänsliga.

De parametrar som påverkas av den här funktionen är:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Giltiga värden är:

Värde|Resultat
---|----
Enabled|Gör POP för att Ignorera skiftläge när URL: er för tokenbaserad autentiseringsparametrar.
Disabled|Återställer standardbeteendet. Standardbeteendet är för URL: en jämförelser för autentisering med Enhetstoken ska vara skiftlägeskänslig.

**Standardbeteendet:** inaktiverad.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Parameter-token Auth
**Syfte:** avgör om frågesträngparametern för tokenbaserad autentisering ska ändras.

Viktig information:

- Alternativet värde definierar den parameternamn för frågesträng som en token kan anges.
- Alternativet värde kan inte anges till ”ec_token”.
- Kontrollera att namnet som definierats i alternativet värde innehåller endast giltiga URL-tecken.

Värde|Resultat
----|----
Enabled|Alternativet värde definierar den parameternamn för frågesträng som token ska definieras.
Disabled|En token kan anges som en odefinierad frågesträngsparameter i fråge-URL.

**Standardbeteendet:** inaktiverad. En token kan anges som en odefinierad frågesträngsparameter i fråge-URL.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>URL-omdirigering
**Syfte:** omdirigerar förfrågningar via Location-huvudet.

Konfigurationen för den här funktionen kräver att ange följande alternativ:

Alternativ|Beskrivning
-|-
Kod|Välj svarskoden som ska returneras till frågeställaren.
Käll & mönster| De här inställningarna definierar ett mönster för begäran-URI som identifierar typ av begäranden kan omdirigeras. Att kommer omdirigeras endast begäranden vars URL: en som uppfyller båda av följande kriterier: <br/> <br/> **Källa (eller innehåll åtkomstpunkt):** väljer en relativ sökväg som identifierar en ursprungsservern. Den här sökvägen är den _/XXXX/_ avsnittet och namnet på slutpunkten. <br/><br/> **Källa (mönster):** ett mönster som identifierar begäranden av relativ sökväg måste anges. Mönster för reguljärt uttryck måste ange en sökväg som börjar direkt efter den tidigare valda innehållsåtkomst peka (se ovan). <br/> – Se till att begäran URI villkoren (det vill säga käll & mönster) tidigare inte står i konflikt med alla matchningsvillkor som definierats för den här funktionen. <br/> -Ange ett mönster; Om du använder ett tomt värde som mönstret matchar alla strängar.
Mål| Ange URL: en som ovan begäranden omdirigeras. <br/><br/> Konstruera dynamiskt med den här URL: en: <br/> – Ett mönster för reguljärt uttryck <br/>- [HTTP-variabler](cdn-http-variables.md) <br/><br/> Byt ut värdena som hämtats i mönstret för källa till mål-mönster genom att använda $_n_ där _n_ identifierar ett värde i den ordning som den hade hämtats. Till exempel representerar $1 det första värdet som avbildas i mönstret för källan, medan $2 representerar det andra värdet. <br/> 
Vi rekommenderar starkt att använda en absolut URL. Användning av en relativ URL kan dirigera om CDN-URL: er till en ogiltig sökväg.

**Exempelscenario**

Det här exemplet visar hur du dirigerar om en kant CNAME-URL som motsvarar det här grundläggande URL: en för CDN: http:\//marketing.azureedge.net/brochures

Kvalificerade begäranden omdirigeras till den här grundläggande edge CNAME-URL: http:\//cdn.mydomain.com/resources

Den här URL-omdirigering kan uppnås genom följande konfiguration: ![URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Viktiga punkter:**

- Omdirigerings-URL-funktionen definierar begäran URL: er som kommer att omdirigeras. Därför krävs inga ytterligare matchningsvillkor. Även om det matchar villkoret har definierats som ”alltid”, omdirigeras begäranden som pekar på mappen ”broschyrer” på ”marknadsföring” kund ursprunget. 
- Alla matchande begäranden dirigeras till gränsen CNAME-URL som definierats i mål-alternativet. 
    - Exempelscenario #1: 
        - Exempel på begäran (CDN-URL): http:\//marketing.azureedge.net/brochures/widgets.pdf 
        - URL för begäran (efter omdirigering): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Exempelscenario #2: 
        - Exempel på begäran (Edge CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf 
        - URL för begäran (efter omdirigering): http:\//cdn.mydomain.com/resources/widgets.pdf
    - Exempelscenario #3: 
        - Exempel på begäran (Edge CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt 
        - URL för begäran (efter omdirigering): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- Begäran-schema (% {scheme}) variabeln utnyttjas i alternativet mål, vilket garanterar att begärandets schema förblir oförändrad efter omdirigering.
- URL-segment som hämtades från begäran läggs till på den nya URL: en via ”$1”.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>URL-omskrivning
**Syfte:** returpaket fråge-URL.

Viktig information:

- Konfigurationen för den här funktionen kräver att ange följande alternativ:

Alternativ|Beskrivning
-|-
 Käll & mönster | De här inställningarna definierar ett mönster för begäran-URI som identifierar typ av begäranden som kan skrivas. Endast begäranden vars URL: en som uppfyller båda av följande kriterier ska skrivas: <br/><br/>  - **Källa (eller innehåll åtkomstpunkt):** väljer en relativ sökväg som identifierar en ursprungsservern. Den här sökvägen är den _/XXXX/_ avsnittet och namnet på slutpunkten. <br/><br/> - **Källa (mönster):** ett mönster som identifierar begäranden av relativ sökväg måste anges. Mönster för reguljärt uttryck måste ange en sökväg som börjar direkt efter den tidigare valda innehållsåtkomst peka (se ovan). <br/> Kontrollera att begäran URI villkoren (det vill säga käll & mönster) tidigare inte står i konflikt med någon av matchningsvillkor som definierats för den här funktionen. Ange ett mönster; Om du använder ett tomt värde som mönstret matchar alla strängar. 
 Mål  |Definiera relativ URL som ovan begäranden ska skrivas med: <br/>    1. Att välja en plats för åtkomst till innehåll som identifierar en ursprungsservern. <br/>    2. Definiera en relativ sökväg med hjälp av: <br/>        – Ett mönster för reguljärt uttryck <br/>        - [HTTP-variabler](cdn-http-variables.md) <br/> <br/> Byt ut värdena som hämtats i mönstret för källa till mål-mönster genom att använda $_n_ där _n_ identifierar ett värde i den ordning som den hade hämtats. Till exempel representerar $1 det första värdet som avbildas i mönstret för källan, medan $2 representerar det andra värdet. 
 Den här funktionen kan POP: erna att skriva om URL: en utan att utföra en traditionell omdirigering. Det vill säga den som begär tar emot samma svarskoden som om URL: en ny hade begärts.

**Exempelscenario 1**

Det här exemplet visar hur du dirigerar om en kant CNAME-URL som motsvarar det här grundläggande URL: en för CDN: http:\//marketing.azureedge.net/brochures/

Kvalificerade begäranden omdirigeras till den här grundläggande edge CNAME-URL: http:\//MyOrigin.azureedge.net/resources/

Den här URL-omdirigering kan uppnås genom följande konfiguration: ![URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Exempelscenario 2**

Det här exemplet visar hur du dirigerar om en kant CNAME URL: en från versaler till gemener med reguljära uttryck.

Den här URL-omdirigering kan uppnås genom följande konfiguration: ![URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Viktiga punkter:**

- URL-Omskrivningsregler-funktionen definierar begäran URL: er som ska skrivas. Därför krävs inga ytterligare matchningsvillkor. Även om det matchar villkoret har definierats som ”alltid”, har endast begäranden som pekar på mappen ”broschyrer” på ”marknadsföring” kund ursprunget skrivits.

- URL-segment som hämtades från begäran läggs till på den nya URL: en via ”$1”.

#### <a name="compatibility"></a>Efterlevnad
Den här funktionen innehåller matchande kriterier som måste uppfyllas innan den kan tillämpas på en begäran. Den här funktionen är inte kompatibel med följande matchningsvillkor för att förhindra att ställa in matchar de villkor som i konflikt:

- AS-nummer
- CDN-ursprung
- IP-adress för klient
- Kunden
- Begäran-schema
- URL-sökväg-katalog
- URL-sökväg-tillägget
- URL: en sökväg filnamn
- URL-sökväg Literal
- URL-sökväg Regex
- URL-sökväg med jokertecken
- URL: en fråga Literal
- URL: en frågeparameter
- URL: en fråga Regex
- URL: en fråga med jokertecken

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Användarvariabeln
**Syfte:** endast för internt bruk.

[Överst på sidan](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>Nästa steg
* [Regelmotor – referens](cdn-rules-engine-reference.md)
* [Regelmotor – villkorliga uttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regelmotor – matchningsvillkor](cdn-rules-engine-reference-match-conditions.md)
* [Åsidosätt HTTP beteende med regler-motor](cdn-rules-engine.md)
* [Översikt över Azure CDN](cdn-overview.md)
