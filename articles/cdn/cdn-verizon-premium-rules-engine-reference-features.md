---
title: Azure CDN från Verizon Premium-regel motor funktioner | Microsoft Docs
description: Referens dokumentation för Azure CDN från funktioner i verktyg för Verizon Premium-regler.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 9177ac544c83305ae95ad681d3dc9f84ac64ea36
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381829"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN från Verizon Premium Rules Engine-funktioner

Den här artikeln innehåller detaljerade beskrivningar av de tillgängliga funktionerna för [regel motorn](cdn-verizon-premium-rules-engine.md)för Azure Content Delivery Network (CDN).

Den tredje delen av en regel är funktionen. En funktion definierar vilken typ av åtgärd som ska tillämpas på den typ av begäran som identifieras av en uppsättning matchnings villkor.

## <a name="access-features"></a>Åtkomst funktioner

Dessa funktioner är utformade för att styra åtkomsten till innehåll.

Namn | Syfte
-----|--------
[Neka åtkomst (403)](#deny-access-403) | Anger om alla förfrågningar avvisas med ett 403-förbjudet svar.
[Token-autentisering](#token-auth) | Anger om tokenbaserad autentisering används för en begäran.
[Nekad token-auktoriseringskod](#token-auth-denial-code) | Bestämmer vilken typ av svar som returneras till en användare när en begäran nekas på grund av tokenbaserad autentisering.
[Ignorera URL-fall för token auth](#token-auth-ignore-url-case) | Anger om URL-jämförelser gjorda av tokenbaserad autentisering är Skift läges känsliga.
[Parameter för token-autentisering](#token-auth-parameter) | Anger om den token-baserade autentiseringens frågesträngs parameter ska byta namn.

## <a name="caching-features"></a>Funktioner för cachelagring

Dessa funktioner är utformade för att anpassa när och hur innehållet ska cachelagras.

Namn | Syfte
-----|--------
[Bandbredds parametrar](#bandwidth-parameters) | Anger om bandbredds begränsnings parametrar (till exempel ec_rate och ec_prebuf) är aktiva.
[Bandbredds begränsning](#bandwidth-throttling) | Begränsar bandbredden för det svar som tillhandahålls av "POP (Point-of-Presence").
[Kringgå cacheminne](#bypass-cache) | Anger om begäran ska kringgå cachelagring.
[Hantering av Cache-Control-huvud](#cache-control-header-treatment) | Styr genereringen av `Cache-Control` sidhuvuden med POP när den externa funktionen för högsta ålder är aktiv.
[Cache-nyckel frågesträng](#cache-key-query-string) | Bestämmer om cache-nyckeln inkluderar eller exkluderar frågesträngs parametrar som är associerade med en begäran.
[Cache-omarbetning av nyckel](#cache-key-rewrite) | Skriver om den cache-nyckel som är kopplad till en begäran.
[Slutför cache-fyllning](#complete-cache-fill) | Bestämmer vad som händer när en begäran resulterar i en partiell cache missar i en POP.
[Komprimera filtyper](#compress-file-types) | Definierar fil formaten för de filer som är komprimerade på servern.
[Standard intern max ålder](#default-internal-max-age) | Fastställer standardvärdet för max-ålder för POP to Origin Server cache-omverifiering.
[Hantering av förfallo datum för huvud](#expires-header-treatment) | Styr genereringen av `Expires` sidhuvuden med en POP när den externa Max-Age-funktionen är aktiv.
[Extern högsta ålder](#external-max-age) | Fastställer Max-ålders intervallet för webbläsare till POP-cache-omverifiering.
[Framtvinga intern max ålder](#force-internal-max-age) | Fastställer Max-ålders intervallet för start till ursprungs serverns cache-omverifiering.
[Stöd för H. 264 (HTTP progressiv nedladdning)](#h264-support-http-progressive-download) | Bestämmer vilka typer av H. 264-filformat som kan användas för att strömma innehåll.
[Svara på no-cache-begäran](#honor-no-cache-request) | Anger om en HTTP-klients begär Anden om att begäran vidarebefordras till ursprungs servern.
[Ignorera ursprung no-cache](#ignore-origin-no-cache) | Bestämmer om CDN ignorerar vissa direktiv som hanteras från en ursprungs Server.
[Ignorera områden som inte går att uppfylla](#ignore-unsatisfiable-ranges) | Avgör vilket svar som returneras till klienter när en begäran genererar ett begärt intervall av 416 som inte uppfyller kraven.
[Intern Max-inaktuell](#internal-max-stale) | Styr hur lång tid det normala förfallo datumet för en cachelagrad till gång kan hanteras från en POP när POP inte kan omverifiera den cachelagrade till gången med ursprungs servern.
[Partiell cache-delning](#partial-cache-sharing) | Anger om en begäran kan generera delvis cachelagrat innehåll.
[Förvalidera cachelagrat innehåll](#prevalidate-cached-content) | Anger om cachelagrat innehåll är kvalificerat för tidig omverifiering innan dess TTL går ut.
[Uppdatera cache-filer med noll byte](#refresh-zero-byte-cache-files) | Bestämmer hur en HTTP-klientbegäran för en cache-till-0-byte hanteras av pop.
[Ange status koder för cachelagring](#set-cacheable-status-codes) | Definierar den uppsättning med status koder som kan leda till cachelagrat innehåll.
[Inaktuell innehålls leverans vid fel](#stale-content-delivery-on-error) | Anger om förfallet cachelagrat innehåll levereras när ett fel uppstår under cache-omverifieringen eller när det begärda innehållet hämtas från kund ursprungs servern.
[Inaktuell vid omverifiering](#stale-while-revalidate) | Förbättrar prestandan genom att tillåta pop att betjäna inaktuella klient till beställaren medan omverifieringen sker.

## <a name="comment-feature"></a>Kommentars funktion

Den här funktionen är utformad för att ge ytterligare information i en regel.

Namn | Syfte
-----|--------
[Kommentar](#comment) | Tillåter att en anteckning läggs till i en regel.

## <a name="header-features"></a>Rubrik funktioner

Dessa funktioner är utformade för att lägga till, ändra eller ta bort huvuden från begäran eller svaret.

Namn | Syfte
-----|--------
[Ålders svars rubrik](#age-response-header) | Anger om ett ålders svars huvud ingår i svaret som skickas till beställaren.
[Felsöka cache-svarshuvuden](#debug-cache-response-headers) | Bestämmer om ett svar kan innehålla huvudet X-EC-debug-svar, som innehåller information om cache-principen för den begärda till gången.
[Ändra huvudet för klientbegäran](#modify-client-request-header) | Skriver över, lägger till eller tar bort en rubrik från en begäran.
[Ändra huvudet för klient svaret](#modify-client-response-header) | Skriver över, lägger till eller tar bort en rubrik från ett svar.
[Ange IP-adress för anpassade klienter](#set-client-ip-custom-header) | Tillåter att IP-adressen för den begär ande klienten läggs till i begäran som en anpassad begär ande rubrik.

## <a name="logging-features"></a>Loggnings funktioner

Dessa funktioner är utformade för att anpassa data som lagras i råa loggfiler.

Namn | Syfte
-----|--------
[Anpassat logg fält 1](#custom-log-field-1) | Anger formatet och det innehåll som är kopplat till det anpassade logg fältet i en rå loggfil.
[Frågesträng för logg](#log-query-string) | Anger om en frågesträng lagras tillsammans med URL: en i Access-loggar.


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

## <a name="origin-features"></a>Ursprungs funktioner

Dessa funktioner är utformade för att styra hur CDN kommunicerar med en ursprungs Server.

Namn | Syfte
-----|--------
[Maximalt antal Keep-Alive-begäranden](#maximum-keep-alive-requests) | Definierar det maximala antalet begär Anden för en Keep-Alive-anslutning innan den stängs.
[Särskilda rubriker för proxy](#proxy-special-headers) | Definierar den uppsättning CDN-Specific begärandehuvuden som vidarebefordras från en POP till en ursprungs Server.

## <a name="specialty-features"></a>Special funktioner

Dessa funktioner ger avancerade användare avancerade funktioner.

Namn | Syfte
-----|--------
[HTTP-metoder för cachelagring](#cacheable-http-methods) | Anger en uppsättning ytterligare HTTP-metoder som kan cachelagras i nätverket.
[Brödtext storlek för cache-begäran](#cacheable-request-body-size) | Definierar tröskelvärdet för att avgöra om ett POST-svar kan cachelagras.
[Användar variabel](#user-variable) | Endast för internt bruk.

## <a name="url-features"></a>URL-funktioner

Dessa funktioner tillåter att en begäran omdirigeras eller skrivs om till en annan URL.

Namn | Syfte
-----|--------
[Följ omdirigeringar](#follow-redirects) | Bestämmer om begär Anden kan omdirigeras till det värdnamn som definierats i det plats huvud som returneras av en kund ursprungs Server.
[URL-omdirigering](#url-redirect) | Omdirigerar begär Anden via plats rubriken.
[URL-omskrivning](#url-rewrite)  | Skriver om URL: en för begäran.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Azure CDN från Verizon Premium Rules-funktioner referens

---

### <a name="age-response-header"></a>Ålders svars rubrik

**Syfte**: avgör om ett ålders svars huvud ingår i svaret som skickas till beställaren.

Värde|Resultat
--|--
Enabled | Ålders svarets huvud ingår i svaret som skickas till beställaren.
Disabled | Ålders svarets huvud undantas från svaret som skickas till beställaren.

**Standard beteende**: inaktive rad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Bandbredds parametrar

**Syfte:** Anger om bandbredds begränsnings parametrar (till exempel ec_rate och ec_prebuf) är aktiva.

Parametrarna för bandbredds begränsning avgör om data överförings hastigheten för en klient förfrågan är begränsad till en anpassad kostnad.

Värde|Resultat
--|--
Enabled|Tillåter pop att respektera begär Anden om bandbredds begränsning.
Disabled|Gör att pop: erna ignorerar bandbredds begränsnings parametrar. Det begärda innehållet hanteras normalt (det vill säga utan bandbredds begränsning).

**Standard beteende:** Aktiva.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Bandbredds begränsning

**Syfte:** Begränsar bandbredden för svar från pop.

Båda följande alternativ måste definieras för att konfigurera bandbredds begränsning korrekt.

Alternativ|Beskrivning
--|--
KByte per sekund|Ange det här alternativet till maximal bandbredd (kB per sekund) som kan användas för att leverera svaret.
Prebuf sekunder|Ange det här alternativet till antalet sekunder som pop: erna ska vänta tills bandbredden är begränsad. Syftet med den här tids perioden för obegränsad bandbredd är att förhindra att en Media spelare upplever problem med att hacka eller buffra på grund av bandbredds begränsning.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Kringgå cacheminne

**Syfte:** Anger om begäran ska kringgå cachelagring.

Värde|Resultat
--|--
Enabled|Gör så att alla begär Anden går över till ursprungs servern, även om innehållet tidigare cachelagrats på pop.
Disabled|Leder till att pop cachelagrar resurser enligt den cachelagrade princip som definierats i svars huvudena.

**Standard beteende:**

- **Http-stor:** Inaktiverats

<!---
- **ADN:** Enabled

--->

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>HTTP-metoder för cachelagring

**Syfte:** Anger en uppsättning ytterligare HTTP-metoder som kan cachelagras i nätverket.

Viktig information:

- Den här funktionen förutsätter att GET-svar alltid ska cachelagras. Därför bör GET HTTP-metoden inte tas med när du anger den här funktionen.
- Den här funktionen stöder bara POST HTTP-metoden. Aktivera cachelagring av svar genom att ställa in den här funktionen på `POST`.
- Som standard är endast förfrågningar vars brödtext är mindre än 14 KB cachelagrade. Använd funktionen storleks gräns för begär ande brödtext för att ange den maximala text storleken för begäran.

**Standard beteende:** Endast GET-svar cachelagras.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Brödtext storlek för cache-begäran

**Syfte:** Definierar tröskelvärdet för att avgöra om ett POST-svar kan cachelagras.

Det här tröskelvärdet bestäms genom att ange en maximal text storlek för begäran. Begär Anden som innehåller en större begär ande text cachelagras inte.

Viktig information:

- Den här funktionen kan endast användas när POST svar är kvalificerade för cachelagring. Använd funktionen för HTTP-metoder för cachelagring för att aktivera cachelagring av POST begär Anden.
- Begär ande texten tas i beaktande för:
    - x-www-form-urlencoded värden
    - Säkerställa en unik cache-nyckel
- Att definiera en stor maximal storlek för begär ande brödtext kan påverka data leveransens prestanda.
    - **Rekommenderat värde:** 14 KB
    - **Minsta värde:** 1 KB

**Standard beteende:** 14 KB

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Hantering av Cache-Control-huvud

**Syfte:** Styr genereringen av `Cache-Control` sidhuvuden vid POP när den externa funktionen för max-ålder är aktiv.

Det enklaste sättet att uppnå den här typen av konfiguration är att placera den externa bestånds tiden och hanterings funktionerna för Cache-Control-huvud i samma instruktion.

Värde|Resultat
--|--
Skriv över|Säkerställer att följande åtgärder utförs:<br/> – Skriver över `Cache-Control`s huvudet som genereras av ursprungs servern. <br/>– Lägger till `Cache-Control`s huvud som skapats av den externa Max-Age-funktionen till svaret.
Släpp igenom|Säkerställer att `Cache-Control`s huvud som skapas av den externa Max-Age-funktionen aldrig läggs till i svaret. <br/> Om ursprungs servern skapar ett `Cache-Control`-sidhuvud skickas den till slutanvändaren. <br/> Om ursprungs servern inte skapar ett `Cache-Control`-huvud kan det här alternativet orsaka att svars huvudet inte innehåller ett `Cache-Control`s huvud.
Lägg till om det saknas|Om ett `Cache-Control` huvud inte har tagits emot från ursprungs servern, lägger det här alternativet till det `Cache-Control`-huvud som produceras av den externa Max-ålders funktionen. Det här alternativet är användbart för att säkerställa att alla till gångar tilldelas ett `Cache-Control`s huvud.
Ta bort| Det här alternativet ser till att ett `Cache-Control`-huvud inte ingår i huvud svaret. Om ett `Cache-Control`s huvud redan har tilldelats, tas det bort från huvudets svar.

**Standard beteende:** Skriva över.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Cache-nyckel frågesträng

**Syfte:** Bestämmer om cache-nyckeln inkluderar eller exkluderar frågesträngs parametrar som är associerade med en begäran.

Viktig information:

- Ange ett eller flera parameter namn för frågesträng och avgränsa varje parameter namn med ett enda blank steg.
- Den här funktionen avgör om frågesträngs parametrar tas med eller undantas från cache-nyckeln. Ytterligare information finns för varje alternativ i följande tabell.

Typ|Beskrivning
--|--
 Omfattar|  Anger att varje angiven parameter ska ingå i cache-nyckeln. En unik cache-nyckel skapas för varje begäran som innehåller ett unikt värde för en frågesträngparametern som definierats i den här funktionen.
 Ta med alla  |Anger att en unik cache-nyckel skapas för varje begäran till en till gång som innehåller en unik frågesträng. Den här typen av konfiguration rekommenderas vanligt vis inte eftersom den kan leda till en liten procent andel cache-träffar. Ett lågt antal cacheträffar ökar belastningen på ursprungs servern eftersom den måste betjäna fler begär Anden. Den här konfigurationen duplicerar det cachelagrade beteendet kallat "Unique-cache" på sidan för cachelagring av frågesträngar.
 Exclude | Anger att endast de angivna parametrarna är exkluderade från cache-nyckeln. Alla andra parametrar för frågesträng ingår i cache-nyckeln.
 Undanta alla  |Anger att alla parametrar för frågesträng exkluderas från cache-nyckeln. Den här konfigurationen duplicerar standardvärdet för cachelagring på Standard-cache på sidan för cachelagring av frågesträngar.  

Med regel motorn kan du anpassa hur frågesträngen för cachelagring av strängar implementeras. Du kan till exempel ange att cachelagring av frågesträngar endast ska utföras på vissa platser eller filtyper.

Om du vill duplicera funktionen "no-cache" för cachelagring av frågesträngar på sidan för cachelagring av frågesträngar, skapar du en regel som innehåller en URL-fråga med jokertecken matchnings villkor och en funktion för att kringgå cache. Ange en asterisk (*) som jokertecken för URL-frågans jokertecken.

>[!IMPORTANT]
> Om token-auktorisering har Aktiver ATS för en sökväg i det här kontot är standard-cache läget det enda läget som kan användas för cachelagring av frågesträngar. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Exempel scenarier

Följande exempel på användning för den här funktionen är en exempel förfrågan och standard-cache-nyckeln:

- **Exempel förfrågan:** http://wpc.0001.&lt;D omain&gt;/800001/Origin/Folder/Asset.htm? SessionID = 1234 & language = en & UserID = 01
- **Standard-cache-nyckel:** /800001/Origin/Folder/Asset.htm

##### <a name="include"></a>Omfattar

Exempel på konfiguration:

- **Typ:** Omfattar
- **Parameter (s):** språk

Den här typen av konfiguration genererar följande parameter-nyckel för frågesträng:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Ta med alla

Exempel på konfiguration:

- **Typ:** Ta med alla

Den här typen av konfiguration genererar följande parameter-nyckel för frågesträng:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Exempel på konfiguration:

- **Typ:** Ingå
- **Parameter (er):** sessions-UserID

Den här typen av konfiguration genererar följande parameter-nyckel för frågesträng:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Undanta alla

Exempel på konfiguration:

- **Typ:** Undanta alla

Den här typen av konfiguration genererar följande parameter-nyckel för frågesträng:

    /800001/Origin/folder/asset.htm

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Cache-omarbetning av nyckel

**Syfte:** Skriver om den cache-nyckel som är kopplad till en begäran.

En cache-Key är den relativa sökvägen som identifierar en till gång för cachelagring. Med andra ord söker servrarna efter en cachelagrad version av en till gång enligt dess sökväg som definieras av dess cache-nyckel.

Konfigurera den här funktionen genom att definiera båda följande alternativ:

Alternativ|Beskrivning
--|--
Ursprunglig sökväg| Definiera den relativa sökvägen till de typer av begär Anden vars cache-Key har skrivits om. Du kan definiera en relativ sökväg genom att välja en bas Start Sök väg och sedan definiera ett mönster för reguljära uttryck.
Ny sökväg|Definiera den relativa sökvägen för den nya cache-nyckeln. Du kan definiera en relativ sökväg genom att välja en bas Start Sök väg och sedan definiera ett mönster för reguljära uttryck. Den här relativa sökvägen kan skapas dynamiskt genom användningen av [http-variabler](cdn-http-variables.md).

**Standard beteende:** En begärans cache-nyckel bestäms av begärande-URI: n.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Kommentar

**Syfte:** Tillåter att en anteckning läggs till i en regel.

En användning för den här funktionen är att ge ytterligare information om generell användning av en regel eller varför ett visst matchnings villkor eller en funktion har lagts till i regeln.

Viktig information:

- Högst 150 tecken kan anges.
- Använd endast alfanumeriska tecken.
- Den här funktionen påverkar inte regelns funktion. Den är bara avsedd att tillhandahålla ett utrymme där du kan ange information för framtida referens eller som kan hjälpa dig att felsöka regeln.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Slutför cache-fyllning

**Syfte:** Bestämmer vad som händer när en begäran resulterar i en partiell cache missar i en POP.

En partiell cache missar beskriver cache status för en till gång som inte har laddats ned helt till en POP. Om en till gång bara är delvis cachelagrad på en POP, vidarebefordras nästa begäran om till gången till ursprungs servern.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
En del av cachen inträffar vanligt vis när en användare avbryter en nedladdning eller för till gångar som endast begärs med HTTP-intervall begär Anden. Den här funktionen är mest användbar för stora till gångar som vanligt vis hämtas från början till slut (till exempel videor). Detta innebär att den här funktionen är aktive rad som standard på den stora HTTP-plattformen. Den är inaktive rad på alla andra plattformar.

Behåll standard konfigurationen för den stora HTTP-plattformen, eftersom den minskar belastningen på din kund ursprungs Server och ökar hastigheten som kunderna laddar ned ditt innehåll med.

Värde|Resultat
--|--
Enabled|Återställer standard beteendet. Standard beteendet är att tvinga POP att initiera en bakgrunds hämtning av till gången från ursprungs servern. Därefter kommer till gången att finnas i POP: s lokala cacheminne.
Disabled|Förhindrar att en POP utför en bakgrunds hämtning för till gången. Resultatet är att nästa begäran om till gången från den regionen gör att en POP begär det från kund ursprungs servern.

**Standard beteende:** Aktiva.

#### <a name="compatibility"></a>Kompatibilitet

Den här funktionen kan inte associeras med följande matchnings villkor på det sätt som cache-inställningarna spåras:

- SOM nummer
- Klientens IP-adress
- Cookie-parameter
- Cookie-parameter regex
- Land/region
- Enhet
- Microsoft Edge CNAME
- Refererande domän
- Tecken för begär ande rubrik
- Huvudbegäran-huvud-regex
- Jokertecken för begär ande rubrik
- Begärandemetod
- Begär ande schema
- URL-frågans litteral
- URL-fråga regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Komprimera filtyper

**Syfte:** Definierar fil formaten för de filer som är komprimerade på servern.

Ett fil format kan anges med hjälp av en medietyp för Internet (t. ex. innehålls typ). Internet medie typen är plattforms oberoende metadata som gör att servrarna kan identifiera fil formatet för en viss till gång. En lista över vanliga typer av Internet Media finns nedan.

Internet medie typ|Beskrivning
--|--
text/ren|Filer med oformaterad text
text/html| HTML-filer
text/CSS|Sammanhängande formatmallar (CSS)
application/x-javascript|Javascript
program/java script|Javascript

Viktig information:

- Ange flera Internet medie typer genom att avgränsa var och en med ett enda blank steg.
- Den här funktionen komprimerar endast till gångar vars storlek är mindre än 1 MB. Större till gångar komprimeras inte av servrarna.
- Vissa typer av innehåll, till exempel bilder, video och ljud medie till gångar (till exempel JPG, MP3, MP4 osv.), är redan komprimerade. Eftersom ytterligare komprimering av dessa typer av till gångar inte avsevärt minskar fil storleken, rekommenderar vi att du inte aktiverar komprimering på dem.
- Jokertecken, till exempel asterisker, stöds inte.
- Innan du lägger till den här funktionen i en regel ska du se till att du ställer in alternativet komprimering inaktiverat på komprimerings sidan för den plattform som regeln tillämpas på.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Anpassat logg fält 1

**Syfte:** Anger formatet och det innehåll som ska tilldelas till det anpassade logg fältet i en rå loggfil.

I det här anpassade fältet kan du avgöra vilka värden för begäran och svars huvud som lagras i dina loggfiler.

Som standard kallas det anpassade logg fältet "x-ec_custom-1". Namnet på det här fältet kan anpassas på sidan obehandlade logg inställningar.

Formatet för att ange begärandehuvuden för begäran och svar definieras enligt följande:

Rubrik typ|Format|Exempel
-|-|-
Begärandehuvud|`%{[RequestHeader]()}[i]()` | % {Accept-Encoding} i <br/> {Referent} i <br/> % {Authorization} i
Svars huvud|`%{[ResponseHeader]()}[o]()`| % {Age} o <br/> % {Content-Type} o <br/> %{Cookie}o

Viktig information:

- Ett anpassat logg fält kan innehålla valfri kombination av rubrik fält och oformaterad text.
- Giltiga tecken i det här fältet är följande: alfanumeriskt (0-9, a-z och A-Z), bindestreck, kolon, semikolon, apostrofer, kommatecken, punkter, under streck, likhets tecken, parenteser, hakparenteser och blank steg. Procent symbolen och klammerparenteser tillåts endast när de används för att ange ett rubrik fält.
- Stavningen för varje angivet huvud fält måste matcha det önskade rubrik namnet för begäran/svar.
- Om du vill ange flera huvuden använder du en avgränsare för att ange varje rubrik. Du kan till exempel använda en förkortning för varje rubrik:
    - AE:% {Accept-Encoding} i A:% {Authorization} i CT:% {Content-Type} o

**Standardvärde:**  -

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Felsöka cache-svarshuvuden

**Syfte:** Bestämmer om ett svar kan innehålla [huvuden i X-EG-debug](cdn-http-debug-headers.md), som innehåller information om cache-principen för den begärda till gången.

Debug cache-svarshuvuden tas med i svaret när båda följande stämmer:

- Funktionen för att felsöka cache-svarshuvuden har Aktiver ATS på den angivna begäran.
- Den angivna begäran definierar den uppsättning av svars rubriker för debug-cachen som ska ingå i svaret.

Felsök cache-svarshuvuden kan begäras genom att inkludera följande rubrik och de angivna direktiven i begäran:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Exempel:**

X-EG-debug: x-EC-cache, x-EG-check-cacheable, x-EG-cache-Key, x-EG-cache-State

Värde|Resultat
-|-
Enabled|Begär Anden om debug-svarshuvuden kommer att returnera ett svar som innehåller huvudet X-EC-debug.
Disabled|Svars huvudet för X-EG-debug kommer att undantas från svaret.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Standard intern max ålder

**Syfte:** Fastställer standardvärdet för max-ålder för POP to Origin Server cache-omverifiering. Det vill säga hur lång tid som ska gå innan en POP-server kommer att kontrol lera om en cachelagrad till gång matchar den till gång som lagras på ursprungs servern.

Viktig information:

- Den här åtgärden utförs bara för svar från en ursprungs server som inte har tilldelat en max ålders angivelse i `Cache-Control`-eller `Expires`s huvudet.
- Den här åtgärden kan inte utföras för till gångar som inte anses vara cacheable.
- Den här åtgärden påverkar inte webbläsare till POP-cache-omverifieringar. De här typerna av omverifieringar bestäms av `Cache-Control` eller `Expires` huvuden som skickas till webbläsaren, som kan anpassas med den externa Max-ålders funktionen.
- Resultatet av den här åtgärden har ingen underrättad inverkan på svarshuvuden och det innehåll som returneras från pop för ditt innehåll, men det kan påverka mängden resigning-trafik som skickas från pop till din ursprungs Server.
- Konfigurera den här funktionen genom att:
    - Välj den status kod som standard intern max ålder kan användas för.
    - Ange ett heltals värde och sedan välja önskad tidsenhet (till exempel sekunder, minuter, timmar osv.). Det här värdet definierar det interna standard intervallet för max ålder.

- Om du ställer in tidsenheten på "off", tilldelas standard intervallet för intern max ålder på 7 dagar för begär Anden som inte har tilldelats en max ålders angivelse i deras `Cache-Control`-eller `Expires`s huvud.

**Standardvärde:** 7 dagar

#### <a name="compatibility"></a>Kompatibilitet

Den här funktionen kan inte associeras med följande matchnings villkor på det sätt som cache-inställningarna spåras:
- SOM nummer
- Klientens IP-adress
- Cookie-parameter
- Cookie-parameter regex
- Land/region
- Enhet
- Edge CNAME
- Refererande domän
- Tecken för begär ande rubrik
- Huvudbegäran-huvud-regex
- Jokertecken för begär ande rubrik
- Begärandemetod
- Begär ande schema
- URL-frågans litteral
- URL-fråga regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Neka åtkomst (403)

**Syfte**: avgör om alla begär Anden avvisas med ett 403-förbjudet svar.

Värde | Resultat
------|-------
Enabled| Gör så att alla begär Anden som uppfyller matchnings kriterierna avvisas med ett 403-förbjudet svar.
Disabled| Återställer standard beteendet. Standard beteendet är att låta ursprungs servern bestämma vilken typ av svar som ska returneras.

**Standard beteende**: inaktive rad

> [!TIP]
   > En möjlig användning för den här funktionen är att associera den med ett matchnings villkor för begärans huvud för att blockera åtkomst till HTTP-referenter som använder infogade länkar till ditt innehåll.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Hantering av förfallo datum för huvud

**Syfte:** Styr genereringen av `Expires` sidhuvuden med en POP när den externa Max-Age-funktionen är aktiv.

Det enklaste sättet att uppnå den här typen av konfiguration är att placera den externa Max åldern och hanterings funktionerna för Expires-huvud i samma instruktion.

Värde|Resultat
--|--
Skriv över|Säkerställer att följande åtgärder utförs:<br/>– Skriver över `Expires`s huvudet som genereras av ursprungs servern.<br/>– Lägger till `Expires`s huvud som skapats av den externa Max-Age-funktionen till svaret.
Släpp igenom|Säkerställer att `Expires`s huvud som skapas av den externa Max-Age-funktionen aldrig läggs till i svaret. <br/> Om ursprungs servern skapar ett `Expires`-sidhuvud skickas den till slutanvändaren. <br/>Om ursprungs servern inte skapar ett `Expires`-huvud kan det här alternativet orsaka att svars huvudet inte innehåller ett `Expires`s huvud.
Lägg till om det saknas| Om ett `Expires`s huvud inte har tagits emot från ursprungs servern, lägger det här alternativet till det `Expires`-huvud som produceras av den externa Max-ålders funktionen. Det här alternativet är användbart för att se till att alla till gångar tilldelas ett `Expires`s huvud.
Ta bort| Ser till att ett `Expires`-huvud inte ingår i huvud svaret. Om ett `Expires`s huvud redan har tilldelats, tas det bort från huvudets svar.

**Standard beteende:** Skriva över

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Extern högsta ålder

**Syfte:** Fastställer Max-ålders intervallet för webbläsare till POP-cache-omverifiering. Med andra ord kan du kontrol lera hur lång tid som ska gå innan en webbläsare söker efter en ny version av en till gång från en POP.

Om du aktiverar den här funktionen skapas `Cache-Control: max-age`-och `Expires`-rubriker från pop-sidorna och de skickas till HTTP-klienten. Som standard kommer de här rubrikerna skriva över de rubriker som skapats av ursprungs servern. Men hanterings funktionerna för Cache-Control och Expires-huvud kan användas för att ändra det här beteendet.

Viktig information:

- Den här åtgärden påverkar inte start-till-serverns cache-omverifieringar. De här typerna av omverifieringar bestäms av `Cache-Control` och `Expires` huvuden som tagits emot från ursprungs servern och kan anpassas till standardvärdet för intern max ålder och den tvingande interna Max åldern.
- Konfigurera den här funktionen genom att ange ett heltals värde och välja önskad tidsenhet (till exempel sekunder, minuter, timmar osv.).
- Om den här funktionen ställs in på ett negativt värde skickas en `Cache-Control: no-cache` och en `Expires` tid som har angetts tidigare med varje svar till webbläsaren. Även om en HTTP-klient inte cachelagrar svaret kommer den här inställningen inte att påverka pop: s möjlighet att cachelagra svaret från ursprungs servern.
- När du ställer in tidsenheten på "off" inaktive ras den här funktionen. De `Cache-Control` och `Expires` huvuden som cachelagras med svaret från ursprungs servern skickas till webbläsaren.

**Standard beteende:** Rund

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Följ omdirigeringar

**Syfte:** Bestämmer om begär Anden kan omdirigeras till det värdnamn som definierats i det plats huvud som returneras av en kund ursprungs Server.

Viktig information:

- Begär Anden kan bara omdirigeras till Edge CNAME som motsvarar samma plattform.

Värde|Resultat
-|-
Enabled|Begär Anden kan omdirigeras.
Disabled|Begär Anden kommer inte att omdirigeras.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Framtvinga intern max ålder

**Syfte:** Fastställer Max-ålders intervallet för start till ursprungs serverns cache-omverifiering. Det vill säga hur lång tid som ska gå innan en POP kan kontrol lera om en cachelagrad till gång matchar den till gång som lagras på ursprungs servern.

Viktig information:

- Den här funktionen åsidosätter det högsta ålders intervallet som definieras i `Cache-Control` eller `Expires` huvuden som genereras från en ursprungs Server.
- Den här funktionen påverkar inte webbläsare till POP-cache-omverifieringar. De här typerna av omverifieringar bestäms av `Cache-Control` eller `Expires` huvuden som skickas till webbläsaren.
- Den här funktionen har ingen undersökande påverkan på svaret som skickas av en POP till beställaren. Det kan dock påverka mängden resigning-trafik som skickas från pop-servern till ursprungs servern.
- Konfigurera den här funktionen genom att:
    - Välj den status kod för vilken en intern max ålder ska tillämpas.
    - Ange ett heltals värde och välja önskad tidsenhet (till exempel sekunder, minuter, timmar osv.). Det här värdet definierar Max ålders intervallet för begäran.

- När du ställer in tidsenheten på "off" inaktive ras den här funktionen. Ett internt Max-ålders intervall kommer inte att tilldelas till begärda till gångar. Om det ursprungliga huvudet inte innehåller instruktioner för cachelagring kommer till gången att cachelagras enligt den aktiva inställningen i standardvärdet för intern max ålder.

**Standard beteende:** Rund

#### <a name="compatibility"></a>Kompatibilitet

Den här funktionen kan inte associeras med följande matchnings villkor på det sätt som cache-inställningarna spåras:
- SOM nummer
- Klientens IP-adress
- Cookie-parameter
- Cookie-parameter regex
- Land/region
- Enhet
- Edge CNAME
- Refererande domän
- Tecken för begär ande rubrik
- Huvudbegäran-huvud-regex
- Jokertecken för begär ande rubrik
- Begärandemetod
- Begär ande schema
- URL-frågans litteral
- URL-fråga regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>Stöd för H. 264 (HTTP progressiv nedladdning)

**Syfte:** Bestämmer vilka typer av H. 264-filformat som kan användas för att strömma innehåll.

Viktig information:

- Definiera en blankstegsavgränsad uppsättning med tillåtna fil namns tillägg för H. 264 i alternativet fil namns tillägg. Alternativet fil tillägg åsidosätter standard beteendet. Behåll MP4-och F4V-stöd genom att inkludera dessa fil namns tillägg när du anger det här alternativet.
- Inkludera en period när du anger varje fil namns tillägg (till exempel _. mp4_, _. F4V_).

**Standard beteende:** HTTP progressiv nedladdning stöder MP4-och F4V-medier som standard.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Svara på no-cache-begäran

**Syfte:** Anger om en HTTP-klients begär Anden om antal cachen ska vidarebefordras till ursprungs servern.

En begäran om no-cache inträffar när HTTP-klienten skickar ett `Cache-Control: no-cache`-och/eller `Pragma: no-cache`-huvud i HTTP-begäran.

Värde|Resultat
--|--
Enabled|Tillåter att en HTTP-klients antal cache-begäranden vidarebefordras till ursprungs servern och att ursprungs servern kommer att returnera svarshuvuden och brödtext via popup-fönstret till HTTP-klienten.
Disabled|Återställer standard beteendet. Standard beteendet är att förhindra att inga cache-begäranden vidarebefordras till ursprungs servern.

För all produktions trafik rekommenderar vi starkt att du lämnar den här funktionen i inaktiverat standard läge. Annars går det inte att avskärma ursprungs servrar från slutanvändare som oavsiktligt kan utlösa många förfrågningar om cachelagring vid uppdatering av webb sidor eller från de många populära medie spelare som kodas för att skicka ett no-cache-huvud med varje video förfrågan. Den här funktionen kan dock vara användbar om du vill använda en viss icke-produktion för mellanlagrings-eller test kataloger för att tillåta att nytt innehåll hämtas på begäran från ursprungs servern.

Den cache-status som rapporteras för en begäran som kan vidarebefordras till en ursprungs server på grund av att den här funktionen är `TCP_Client_Refresh_Miss`. Rapporten cache Statuss, som är tillgänglig i modulen huvud rapportering, ger statistisk information efter cache-status. Med den här rapporten kan du spåra antalet och procent andelen begär Anden som vidarebefordras till en ursprungs server på grund av den här funktionen.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignorera ursprung no-cache

**Syfte:** Bestämmer om CDN ignorerar följande direktiv som hanteras från en ursprungs Server:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Viktig information:

- Konfigurera den här funktionen genom att definiera en blankstegsavgränsad lista med status koder för vilka ovanstående direktiv ignoreras.
- Uppsättningen med giltiga status koder för den här funktionen är: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, 505, och.
- Inaktivera den här funktionen genom att ställa in den på ett tomt värde.

**Standard beteende:** Standard beteendet är att respektera ovanstående direktiv.

#### <a name="compatibility"></a>Kompatibilitet

Den här funktionen kan inte associeras med följande matchnings villkor på det sätt som cache-inställningarna spåras:
- SOM nummer
- Klientens IP-adress
- Cookie-parameter
- Cookie-parameter regex
- Land/region
- Enhet
- Edge CNAME
- Refererande domän
- Tecken för begär ande rubrik
- Huvudbegäran-huvud-regex
- Jokertecken för begär ande rubrik
- Begärandemetod
- Begär ande schema
- URL-frågans litteral
- URL-fråga regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignorera områden som inte går att uppfylla

**Syfte:** Avgör vilket svar som returneras till klienter när en begäran genererar ett begärt intervall av 416 som inte kan uppfylla status koden.

Som standard returneras den här status koden när den angivna begäran om byte-intervall inte kan uppfyllas av en POP och ett IF-Range begär ande rubrik fält inte har angetts.

Värde|Resultat
-|-
Enabled|Förhindrar att pop svarar på en ogiltig byte-Range-begäran med ett 416 begärt intervall som inte uppfyller kraven. I stället kommer servrarna att leverera den begärda till gången och returnera en 200 OK till klienten.
Disabled|Återställer standard beteendet. Standard beteendet är att respektera det begärda intervallet för 416 som inte uppfyller kraven.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Intern Max-inaktuell

**Syfte:** Styr hur lång tid det normala förfallo datumet för en cachelagrad till gång kan hanteras från en POP när POP inte kan omverifiera den cachelagrade till gången med ursprungs servern.

När en till gångs max ålder upphör att gälla kommer POP-servern att skicka en begäran om verifiering till ursprungs servern. Ursprungs servern svarar sedan med antingen en 304 som inte har ändrats för att ge POP ett nytt lån på den cachelagrade till gången, eller andra med 200 OK för att tillhandahålla POP med en uppdaterad version av den cachelagrade till gången.

Om POP inte kan upprätta en anslutning till ursprungs servern vid ett försök att göra en sådan omverifiering, styr den här interna Max inaktuella funktionen om, och hur länge, POP kan fortsätta att betjäna den nu inaktuella till gången.

Observera att det här tidsintervallet startar när till gångens max ålder upphör att gälla, inte när den misslyckade omverifieringen sker. Den längsta period under vilken en till gång kan hanteras utan en lyckad omautentisering är därför den tid som anges av kombinationen av max-ålder plus Max-inaktuell. Om till exempel en till gång cachelagrades vid 9:00 med en högsta ålder på 30 minuter och en högsta-inaktuell på 15 minuter skulle ett misslyckat omverifierings försök på 9:44 leda till att en slutanvändare tar emot den inaktuella cachelagrade till gången, medan ett misslyckat omverifierings försök vid 9:46 skulle resultera i en d användaren får en timeout på 504 Gateway.

Alla värden som har kon figurer ATS för den här funktionen ersätts av `Cache-Control: must-revalidate` eller `Cache-Control: proxy-revalidate` huvuden som tagits emot från ursprungs servern. Om något av dessa huvuden tas emot från ursprungs servern när en till gång ursprungligen cachelagras, kommer POP inte att betjäna en inaktuell cachelagrad till gång. I sådana fall, om POP inte kan omverifiera med ursprunget när till gångens Max ålders intervall har upphört att gälla, returnerar POP en 504 Gateway-tidsgräns.

Viktig information:

- Konfigurera den här funktionen genom att:
    - Välj den status kod för vilken en högsta-inaktuell ska tillämpas.
    - Ange ett heltals värde och sedan välja önskad tidsenhet (till exempel sekunder, minuter, timmar osv.). Det här värdet definierar den interna Max inaktuell som ska användas.

- När du ställer in tidsenheten på "off" inaktive ras den här funktionen. En cachelagrad till gång kommer inte att betjänas utanför den normala förfallo tiden.

**Standard beteende:** Två minuter

#### <a name="compatibility"></a>Kompatibilitet

Den här funktionen kan inte associeras med följande matchnings villkor på det sätt som cache-inställningarna spåras:
- SOM nummer
- Klientens IP-adress
- Cookie-parameter
- Cookie-parameter regex
- Land/region
- Enhet
- Edge CNAME
- Refererande domän
- Tecken för begär ande rubrik
- Huvudbegäran-huvud-regex
- Jokertecken för begär ande rubrik
- Begärandemetod
- Begär ande schema
- URL-frågans litteral
- URL-fråga regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Frågesträng för logg

**Syfte:** Anger om en frågesträng ska lagras tillsammans med URL: en i Access-loggar.

Värde|Resultat
-|-
Enabled|Tillåter lagring av frågesträngar när du registrerar URL: er i en Access-logg. Om en URL inte innehåller någon frågesträng kommer det här alternativet inte att ha någon påverkan.
Disabled|Återställer standard beteendet. Standard beteendet är att ignorera frågesträngar när du registrerar URL: er i en Access-logg.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximalt antal Keep-Alive-begäranden

**Syfte:** Definierar det maximala antalet begär Anden för en Keep-Alive-anslutning innan den stängs.

Att ställa in maximalt antal begär anden till ett lågt värde rekommenderas inte och kan leda till försämrade prestanda.

Viktig information:

- Ange det här värdet som ett heltal.
- Inkludera inte kommatecken eller punkter i det angivna värdet.

**Standardvärde:** 10 000-begäranden

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Ändra huvudet för klientbegäran

**Syfte:** Varje begäran innehåller en uppsättning begärandehuvuden som beskriver den. Den här funktionen kan antingen:

- Lägg till eller Skriv över värdet som tilldelats ett begär ande huvud. Om det angivna begär ande huvudet inte finns, kommer den här funktionen att lägga till den i begäran.
- Ta bort ett begär ande huvud från begäran.

Begär Anden som vidarebefordras till en ursprungs Server återspeglar de ändringar som gjorts av den här funktionen.

En av följande åtgärder kan utföras i ett begär ande huvud:

Alternativ|Beskrivning|Exempel
-|-|-
Lägg till|Det angivna värdet läggs till i slutet av det befintliga begär ande huvudets värde.|**Värde för begär ande huvud (klient):**<br/>Value1<br/>**Värde för begär ande huvud (regel motor):**<br/>Value2 <br/>**Nytt värde för begär ande huvud:** <br/>Value1Value2
Skriv över|Värdet för begär ande huvudet kommer att anges till det angivna värdet.|**Värde för begär ande huvud (klient):**<br/>Value1<br/>**Värde för begär ande huvud (regel motor):**<br/>Value2<br/>**Nytt värde för begär ande huvud:**<br/> Value2 <br/>
Ta bort|Tar bort det angivna begär ande huvudet.|**Värde för begär ande huvud (klient):**<br/>Value1<br/>**Ändra konfigurationen för klient begär ande huvud:**<br/>Ta bort begär ande rubriken i fråga.<br/>**Medför**<br/>Det angivna begär ande huvudet kommer inte att vidarebefordras till ursprungs servern.

Viktig information:

- Se till att värdet som anges i namn alternativet är en exakt matchning för det önskade begär ande huvudet.
- Det går inte att identifiera ett huvud ärende. Till exempel kan någon av följande variationer i `Cache-Control` rubrik namnet användas för att identifiera det:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Använd endast alfanumeriska tecken, bindestreck eller under streck när du anger ett rubrik namn.
- Att ta bort en rubrik förhindrar att den vidarebefordras till en ursprungs Server av pop.
- Följande rubriker är reserverade och kan inte ändras av den här funktionen:
    - vidarebefordras
    - host
    - rapportör
    - varning
    - x-vidarebefordrad – för
    - Alla huvud namn som börjar med "x-EG" är reserverade.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Ändra huvudet för klient svaret

Varje svar innehåller en uppsättning svarshuvuden som beskriver det. Den här funktionen kan antingen:

- Lägg till eller Skriv över värdet som tilldelats ett svars huvud. Om det angivna svars huvudet inte finns, kommer den här funktionen att lägga till den i svaret.
- Ta bort ett svars huvud från svaret.

Som standard definieras svars huvud värden av en ursprungs Server och av pop.

En av följande åtgärder kan utföras i ett svars huvud:

Alternativ|Beskrivning|Exempel
-|-|-
Lägg till|Det angivna värdet läggs till i slutet av det befintliga svars huvudets värde.|**Svars huvud värde (klient):**<br />Value1<br/>**Svars huvud värde (regel motor):**<br/>Value2<br/>**Nytt värde för svars huvud:**<br/>Value1Value2
Skriv över|Svars huvudets värde kommer att anges till det angivna värdet.|**Svars huvud värde (klient):**<br/>Value1<br/>**Svars huvud värde (regel motor):**<br/>Value2 <br/>**Nytt värde för svars huvud:**<br/>Value2 <br/>
Ta bort|Tar bort det angivna svars huvudet.|**Svars huvud värde (klient):**<br/>Value1<br/>**Ändra konfiguration av klient svars huvud:**<br/>Ta bort svars huvudet i fråga.<br/>**Medför**<br/>Det angivna svars huvudet vidarebefordras inte till beställaren.

Viktig information:

- Se till att värdet som anges i namn alternativet är en exakt matchning för det önskade svars huvudet.
- Det går inte att identifiera ett huvud ärende. Till exempel kan någon av följande variationer i `Cache-Control` rubrik namnet användas för att identifiera det:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Om du tar bort en rubrik så förhindras det från att vidarebefordras till beställaren.
- Följande rubriker är reserverade och kan inte ändras av den här funktionen:
    - accept-encoding
    - tids
    - anslutning
    - content-encoding
    - innehålls längd
    - innehålls intervall
    - date
    - server
    - släp
    - överförings kodning
    - upgrade
    - Vary
    - rapportör
    - varning
    - Alla huvud namn som börjar med "x-EG" är reserverade.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Partiell cache-delning

**Syfte:** Anger om en begäran kan generera delvis cachelagrat innehåll.

Denna partiella cache kan sedan användas för att uppfylla nya begär Anden för innehållet tills det begärda innehållet är fullständigt cachelagrat.

Värde|Resultat
-|-
Enabled|Begär Anden kan generera delvis cachelagrat innehåll.
Disabled|Begär Anden kan bara generera en fullständigt cachelagrad version av det begärda innehållet.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Förvalidera cachelagrat innehåll

**Syfte:** Anger om cachelagrat innehåll kommer att bli kvalificerat för tidig omverifiering innan dess TTL går ut.

Definiera hur lång tid som ska förfalla innan det begärda innehållets TTL-värde under vilken det är berättigat till tidig omverifiering.

Viktig information:

- Om du väljer "av" som tidsenhet måste du göra en omverifiering för att kunna genomföras efter att det cachelagrade innehållets TTL har gått ut. Tiden ska inte anges och ignoreras.

**Standard beteende:** Rund. Det går bara att göra en omverifiering efter att det cachelagrade innehållets TTL har upphört att gälla.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Särskilda rubriker för proxy

**Syfte:** Definierar uppsättningen [Verizon-Specific http-](cdn-verizon-http-headers.md) begärandehuvuden som ska vidarebefordras från en pop-server till en ursprungs Server.

Viktig information:

- Varje CDN-särskilt begär ande huvud som definieras i den här funktionen vidarebefordras till en ursprungs Server. Uteslutna huvuden vidarebefordras inte.
- Om du vill förhindra att ett CDN-särskilt begär ande huvud vidarebefordras tar du bort det från den blankstegsavgränsad listan i rubrik List fältet.

Följande HTTP-huvuden ingår i standard listan:
- Via
- X-Forwarded-For
- X-Forwarded-Proto
- X-värd
- X-Midgress
- X-Gateway-lista
- X-EC-Name
- Värd

**Standard beteende:** Alla CDN-speciella begärandehuvuden kommer att vidarebefordras till ursprungs servern.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Uppdatera cache-filer med noll byte

**Syfte:** Bestämmer hur en HTTP-klientbegäran för en cache-till-0-byte hanteras av pop.

Giltiga värden är:

Värde|Resultat
--|--
Enabled|Gör att POP hämtar till gången från ursprungs servern igen.
Disabled|Återställer standard beteendet. Standard beteendet är att betjäna giltiga cache-tillgångar på begäran.

Den här funktionen krävs inte för korrekt cachelagring och innehålls leverans, men kan vara användbart som en lösning. Till exempel kan dynamiska innehålls generatorer på ursprungs servrar oavsiktligt leda till att 0 byte-svar skickas till pop: ar. De här typerna av svar cachelagras vanligt vis av pop. Om du vet att ett svar på 0 byte aldrig är ett giltigt svar för sådant innehåll, kan den här funktionen förhindra att dessa typer av resurser betjänas till dina klienter.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Ange status koder för cachelagring

**Syfte:** Definierar den uppsättning med status koder som kan leda till cachelagrat innehåll.

Som standard aktive ras cachelagring endast för 200 OK-svar.

Definiera en blankstegsavgränsad uppsättning av önskade status koder.

Viktig information:

- Aktivera funktionen ignorera ursprung no-cache. Om den här funktionen inte är aktive rad får inte några OK-svar som inte är 200 cachelagras.
- Uppsättningen med giltiga status koder för den här funktionen är: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500 och 501.
- Den här funktionen kan inte användas för att inaktivera cachelagring för svar som genererar en status kod på 200 OK.

**Standard beteende:** Cachelagring är bara aktiverat för svar som genererar en status kod på 200 OK.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Ange IP-adress för anpassade klienter

**Syfte:** Lägger till en anpassad rubrik som identifierar den begär ande klienten via IP-adress till begäran.

Alternativet rubrik namn definierar namnet på det anpassade begär ande huvud där klientens IP-adress lagras.

Med den här funktionen kan en kund ursprungs server ta reda på klientens IP-adresser via en anpassad begäran-rubrik. Om begäran hanteras från cachen informeras inte ursprungs servern om klientens IP-adress. Vi rekommenderar därför att den här funktionen används med till gångar som inte cachelagras.

Se till att det angivna huvud namnet inte matchar något av följande namn:

- Rubrik namn för standard begär Ande. Du hittar en lista över standard huvud namn i [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Reserverade huvud namn:
    - vidarebefordrad – för
    - host
    - Vary
    - rapportör
    - varning
    - x-vidarebefordrad – för
    - Alla huvud namn som börjar med "x-EG" är reserverade.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Inaktuell innehålls leverans vid fel

**Syfte:** Anger om förfallet cachelagrat innehåll ska levereras när ett fel uppstår under cache-omverifieringen eller när det begärda innehållet hämtas från kund ursprungs servern.

Värde|Resultat
-|-
Enabled|Inaktuellt innehåll hanteras till beställaren när ett fel uppstår under en anslutning till en ursprungs Server.
Disabled|Ursprungs serverns fel vidarebefordras till beställaren.

**Standard beteende:** Inaktiverats

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Inaktuell vid omverifiering

**Syfte:** Förbättrar prestandan genom att tillåta pop att betjäna inaktuellt innehåll till beställaren när omverifieringen sker.

Viktig information:

- Beteendet för den här funktionen varierar beroende på vald tidsenhet.
    - **Tidsenhet:** Ange en tids längd och välj en tidsenhet (till exempel sekunder, minuter, timmar osv.) för att tillåta inaktuell innehålls leverans. Den här typen av installation tillåter CDN att förlänga den tids längd som den kan leverera innehåll innan verifiering krävs enligt följande formel: **TTL** + **inaktuell vid Omvaliderings tid**
    - **Av:** Välj "off" om du vill kräva omverifiering innan en begäran om inaktuellt innehåll kan hanteras.
        - Ange ingen tids längd eftersom den inte är tillämplig och kommer att ignoreras.

**Standard beteende:** Rund. Omverifiering måste ske innan det begärda innehållet kan hanteras.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token-autentisering

**Syfte:** Anger om tokenbaserad autentisering ska tillämpas på en begäran.

Om tokenbaserad autentisering är aktive rad kommer endast begär Anden som tillhandahåller en krypterad token och som uppfyller de krav som anges av denna token att respekteras.

Krypterings nyckeln som används för att kryptera och dekryptera token-värden bestäms av primär nyckeln och alternativen för säkerhets kopiering på sidan token auth. Tänk på att krypterings nycklarna är plattformsspecifika.

**Standard beteende:** Inaktiverats.

Den här funktionen har företräde framför de flesta funktioner med undantag för funktionen för URL-omskrivning.

Värde | Resultat
------|---------
Enabled | Skyddar det begärda innehållet med tokenbaserad autentisering. Endast begär Anden från klienter som tillhandahåller en giltig token och uppfyller dess krav kommer att följas. FTP-transaktioner undantas från tokenbaserad autentisering.
Disabled| Återställer standard beteendet. Standard beteendet är att tillåta din token Authentication-konfiguration att avgöra om en begäran kommer att skyddas.

#### <a name="compatibility"></a>Kompatibilitet

Använd inte token auth med villkoret Always Always.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Nekad token-auktoriseringskod

**Syfte:** Bestämmer vilken typ av svar som ska returneras till en användare när en begäran nekas på grund av tokenbaserad autentisering.

De tillgängliga svars koderna visas i följande tabell.

Svarskod|Svars namn|Beskrivning
-------------|-------------|--------
301|Flyttad permanent|Den här status koden omdirigerar obehöriga användare till den URL som anges i plats rubriken.
302|Kon|Den här status koden omdirigerar obehöriga användare till den URL som anges i plats rubriken. Den här status koden är bransch standard metoden för att utföra en omdirigering.
307|Tillfällig omdirigering|Den här status koden omdirigerar obehöriga användare till den URL som anges i plats rubriken.
401|Behörighet saknas|Genom att kombinera denna status kod med WWW-autentisera svars huvudet kan du uppmana en användare att autentisera.
403|Förbjudet|Det här meddelandet är standard 403 status meddelandet som en obehörig användare kan se när de försöker komma åt skyddat innehåll.
404|Filen hittades inte|Den här status koden anger att HTTP-klienten kunde kommunicera med servern, men det begärda innehållet hittades inte.

#### <a name="compatibility"></a>Kompatibilitet

Använd inte en denial-kod för token-autentisering med villkoret Always match. Använd i stället avsnittet **anpassad Denial-hantering** på sidan **token auth** i **Hantera** -portalen. Mer information finns i [skydda Azure CDN-tillgångar med token-autentisering](cdn-token-auth.md).

#### <a name="url-redirection"></a>URL-omdirigering

Den här funktionen stöder URL-omdirigering till en användardefinierad URL när den har kon figurer ATS för att returnera en 3xx status kod. Den här användardefinierade URL: en kan anges genom att utföra följande steg:

1. Välj en 3xx-svarskod för den nekade funktionen för nekad token-kod.
2. Välj "plats" från alternativet valfritt rubrik namn.
3. Ställ in det valfria rubrik värde alternativet på önskad URL.

Om ingen URL har definierats för en status kod för 3xx, returneras standard svars sidan för en 3xx-statuskod till användaren.

URL-omdirigering gäller endast för 3xx-svars koder.

Alternativet valfritt rubrik värde stöder alfanumeriska tecken, citat tecken och blank steg.

#### <a name="authentication"></a>Autentisering

Den här funktionen har stöd för möjligheten att inkludera WWW-autentisera-huvudet vid svar på en otillåten begäran om innehåll som skyddas av tokenbaserad autentisering. Om WWW-autentisera-sidhuvudet har angetts till "grundläggande" i konfigurationen, uppmanas den obehöriga användaren att ange autentiseringsuppgifter för kontot.

Konfigurationen ovan kan uppnås genom att utföra följande steg:

1. Välj "401" som svars kod för den nekade funktionen för token-autentisering.
2. Välj "WWW-autentisera" från alternativet valfritt rubrik namn.
3. Ange alternativet för valfritt rubrik värde till "grundläggande".

WWW-autentisera-huvudet gäller endast för 401-svars koder.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Ignorera URL-fall för token auth

**Syfte:** Anger om URL-jämförelser gjorda av tokenbaserad autentisering är Skift läges känsliga.

De parametrar som påverkas av den här funktionen är:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Giltiga värden är:

Värde|Resultat
---|----
Enabled|Gör att POP ignorerar Skift läge när URL: er för token-baserade autentiseringsmetoder jämförs.
Disabled|Återställer standard beteendet. Standard beteendet är för URL-jämförelser för att token-autentisering ska vara Skift läges känslig.

**Standard beteende:** Inaktiverats.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Parameter för token-autentisering

**Syfte:** Anger om den token-baserade autentiseringens frågesträngs parameter ska byta namn.

Viktig information:

- Alternativet Value definierar parameter namnet för frågesträngen som en token kan anges genom.
- Alternativet Value kan inte anges till "ec_token".
- Kontrol lera att namnet som definierats i värde-alternativet bara innehåller giltiga URL-tecken.

Värde|Resultat
----|----
Enabled|Alternativet Value definierar parameter namnet för frågesträngen genom vilka tokens ska definieras.
Disabled|En token kan anges som en odefinierad frågesträng i fråge-URL: en.

**Standard beteende:** Inaktiverats. En token kan anges som en odefinierad frågesträng i fråge-URL: en.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL-omdirigering

**Syfte:** Omdirigerar begär Anden via plats rubriken.

Konfigurationen av den här funktionen kräver att du ställer in följande alternativ:

Alternativ|Beskrivning
-|-
Kod|Välj den svarskod som ska returneras till beställaren.
Käll &s mönster| Dessa inställningar definierar ett URI-mönster för begäran som identifierar den typ av begär Anden som kan omdirigeras. Endast begär Anden vars URL uppfyller båda följande villkor kommer att omdirigeras: <br/> <br/> **Källa (eller innehålls åtkomst punkt):** Välj en relativ sökväg som identifierar en ursprungs Server. Den här sökvägen är _/xxxx/_ -avsnittet och slut punktens namn. <br/><br/> **Källa (mönster):** Ett mönster som identifierar begär Anden efter relativ sökväg måste definieras. Detta reguljära uttrycks mönster måste definiera en sökväg som startar direkt efter den tidigare valda innehålls åtkomst punkten (se ovan). <br/> – Kontrol lera att URI-kriterierna för begäran (det vill säga käll & mönster) som tidigare definierats inte hamnar i konflikt med eventuella matchnings villkor som definierats för den här funktionen. <br/> -Ange ett mönster; Om du använder ett tomt värde som mönster matchas alla strängar.
Mål| Definiera den URL som ovanstående begär Anden ska omdirigeras till. <br/><br/> Skapa denna URL dynamiskt med: <br/> – Ett mönster för reguljära uttryck <br/>- [http-variabler](cdn-http-variables.md) <br/><br/> Ersätt värdena som anges i käll mönstret i mål mönstret med hjälp av $_n_ där _n_ identifierar ett värde i den ordning som det fångades. $1 representerar till exempel det första värdet som samlas in i käll mönstret, medan $2 representerar det andra värdet. <br/>

Vi rekommenderar starkt att du använder en absolut URL. Användning av en relativ URL kan omdirigera CDN-URL: er till en ogiltig sökväg.

**Exempel scenario**

Det här exemplet visar hur du omdirigerar en kant-CNAME-URL som matchar den här grundläggande CDN-URL: en: http:\//marketing.azureedge.net/brochures

Kvalificerings begär Anden kommer att omdirigeras till denna grundläggande CNAME-URL: http:\//cdn.mydomain.com/resources

Den här URL-omdirigeringen kan uppnås genom följande konfiguration: ![URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Nyckel punkter:**

- Funktionen URL-omdirigering definierar de URL: er för begäran som ska omdirigeras. Det innebär att ytterligare matchnings villkor inte krävs. Även om matchnings villkoret har definierats som "Always", kommer endast begär Anden som pekar på mappen "broschyrer" på kund ursprunget "marknadsföring" att omdirigeras.
- Alla matchande begär Anden kommer att omdirigeras till den gräns CNAME-URL som definierats i mål alternativet.
    - Exempel scenario #1:
        - Exempel förfrågan (CDN-URL): http:\//marketing.azureedge.net/brochures/widgets.pdf
        - URL för begäran (efter omdirigering): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Exempel scenario #2:
        - Exempel förfrågan (Edge CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf
        - URL för begäran (efter omdirigering): http:\//cdn.mydomain.com/resources/widgets.pdf exempel scenario
    - Exempel scenario #3:
        - Exempel förfrågan (Edge CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - URL för begäran (efter omdirigering): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- Variabeln Request schema (% {Scheme}) används i alternativet mål, vilket säkerställer att begärans schema förblir oförändrat efter omdirigering.
- URL-segmenten som fångades från begäran läggs till i den nya URL: en via "$1".

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL-omskrivning

**Syfte:** Skriver om URL: en för begäran.

Viktig information:

- Konfigurationen av den här funktionen kräver att du ställer in följande alternativ:

Alternativ|Beskrivning
-|-
 Käll &s mönster | Dessa inställningar definierar ett URI-mönster för begäran som identifierar den typ av begär Anden som kan skrivas om. Endast begär Anden vars URL uppfyller båda följande kriterier skrivs om: <br/><br/>  - **källa (eller innehålls åtkomst punkt):** Välj en relativ sökväg som identifierar en ursprungs Server. Den här sökvägen är _/xxxx/_ -avsnittet och slut punktens namn. <br/><br/> - **källa (mönster):** ett mönster som identifierar begär Anden efter relativ sökväg måste definieras. Detta reguljära uttrycks mönster måste definiera en sökväg som startar direkt efter den tidigare valda innehålls åtkomst punkten (se ovan). <br/> Kontrol lera att URI-kriterierna för begäran (det vill säga käll & mönster) som tidigare definierats inte hamnar i konflikt med något av matchnings villkoren som definierats för den här funktionen. Ange ett mönster. Om du använder ett tomt värde som mönster matchas alla strängar.
 Mål  |Definiera den relativa URL som ovanstående begär Anden kommer att skrivas om av: <br/>    1. Välj en innehålls åtkomst punkt som identifierar en ursprungs Server. <br/>    2. definiera en relativ sökväg med: <br/>        – Ett mönster för reguljära uttryck <br/>        - [http-variabler](cdn-http-variables.md) <br/> <br/> Ersätt värdena som anges i käll mönstret i mål mönstret med hjälp av $_n_ där _n_ identifierar ett värde i den ordning som det fångades. $1 representerar till exempel det första värdet som samlas in i käll mönstret, medan $2 representerar det andra värdet.

 Med den här funktionen kan pop skriva om URL: en utan att utföra en traditionell omdirigering. Det vill säga att beställaren tar emot samma svarskod som om den omskrivna URL: en har begärts.

**Exempel scenario 1**

Det här exemplet visar hur du omdirigerar en kant-CNAME-URL som matchar den här grundläggande CDN-URL: en: http:\//marketing.azureedge.net/brochures/

Kvalificerings begär Anden kommer att omdirigeras till denna grundläggande CNAME-URL: http:\//MyOrigin.azureedge.net/resources/

Den här URL-omdirigeringen kan uppnås genom följande konfiguration: ![URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Exempel scenario 2**

Det här exemplet visar hur du omdirigerar en kant-CNAME-URL från VERSALer till gemener med reguljära uttryck.

Den här URL-omdirigeringen kan uppnås genom följande konfiguration: ![URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Nyckel punkter:**

- Funktionen för URL-omskrivning definierar de URL: er för begäran som kommer att skrivas om. Det innebär att ytterligare matchnings villkor inte krävs. Även om matchnings villkoret har definierats som "Always", kommer endast begär Anden som pekar på mappen "broschyrer" på kund ursprunget för marknadsföring att skrivas om.

- URL-segmenten som fångades från begäran läggs till i den nya URL: en via "$1".

#### <a name="compatibility"></a>Kompatibilitet

Den här funktionen innehåller matchande villkor som måste uppfyllas innan den kan användas för en begäran. Den här funktionen är inte kompatibel med följande matchnings villkor för att förhindra att matchnings villkor konfigureras i konflikt:

- SOM nummer
- CDN-ursprung
- Klientens IP-adress
- Kund ursprung
- Begär ande schema
- Katalog för URL-sökväg
- URL Path-tillägg
- URL-sökväg filename
- URL-sökväg literal
- URL-sökväg regex
- Jokertecken för URL-sökväg
- URL-frågans litteral
- Parameter för URL-fråga
- URL-fråga regex
- Jokertecken för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Användar variabel

**Syfte:** Endast för internt bruk.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Nästa steg

- [Regelmotor – referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – matchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Åsidosätt HTTP-beteende med regel motorn](cdn-verizon-premium-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)