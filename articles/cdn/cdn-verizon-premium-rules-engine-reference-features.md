---
title: Azure CDN från Verizon Premium regler motorfunktioner | Microsoft-dokument
description: Referensdokumentation för Azure CDN från Verizon Premium regler motorfunktioner.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 373e7838327d11b1b54278ee0c16c6e6ae554b0b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253500"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN från Verizon Premium regler motorfunktioner

I den här artikeln visas detaljerade beskrivningar av tillgängliga [Rules Engine](cdn-verizon-premium-rules-engine.md)funktioner för CDN-regler (Azure Content Delivery Network) .

Den tredje delen av en regel är funktionen. En funktion definierar vilken typ av åtgärd som tillämpas på den begärandetyp som identifieras av en uppsättning matchningsvillkor.

## <a name="access-features"></a>Åtkomstfunktioner

Dessa funktioner är utformade för att styra åtkomsten till innehåll.

Namn | Syfte
-----|--------
[Neka åtkomst (403)](#deny-access-403) | Avgör om alla begäranden avvisas med ett 403 Förbjudet svar.
[Token Auth](#token-auth) | Avgör om tokenbaserad autentisering tillämpas på en begäran.
[Avslagskod för tokenauth](#token-auth-denial-code) | Bestämmer vilken typ av svar som returneras till en användare när en begäran nekas på grund av tokenbaserad autentisering.
[Url-ärende för tokenauth ignorera](#token-auth-ignore-url-case) | Avgör om URL-jämförelser som görs av tokenbaserad autentisering är skiftlägeskänsliga.
[Parametern Token Auth](#token-auth-parameter) | Avgör om frågesträngparametern Token-baserad autentisering ska döpas om.

## <a name="caching-features"></a>Cachelagringsfunktioner

Dessa funktioner är utformade för att anpassa när och hur innehållet cachelagras.

Namn | Syfte
-----|--------
[Bandbredd Parametrar](#bandwidth-parameters) | Avgör om parametrar för begränsning av bandbredd (t.ex. ec_rate och ec_prebuf) är aktiva.
[Bandbreddsbegränsning](#bandwidth-throttling) | Begränsar bandbredden för svaret från point-of-presence (POP).
[Bypass-cache](#bypass-cache) | Avgör om begäran ska kringgå cachelagring.
[Cache-kontroll header behandling](#cache-control-header-treatment) | Styr genereringen av `Cache-Control` rubriker av POP när funktionen Extern maxålder är aktiv.
[Frågesträng för cachetangent](#cache-key-query-string) | Avgör om cachetangenten innehåller eller utesluter frågesträngparametrar som är associerade med en begäran.
[Cache-nyckel skriva](#cache-key-rewrite) | Skriver om cachenyckeln som är associerad med en begäran.
[Fullständig cachefyllning](#complete-cache-fill) | Avgör vad som händer när en begäran resulterar i en partiell cachemiss på en POP.
[Komprimera filtyper](#compress-file-types) | Definierar filformaten för de filer som komprimeras på servern.
[Standardintern maxålder](#default-internal-max-age) | Bestämmer standardintervallet för maxålder för POP-till-ursprungsservercachens omvalidering.
[Upphör att gälla rubrikbehandling](#expires-header-treatment) | Styr genereringen av `Expires` rubriker med en POP när funktionen Extern maxålder är aktiv.
[Extern maxålder](#external-max-age) | Bestämmer intervallet maxålder för webbläsare till POP-cacheåtervalidation.
[Tvinga inre max-ålder](#force-internal-max-age) | Bestämmer intervallet för maxålder för POP till ursprungsservercachen.
[H.264-stöd (HTTP Progressiv nedladdning)](#h264-support-http-progressive-download) | Bestämmer vilka typer av H.264-filformat som kan användas för att strömma innehåll.
[Begäran om att inte uppfylla no-cache](#honor-no-cache-request) | Avgör om en HTTP-klients no-cache-begäranden vidarebefordras till ursprungsservern.
[Ignorera ursprungsnr cache](#ignore-origin-no-cache) | Avgör om CDN ignorerar vissa direktiv som visas från en ursprungsserver.
[Ignorera otåliga intervall](#ignore-unsatisfiable-ranges) | Bestämmer svaret som returneras till klienter när en begäran genererar en 416 Begärd statuskod för begärt intervall.
[Intern Max-inaktuella](#internal-max-stale) | Styr hur länge efter den normala förfallotiden en cachelagrad tillgång kan visas från en POP när POP inte kan förnya den cachelagrade tillgången med ursprungsservern.
[Partiell cachedelning](#partial-cache-sharing) | Avgör om en begäran kan generera delvis cachelagrat innehåll.
[Förvalidat cachelande innehåll](#prevalidate-cached-content) | Avgör om cachelagrat innehåll är kvalificerat för tidig förlängning innan dess TTL upphör att gälla.
[Uppdatera cachefiler med noll byte](#refresh-zero-byte-cache-files) | Bestämmer hur en HTTP-klients begäran om en cachetillgång med 0 byte hanteras av POP:erna.
[Ange cacherbara statuskoder](#set-cacheable-status-codes) | Definierar den uppsättning statuskoder som kan resultera i cachelagrat innehåll.
[Inaktuell innehållsleverans vid fel](#stale-content-delivery-on-error) | Avgör om utgånget cachelagrat innehåll levereras när ett fel inträffar under cacheåtervalidationen eller när det begärda innehållet hämtas från kundens ursprungsserver.
[Inaktuella medan Revalidate](#stale-while-revalidate) | Förbättrar prestanda genom att låta POP:erna att betjäna den inaktuella klienten till beställaren medan förlängning sker.

## <a name="comment-feature"></a>Kommentarsfunktion

Den här funktionen är utformad för att ge ytterligare information i en regel.

Namn | Syfte
-----|--------
[Kommentar](#comment) | Gör att en anteckning kan läggas till inom en regel.

## <a name="header-features"></a>Huvudfunktioner

Dessa funktioner är utformade för att lägga till, ändra eller ta bort rubriker från begäran eller svaret.

Namn | Syfte
-----|--------
[Rubrik för ålderssvar](#age-response-header) | Avgör om ett age-svarshuvud ingår i svaret som skickas till beställaren.
[Felsökning av cachesvarsrubriker](#debug-cache-response-headers) | Avgör om ett svar kan innehålla X-EC-Debug-svarshuvudet, som innehåller information om cacheprincipen för den begärda tillgången.
[Ändra klientbegäranshuvud](#modify-client-request-header) | Skriver över, lägger till eller tar bort ett huvud från en begäran.
[Ändra klientsvarshuvud](#modify-client-response-header) | Skriver över, lägger till eller tar bort ett sidhuvud från ett svar.
[Ange anpassad klient-IP-huvud](#set-client-ip-custom-header) | Gör att IP-adressen för den begärande klienten kan läggas till i begäran som ett anpassat begärandehuvud.

## <a name="logging-features"></a>Loggningsfunktioner

Dessa funktioner är utformade för att anpassa data som lagras i råloggfiler.

Namn | Syfte
-----|--------
[Anpassat loggfält 1](#custom-log-field-1) | Bestämmer formatet och innehållet som tilldelas det anpassade loggfältet i en rå loggfil.
[Loggfrågesträng](#log-query-string) | Avgör om en frågesträng lagras tillsammans med URL:en i åtkomstloggar.


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

## <a name="origin-features"></a>Origin-funktioner

Dessa funktioner är utformade för att styra hur CDN kommunicerar med en ursprungsserver.

Namn | Syfte
-----|--------
[Maximala keep-alive-begäranden](#maximum-keep-alive-requests) | Definierar det maximala antalet begäranden för en Keep-Alive-anslutning innan den stängs.
[Särskilda proxyrubriker](#proxy-special-headers) | Definierar uppsättningen CDN-specifika begäranderubriker som vidarebefordras från en POP till en ursprungsserver.

## <a name="specialty-features"></a>Specialfunktioner

Dessa funktioner ger avancerade funktioner för avancerade användare.

Namn | Syfte
-----|--------
[Cachelagbara HTTP-metoder](#cacheable-http-methods) | Bestämmer vilken uppsättning ytterligare HTTP-metoder som kan cachelagras i nätverket.
[Brödstorlek för cachelagbar begäran](#cacheable-request-body-size) | Definierar tröskelvärdet för att avgöra om ett POST-svar kan cachelagras.
[Användarvariabel](#user-variable) | Endast för internt bruk.

## <a name="url-features"></a>URL-funktioner

Dessa funktioner gör att en begäran kan omdirigeras eller skrivas om till en annan webbadress.

Namn | Syfte
-----|--------
[Följ omdirigeringar](#follow-redirects) | Avgör om begäranden kan omdirigeras till det värdnamn som definierats i platshuvudet som returneras av en kundursprungsserver.
[Url-omdirigering](#url-redirect) | Omdirigerar begäranden via platshuvudet.
[URL-omskrivning](#url-rewrite)  | Skriver om url:en för begäran.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Azure CDN från Verizon Premium regler motorfunktioner referens

---

### <a name="age-response-header"></a>Rubrik för ålderssvar

**Syfte**: Avgör om ett age-svarshuvud ingår i svaret som skickas till beställaren.

Värde|Resultat
--|--
Enabled | Ålderssvarshuvudet ingår i svaret som skickas till beställaren.
Disabled | Svarshuvudet för ålder utesluts från svaret som skickas till beställaren.

**Standardbeteende:** Inaktiverat.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Bandbredd Parametrar

**Syfte:** Avgör om parametrar för begränsning av bandbredd (t.ex. ec_rate och ec_prebuf) är aktiva.

Parametrar för bandbreddsbegränsning avgör om dataöverföringshastigheten för en klients begäran är begränsad till en anpassad hastighet.

Värde|Resultat
--|--
Enabled|Gör att POP:er kan uppfylla begäranden om bandbreddsbegränsning.
Disabled|Gör att POP:erna ignorerar parametrar för begränsning av bandbredd. Det begärda innehållet visas normalt (det vill säga utan bandbreddsbegränsning).

**Standardbeteende:** Aktiverat.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Bandbreddsbegränsning

**Syfte:** Begränsar bandbredden för svaret från POP.Throttles the bandwidth for the response provided by the POP.

Båda följande alternativ måste definieras för att korrekt ställa in bandbreddsbegränsning.

Alternativ|Beskrivning
--|--
Kbyte per sekund|Ange det här alternativet till den maximala bandbredd (Kb per sekund) som kan användas för att leverera svaret.
Prebuf sekunder|Ange det här alternativet till antalet sekunder för POP att vänta tills bandbredden begränsas. Syftet med denna tidsperiod med obegränsad bandbredd är att förhindra att en mediespelare upplever stamning eller buffring problem på grund av bandbredd strypning.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Bypass-cache

**Syfte:** Avgör om begäran ska kringgå cachelagring.

Värde|Resultat
--|--
Enabled|Gör att alla begäranden faller igenom till ursprungsservern, även om innehållet tidigare cachelagrats på POP.Causes all requests to fall through to the origin server, even if the content was previously cached on POPs.
Disabled|Gör att POP-adresser cachelagrar tillgångar enligt cacheprincipen som definierats i svarshuvudena.

**Standardbeteendet:**

- **HTTP-stor:** Inaktiverad

<!---
- **ADN:** Enabled

--->

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Cachelagbara HTTP-metoder

**Syfte:** Bestämmer vilken uppsättning ytterligare HTTP-metoder som kan cachelagras i nätverket.

Viktig information:

- Den här funktionen förutsätter att GET-svar alltid ska cachelagras. Därför bör GET HTTP-metoden inte inkluderas när den här funktionen anges.
- Den här funktionen stöder endast POST HTTP-metoden. Aktivera POST-svarscachelagring genom `POST`att ställa in den här funktionen på .
- Som standard cachelagras endast begäranden vars brödtext är mindre än 14 kB. Använd funktionen Cacheable Request Body Size för att ange den maximala kroppsstorleken för begäran.

**Standardbeteende:** Endast GET-svar cachelagras.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Brödstorlek för cachelagbar begäran

**Syfte:** Definierar tröskelvärdet för att avgöra om ett POST-svar kan cachelagras.

Detta tröskelvärde bestäms genom att ange en maximal kroppsstorlek för begäran. Begäranden som innehåller en större begärandetext cachelagras inte.

Viktig information:

- Den här funktionen gäller endast när POST-svar är berättigade till cachelagring. Använd funktionen Cacheable HTTP Methods för att aktivera CACHELAGRING av POST-begäran.
- Organet för begäran beaktas för
    - x-www-form-urlencoded värden
    - Säkerställa en unik cachenyckel
- Om du definierar en stor maximal mängd för begäran kan dataleveransprestanda påverkas.
    - **Rekommenderat värde:** 14 kb
    - **Minsta värde:** 1 kb

**Standardbeteende:** 14 kb

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Cache-kontroll header behandling

**Syfte:** Styr genereringen av `Cache-Control` rubriker av POP när funktionen Extern maxålder är aktiv.

Det enklaste sättet att uppnå den här typen av konfiguration är att placera funktionerna Extern maxålder och cachekontrollhuvudbehandling i samma sats.

Värde|Resultat
--|--
Skriv över|Säkerställer att följande åtgärder inträffar:<br/> - Skriver över `Cache-Control` huvudet som genereras av ursprungsservern. <br/>- Lägger till `Cache-Control` huvudet som produceras av funktionen Extern maxålder i svaret.
Passera genom|Säkerställer att `Cache-Control` huvudet som produceras av funktionen Extern maxålder aldrig läggs till i svaret. <br/> Om ursprungsservern `Cache-Control` skapar ett huvud skickas den vidare till slutanvändaren. <br/> Om ursprungsservern inte `Cache-Control` skapar något huvud kan det här alternativet `Cache-Control` leda till att svarshuvudet inte innehåller ett huvud.
Lägg till om saknas|Om `Cache-Control` ett huvud inte togs emot från ursprungsservern läggs det här alternativet till huvudet `Cache-Control` som produceras av funktionen Extern maxålder. Det här alternativet är användbart för `Cache-Control` att säkerställa att alla tillgångar tilldelas ett huvud.
Ta bort| Det här alternativet `Cache-Control` säkerställer att ett huvud inte ingår i sidhuvudssvaret. Om `Cache-Control` ett huvud redan har tilldelats tas det bort från sidhuvudssvaret.

**Standardbeteende:** Skriv över.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Frågesträng för cachetangent

**Syfte:** Avgör om cachetangenten innehåller eller utesluter frågesträngparametrar som är associerade med en begäran.

Viktig information:

- Ange ett eller flera frågesträngparameternamn och avgränsa varje parameternamn med ett enda blanksteg.
- Den här funktionen avgör om frågesträngparametrar inkluderas eller utesluts från cachenyckeln. Ytterligare information finns för varje alternativ i följande tabell.

Typ|Beskrivning
--|--
 Inkludera|  Anger att varje angiven parameter ska inkluderas i cachenyckeln. En unik cachenyckel genereras för varje begäran som innehåller ett unikt värde för en frågesträngparameter som definierats i den här funktionen.
 Inkludera alla  |Anger att en unik cachenyckel skapas för varje begäran till en tillgång som innehåller en unik frågesträng. Den här typen av konfiguration rekommenderas vanligtvis inte eftersom det kan leda till en liten procentandel av cacheträffarna. Ett lågt antal cacheträffar ökar belastningen på ursprungsservern, eftersom den måste hantera fler begäranden. Den här konfigurationen duplicerar cachelagringsbeteendet som kallas "unik cache" på sidan Frågesträngcache.
 Exclude | Anger att endast de angivna parametrarna är undantagna från cachetangenten. Alla andra frågesträngparametrar ingår i cachenyckeln.
 Uteslut alla  |Anger att alla frågesträngparametrar är undantagna från cachetangenten. Den här konfigurationen duplicerar standardcachelagringsbeteendet för standardcache på sidan Frågesträngcache.  

Med regelmotorn kan du anpassa det sätt på vilket frågesträngcachening implementeras. Du kan till exempel ange att frågesträngcachelagring endast utförs på vissa platser eller filtyper.

Om du vill duplicera frågesträngens cachelagringsbeteende utan cache på sidan Frågesträngcache skapar du en regel som innehåller ett matchningsvillkor för URL Query-jokertecken och en bypass-cache-funktion. Ange matchningsvillkoret för URL Query-jokertecken till en asterisk (*).

>[!IMPORTANT]
> Om tokenauktorisering är aktiverat för en sökväg för det här kontot är standardcacheläget det enda läge som kan användas för frågesträngcachelagring. Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Exempel på scenarier

Följande exempelanvändning för den här funktionen innehåller en exempelbegäran och standardcachen:

- **Exempel på begäran:** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **Standardcachetangent:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Inkludera

Exempelkonfiguration:

- **Typ:** Inkluderar
- **Parametrar:** språk

Den här typen av konfiguration genererar följande cachenyckel för frågesträngparameter:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Inkludera alla

Exempelkonfiguration:

- **Typ:** Inkludera alla

Den här typen av konfiguration genererar följande cachenyckel för frågesträngparameter:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Exempelkonfiguration:

- **Typ:** Utesluta
- **Parametrar:** sessionerad userid

Den här typen av konfiguration genererar följande cachenyckel för frågesträngparameter:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Uteslut alla

Exempelkonfiguration:

- **Typ:** Uteslut alla

Den här typen av konfiguration genererar följande cachenyckel för frågesträngparameter:

    /800001/Origin/folder/asset.htm

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Cache-nyckel skriva

**Syfte:** Skriver om cachenyckeln som är associerad med en begäran.

En cachenyckel är den relativa sökväg som identifierar en tillgång för cachelagring. Med andra ord söker servrarna efter en cachelagrad version av en tillgång enligt dess sökväg enligt dess cache-nyckel.

Konfigurera den här funktionen genom att definiera båda följande alternativ:

Alternativ|Beskrivning
--|--
Ursprunglig bana| Definiera den relativa sökvägen till de typer av begäranden vars cachenyckel skrivs om. En relativ sökväg kan definieras genom att välja en basursprungssökväg och sedan definiera ett mönster för reguljärt uttryck.
Ny bana|Definiera den relativa sökvägen för den nya cachetangenten. En relativ sökväg kan definieras genom att välja en basursprungssökväg och sedan definiera ett mönster för reguljärt uttryck. Den här relativa sökvägen kan konstrueras dynamiskt med hjälp av [HTTP-variabler](cdn-http-variables.md).

**Standardbeteende:** En begärans cachenyckel bestäms av URI-begäran.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Kommentar

**Syfte:** Gör att en anteckning kan läggas till inom en regel.

En användning för den här funktionen är att ge ytterligare information om det allmänna syftet med en regel eller varför ett visst matchningsvillkor eller en viss funktion har lagts till i regeln.

Viktig information:

- Högst 150 tecken kan anges.
- Använd endast alfanumeriska tecken.
- Den här funktionen påverkar inte regelns beteende. Det är bara tänkt att tillhandahålla ett område där du kan ge information för framtida referens eller som kan hjälpa vid felsökning av regeln.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Fullständig cachefyllning

**Syfte:** Avgör vad som händer när en begäran resulterar i en partiell cachemiss på en POP.

En partiell cache miss beskriver cache status för en tillgång som inte har hämtats helt till en POP. Om en tillgång endast delvis cachelagras på en POP, vidarebefordras nästa begäran för den tillgången igen till ursprungsservern.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
En partiell cachemiss inträffar vanligtvis när en användare avbryter en hämtning eller för tillgångar som endast begärs med hjälp av HTTP-intervallbegäranden. Den här funktionen är mest användbar för stora resurser som vanligtvis inte hämtas från början till (till exempel videor). Därför är den här funktionen aktiverad som standard på HTTP Large-plattformen. Det är inaktiverat på alla andra plattformar.

Behåll standardkonfigurationen för HTTP Large-plattformen, eftersom den minskar belastningen på kundens ursprungsserver och ökar hastigheten med vilken dina kunder hämtar ditt innehåll.

Värde|Resultat
--|--
Enabled|Återställer standardbeteendet. Standardbeteendet är att tvinga POP att initiera en bakgrundshämtning av tillgången från ursprungsservern. Därefter kommer tillgången att finnas i POP:s lokala cache.
Disabled|Förhindrar att en POP utför en bakgrundshämtning för tillgången. Resultatet är att nästa begäran för den tillgången från den regionen gör att en POP begär den från kundens ursprungsserver.

**Standardbeteende:** Aktiverat.

#### <a name="compatibility"></a>Kompatibilitet

På grund av hur cacheinställningar spåras kan den här funktionen inte associeras med följande matchningsvillkor:

- AS-nummer
- Klientens IP-adress
- Cookie Parameter
- Regex för cookieparameter
- Land/region
- Enhet
- Microsoft Edge Cname
- Refererande domän
- Literal för begär rubrik
- Begär rubrik Regex
- Jokertecken för begäran om huvud
- Metod för begäran
- Schema för begäran
- URL-fråga litteral
- URL-fråga Regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Komprimera filtyper

**Syfte:** Definierar filformaten för de filer som komprimeras på servern.

Ett filformat kan anges med hjälp av internetmedietypen (till exempel Innehållstyp). Internet media typ är plattformsoberoende metadata som gör det möjligt för servrarna att identifiera filformatet för en viss tillgång. En lista över vanliga internetmedietyper finns nedan.

Typ av Internet-media|Beskrivning
--|--
text/oformaterad|Oformaterade textfiler
text/html| HTML-filer
text/css|Kaskadformatmallar (CSS)
program/x-javascript|Javascript
program/javascript|Javascript

Viktig information:

- Ange flera Internet-medietyper genom att avgränsa var och en med ett enda blanksteg.
- Den här funktionen komprimerar bara tillgångar vars storlek är mindre än 1 MB. Större tillgångar komprimeras inte av servrarna.
- Vissa typer av innehåll, till exempel bilder, video- och ljudmedietillgångar (till exempel JPG, MP3, MP4 osv.), är redan komprimerade. Eftersom ytterligare komprimering på dessa typer av tillgångar inte minskar filstorleken avsevärt, rekommenderar vi att du inte aktiverar komprimering på dem.
- Jokertecken, till exempel asterisker, stöds inte.
- Innan du lägger till den här funktionen i en regel bör du ange alternativet Komprimering inaktiverad på komprimeringssidan för den plattform som den här regeln tillämpas på.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Anpassat loggfält 1

**Syfte:** Bestämmer formatet och innehållet som ska tilldelas det anpassade loggfältet i en rå loggfil.

Med det här anpassade fältet kan du bestämma vilka värden för begäran och svarshuvud som lagras i loggfilerna.

Som standard kallas det anpassade loggfältet "x-ec_custom-1". Namnet på det här fältet kan anpassas från sidan Rålogginställningar.

Formatet för att ange begärande- och svarsrubriker definieras på följande sätt:

Rubriktyp|Format|Exempel
-|-|-
Begärandehuvud|`%{[RequestHeader]()}[i]()` | %{Acceptera-kodning}i <br/> {Referrer}i <br/> %{Auktorisering}i
Svarshuvud|`%{[ResponseHeader]()}[o]()`| %{Ålder}o <br/> %{Innehållstyp}o <br/> %{Cookie}o

Viktig information:

- Ett anpassat loggfält kan innehålla valfri kombination av rubrikfält och oformaterad text.
- Giltiga tecken för det här fältet är följande: alfanumeriska (0-9, a-z och A-Z), streck, kolon, semikolon, apostrofer, kommatecken, punkter, understreck, likhetstecken, parenteser, parenteser och blanksteg. Procentsymbolen och klammerparenteserna tillåts endast när de används för att ange ett huvudfält.
- Stavningen för varje angivet rubrikfält måste matcha önskat namn på begäran/svarshuvud.
- Om du vill ange flera rubriker använder du en avgränsare för att ange varje rubrik. Du kan till exempel använda en förkortning för varje rubrik:
    - AE: %{Acceptera-kodning}i A: %{Authorization}i CT: %{Content-Type}o

**Standardvärdet:** -

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Felsökning av cachesvarsrubriker

**Syfte:** Avgör om ett svar kan innehålla [X-EC-Debug-svarshuvuden](cdn-http-debug-headers.md), som innehåller information om cacheprincipen för den begärda tillgången.

Debug cache svarsrubriker kommer att ingå i svaret när båda följande är sanna:

- Funktionen Felsökningscachens svarshuvuden har aktiverats på den angivna begäran.
- Den angivna begäran definierar den uppsättning felsökningscachesvarshuvuden som ska inkluderas i svaret.

Felsökningscachens svarshuvuden kan begäras genom att inkludera följande huvud och de angivna direktiven i begäran:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Exempel:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Värde|Resultat
-|-
Enabled|Begäranden om felsökningscachesvarshuvuden returnerar ett svar som innehåller X-EC-Debug-huvudet.
Disabled|X-EC-Debug-svarshuvudet kommer att uteslutas från svaret.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Standardintern maxålder

**Syfte:** Bestämmer standardintervallet för maxålder för POP-till-ursprungsservercachens omvalidering. Med andra ord, den tid som kommer att passera innan en POP kommer att kontrollera om en cachelagd tillgång matchar tillgången lagras på ursprungsservern.

Viktig information:

- Den här åtgärden kommer endast att gälla för svar från en ursprungsserver som inte tilldelar en maxåldersindikering i `Cache-Control` eller `Expires` huvudet.
- Den här åtgärden kommer inte att äga rum för tillgångar som inte anses cachelagras.
- Den här åtgärden påverkar inte webbläsarens till POP-cacheåtervalidering. Dessa typer av förlängningar bestäms `Cache-Control` av `Expires` eller rubriker som skickas till webbläsaren, som kan anpassas med funktionen Extern maxålder.
- Resultatet av den här åtgärden har ingen observerbar effekt på svarsrubrikerna och innehållet som returneras från POP för ditt innehåll, men det kan påverka mängden förlängningstrafik som skickas från POP till din ursprungsserver.
- Konfigurera den här funktionen genom att:
    - Välja den statuskod för vilken en intern standardålder kan tillämpas.
    - Ange ett heltalsvärde och sedan välja önskad tidsenhet (till exempel sekunder, minuter, timmar osv.). Det här värdet definierar standardintervallet för intern maxålder.

- Om du ställer in tidsenheten på "Av" tilldelas ett standardintervall för intern maxålder på 7 `Cache-Control` `Expires` dagar för begäranden som inte har tilldelats en maxåldersindikering i deras eller huvudet.

**Standardvärde:** 7 dagar

#### <a name="compatibility"></a>Kompatibilitet

På grund av hur cacheinställningar spåras kan den här funktionen inte associeras med följande matchningsvillkor:
- AS-nummer
- Klientens IP-adress
- Cookie Parameter
- Regex för cookieparameter
- Land/region
- Enhet
- Kant Cname
- Refererande domän
- Literal för begär rubrik
- Begär rubrik Regex
- Jokertecken för begäran om huvud
- Metod för begäran
- Schema för begäran
- URL-fråga litteral
- URL-fråga Regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Neka åtkomst (403)

**Syfte**: Avgör om alla begäranden avvisas med ett 403 Forbidden-svar.

Värde | Resultat
------|-------
Enabled| Gör att alla begäranden som uppfyller matchningsvillkoren avvisas med ett 403 Förbjudet svar.
Disabled| Återställer standardbeteendet. Standardbeteendet är att låta ursprungsservern bestämma vilken typ av svar som ska returneras.

**Standardbeteende:** Inaktiverat

> [!TIP]
   > En möjlig användning för den här funktionen är att associera den med ett matchningsvillkor för begäranhuvud för att blockera åtkomst till HTTP-länkar som använder infogade länkar till ditt innehåll.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Upphör att gälla rubrikbehandling

**Syfte:** Styr genereringen av `Expires` rubriker med en POP när funktionen Extern maxålder är aktiv.

Det enklaste sättet att uppnå den här typen av konfiguration är att placera funktionerna Extern maxålder och förfallna sidbehandling i samma sats.

Värde|Resultat
--|--
Skriv över|Säkerställer att följande åtgärder kommer att vidtas:<br/>- Skriver över `Expires` huvudet som genereras av ursprungsservern.<br/>- Lägger till `Expires` huvudet som produceras av funktionen Extern maxålder i svaret.
Passera genom|Säkerställer att `Expires` huvudet som produceras av funktionen Extern maxålder aldrig läggs till i svaret. <br/> Om ursprungsservern `Expires` skapar ett huvud går den vidare till slutanvändaren. <br/>Om ursprungsservern inte `Expires` skapar något huvud kan det här alternativet `Expires` leda till att svarshuvudet inte innehåller ett huvud.
Lägg till om saknas| Om `Expires` ett huvud inte togs emot från ursprungsservern läggs det här alternativet till huvudet `Expires` som produceras av funktionen Extern maxålder. Det här alternativet är användbart för att `Expires` säkerställa att alla tillgångar tilldelas ett huvud.
Ta bort| Säkerställer att `Expires` ett huvud inte ingår i sidhuvudssvaret. Om `Expires` ett huvud redan har tilldelats tas det bort från sidhuvudssvaret.

**Standardbeteende:** Skriv över

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Extern maxålder

**Syfte:** Bestämmer intervallet maxålder för webbläsare till POP-cacheåtervalidation. Med andra ord, den tid som kommer att passera innan en webbläsare kan söka efter en ny version av en tillgång från en POP.

Om du aktiverar `Cache-Control: max-age` den `Expires` här funktionen genereras och rubriker från POP:erna och de skickas till HTTP-klienten. Som standard skriver dessa rubriker över de rubriker som skapats av ursprungsservern. Cache-Control Header Treatment och funktionerna Förfaller huvudbehandling kan dock användas för att ändra det här beteendet.

Viktig information:

- Den här åtgärden påverkar inte POP till ursprung server cache förlängningar. Dessa typer av förlängningar bestäms `Cache-Control` av `Expires` och rubriker som tas emot från ursprungsservern och kan anpassas med funktionerna Standard intern maxålder och kraftinterken maxålder.
- Konfigurera den här funktionen genom att ange ett heltalsvärde och välja önskad tidsenhet (till exempel sekunder, minuter, timmar osv.).
- Om du ställer in den här funktionen på `Cache-Control: no-cache` ett `Expires` negativt värde skickas pop-adresser för att skicka en och en tid som har angetts tidigare med varje svar på webbläsaren. Även om en HTTP-klient inte cachelagrar svaret påverkar den här inställningen inte POP:ernas förmåga att cachelagra svaret från ursprungsservern.
- Om du ställer in tidsenheten på "Av" inaktiveras den här funktionen. `Cache-Control` Rubrikerna `Expires` och rubriker som cachelagras med ursprungsserverns svar kommer att gå vidare till webbläsaren.

**Standardbeteende:** Av

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Följ omdirigeringar

**Syfte:** Avgör om begäranden kan omdirigeras till det värdnamn som definierats i platshuvudet som returneras av en kundursprungsserver.

Viktig information:

- Begäranden kan endast omdirigeras till kant-CNAMEs som motsvarar samma plattform.

Värde|Resultat
-|-
Enabled|Begäranden kan omdirigeras.
Disabled|Begäranden omdirigeras inte.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Tvinga inre max-ålder

**Syfte:** Bestämmer intervallet för maxålder för POP till ursprungsservercachen. Med andra ord kan den tid som ska gå innan en POP kontrollera om en cachelagd tillgång matchar tillgången som lagras på ursprungsservern.

Viktig information:

- Den här funktionen åsidosätter det `Cache-Control` intervall `Expires` med maxålder som definierats i eller rubriker som genereras från en ursprungsserver.
- Den här funktionen påverkar inte webbläsare till POP-cacheåtervalidering. Dessa typer av förlängningar bestäms `Cache-Control` av `Expires` de eller rubriker som skickas till webbläsaren.
- Den här funktionen har ingen observerbar effekt på svaret som levereras av en POP till beställaren. Det kan dock påverka mängden förlängningstrafik som skickas från POP till ursprungsservern.
- Konfigurera den här funktionen genom att:
    - Välja den statuskod för vilken en intern maxålder ska tillämpas.
    - Ange ett heltalsvärde och välja önskad tidsenhet (till exempel sekunder, minuter, timmar osv.). Det här värdet definierar begärans intervall för maxålder.

- Om du ställer in tidsenheten på "Av" inaktiveras den här funktionen. Ett internt intervall för maxålder tilldelas inte till begärda tillgångar. Om det ursprungliga huvudet inte innehåller cachelagringsinstruktioner cachelagras tillgången enligt den aktiva inställningen i funktionen Standardintern maxålder.

**Standardbeteende:** Av

#### <a name="compatibility"></a>Kompatibilitet

På grund av hur cacheinställningar spåras kan den här funktionen inte associeras med följande matchningsvillkor:
- AS-nummer
- Klientens IP-adress
- Cookie Parameter
- Regex för cookieparameter
- Land/region
- Enhet
- Kant Cname
- Refererande domän
- Literal för begär rubrik
- Begär rubrik Regex
- Jokertecken för begäran om huvud
- Metod för begäran
- Schema för begäran
- URL-fråga litteral
- URL-fråga Regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H.264-stöd (HTTP Progressiv nedladdning)

**Syfte:** Bestämmer vilka typer av H.264-filformat som kan användas för att strömma innehåll.

Viktig information:

- Definiera en utrymmesavgränsad uppsättning tillåtna H.264-filnamnstillägg i alternativet Filnamn. Alternativet Filtillägg åsidosätter standardbeteendet. Underhåll MP4- och F4V-stöd genom att inkludera dessa filnamnstillägg när du anger det här alternativet.
- Inkludera en punkt när du anger varje filnamnstillägg (till exempel _.mp4_, _.f4v_).

**Standardbeteende:** HTTP Progressive Download stöder MP4- och F4V-media som standard.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Begäran om att inte uppfylla no-cache

**Syfte:** Avgör om en HTTP-klients no-cache-begäranden ska vidarebefordras till ursprungsservern.

En begäran om no-cache inträffar `Cache-Control: no-cache` när `Pragma: no-cache` HTTP-klienten skickar en och/eller ett huvud i HTTP-begäran.

Värde|Resultat
--|--
Enabled|Gör att en HTTP-klients no-cache-begäranden vidarebefordras till ursprungsservern, och ursprungsservern returnerar svarshuvudena och brödtexten via POP-klienten tillbaka till HTTP-klienten.
Disabled|Återställer standardbeteendet. Standardbeteendet är att förhindra att begäranden utan cache inte vidarebefordras till ursprungsservern.

För all produktionstrafik rekommenderas det starkt att den här funktionen lämnas i standardtillståndet för inaktiverat läge. Annars kommer ursprungsservrar inte att skyddas från slutanvändare som oavsiktligt kan utlösa många no-cache-begäranden när webbsidor uppdateras, eller från de många populära mediespelare som är kodade för att skicka en no-cache-rubrik med varje videobegäran. Den här funktionen kan dock vara användbar för att gälla för vissa kataloger som inte är produktionsföresättningar eller testning, så att nytt innehåll kan dras på begäran från ursprungsservern.

Cachestatusen som rapporteras för en begäran som kan vidarebefordras till `TCP_Client_Refresh_Miss`en ursprungsserver på grund av den här funktionen är . Rapporten Cachestatus, som är tillgänglig i kärnrapporteringsmodulen, innehåller statistisk information efter cachestatus. Med den här rapporten kan du spåra antalet och procentandelen begäranden som vidarebefordras till en ursprungsserver på grund av den här funktionen.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignorera ursprungsnr cache

**Syfte:** Avgör om CDN ska ignorera följande direktiv som visas från en ursprungsserver:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Viktig information:

- Konfigurera den här funktionen genom att definiera en utrymmesavgränsad lista med statuskoder för vilka ovanstående direktiv ignoreras.
- Uppsättningen giltiga statuskoder för den här funktionen är: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 och 505.
- Inaktivera den här funktionen genom att ställa in den på ett tomt värde.

**Standardbeteende:** Standardbeteendet är att uppfylla ovanstående direktiv.

#### <a name="compatibility"></a>Kompatibilitet

På grund av hur cacheinställningar spåras kan den här funktionen inte associeras med följande matchningsvillkor:
- AS-nummer
- Klientens IP-adress
- Cookie Parameter
- Regex för cookieparameter
- Land/region
- Enhet
- Kant Cname
- Refererande domän
- Literal för begär rubrik
- Begär rubrik Regex
- Jokertecken för begäran om huvud
- Metod för begäran
- Schema för begäran
- URL-fråga litteral
- URL-fråga Regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignorera otåliga intervall

**Syfte:** Bestämmer svaret som ska returneras till klienter när en begäran genererar en 416 Begärd statuskod för det begärda intervallet.

Som standard returneras den här statuskoden när den angivna byteintervallbegäran inte kan uppfyllas av en POP och ett sidhuvudfält för If-Range-begäran inte har angetts.

Värde|Resultat
-|-
Enabled|Förhindrar att POP:erna svarar på en ogiltig begäran om byteintervall med en statuskod för 416 begärt intervall som inte kan rekommenderas. I stället kommer servrarna att leverera den begärda tillgången och returnera ett 200 OK till klienten.
Disabled|Återställer standardbeteendet. Standardbeteendet är att uppfylla statuskoden 416 Begärt intervall inte satisfiable.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Intern Max-inaktuella

**Syfte:** Styr hur länge efter den normala förfallotiden en cachelagrad tillgång kan visas från en POP när POP inte kan förnya den cachelagrade tillgången med ursprungsservern.

Normalt, när en tillgångs max-ålder tid löper ut, pop kommer att skicka en begäran om förlängning till ursprungsservern. Ursprungsservern svarar sedan med antingen en 304 Inte ändras för att ge POP ett nytt lån på den cachelagrade tillgången, eller annars med 200 OK för att ge POP med en uppdaterad version av den cachelagrade tillgången.

Om POP inte kan upprätta en anslutning till ursprungsservern när du försöker en sådan förlängning, styr den här interna maxinaktuella funktionen om och hur länge POP kan fortsätta att betjäna den nu inaktuella tillgången.

Observera att det här tidsintervallet börjar när tillgångens maxålder upphör att gälla, inte när den misslyckade förlängningen inträffar. Den maximala period under vilken en tillgång kan betjänas utan lyckad förlängning är därför den tid som anges av kombinationen av max-age plus max-unken. Om en tillgång till exempel cachelagrades vid 9:00 med en maxålder på 30 minuter och en max-inaktuella på 15 minuter, skulle ett misslyckat återvalideringsförsök på 9:44 resultera i att en slutanvändare tar emot den inaktuella cachelagrade tillgången, medan ett misslyckat omvalideringsförsök på 9:46 skulle resultera i att slutanvändaren får en 504 Gateway-timeout.

Alla värden som konfigurerats för den `Cache-Control: must-revalidate` `Cache-Control: proxy-revalidate` här funktionen ersätts av eller rubriker som tas emot från ursprungsservern. Om något av dessa rubriker tas emot från ursprungsservern när en tillgång först cachelagras, kommer POP inte att tjäna en inaktuell cachelagrad tillgång. I så fall, om POP inte kan förnya med ursprunget när tillgångens maxåldersintervall har gått ut, returnerar POP ett 504 Gateway Timeout-fel.

Viktig information:

- Konfigurera den här funktionen genom att:
    - Välja den statuskod som en max-inakt ska tillämpas för.
    - Ange ett heltalsvärde och sedan välja önskad tidsenhet (till exempel sekunder, minuter, timmar osv.). Det här värdet definierar den interna max-inaktuella som ska tillämpas.

- Om du ställer in tidsenheten på "Av" inaktiveras den här funktionen. En cachelagrad tillgång kommer inte att serveras efter dess normala utgångstid.

**Standardbeteende:** Två minuter

#### <a name="compatibility"></a>Kompatibilitet

På grund av hur cacheinställningar spåras kan den här funktionen inte associeras med följande matchningsvillkor:
- AS-nummer
- Klientens IP-adress
- Cookie Parameter
- Regex för cookieparameter
- Land/region
- Enhet
- Kant Cname
- Refererande domän
- Literal för begär rubrik
- Begär rubrik Regex
- Jokertecken för begäran om huvud
- Metod för begäran
- Schema för begäran
- URL-fråga litteral
- URL-fråga Regex
- Jokertecken för URL-fråga
- Parameter för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Loggfrågesträng

**Syfte:** Avgör om en frågesträng ska lagras tillsammans med URL:en i åtkomstloggar.

Värde|Resultat
-|-
Enabled|Tillåter lagring av frågesträngar när webbadresser spelas in i en åtkomstlogg. Om en URL inte innehåller någon frågesträng får det här alternativet ingen effekt.
Disabled|Återställer standardbeteendet. Standardbeteendet är att ignorera frågesträngar när webbadresser spelas in i en åtkomstlogg.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximala keep-alive-begäranden

**Syfte:** Definierar det maximala antalet begäranden för en Keep-Alive-anslutning innan den stängs.

Om du anger det maximala antalet begäranden till ett lågt värde avråds inte och prestandaförseningar.

Viktig information:

- Ange det här värdet som ett helt heltal.
- Ta inte med kommatecken eller perioder i det angivna värdet.

**Standardvärde:** 10 000 begäranden

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Ändra klientbegäranshuvud

**Syfte:** Varje begäran innehåller en uppsättning begäranden som beskriver den. Den här funktionen kan antingen:

- Lägg till eller skriva över värdet som tilldelats ett begärandehuvud. Om det angivna begärandehuvudet inte finns lägger den här funktionen till det i begäran.
- Ta bort ett begärandehuvud från begäran.

Begäranden som vidarebefordras till en ursprungsserver återspeglar de ändringar som gjorts av den här funktionen.

En av följande åtgärder kan utföras på ett begärandehuvud:

Alternativ|Beskrivning|Exempel
-|-|-
Lägg till|Det angivna värdet läggs till i slutet av det befintliga huvudvärdet för begäran.|**Felvärde för begäranden (klient):**<br/>Värde1<br/>**Begär huvudvärde (regelmotor):**<br/>Värde2 <br/>**Nytt huvudvärde för begäran:** <br/>Värde1Värde2
Skriv över|Värdet för begäranhuvudet ställs in på det angivna värdet.|**Felvärde för begäranden (klient):**<br/>Värde1<br/>**Begär huvudvärde (regelmotor):**<br/>Värde2<br/>**Nytt huvudvärde för begäran:**<br/> Värde2 <br/>
Ta bort|Tar bort det angivna begärandehuvudet.|**Felvärde för begäranden (klient):**<br/>Värde1<br/>**Ändra klientbegäranhuvudkonfiguration:**<br/>Ta bort det aktuella begärandehuvudet.<br/>**Resultatet:**<br/>Det angivna begärandehuvudet vidarebefordras inte till ursprungsservern.

Viktig information:

- Kontrollera att värdet som anges i alternativet Namn är en exakt matchning för önskat begärandehuvud.
- Ärendet beaktas inte vid identifieringen av ett huvud. Någon av följande varianter av `Cache-Control` sidhuvudets namn kan till exempel användas för att identifiera det:
    - cache-kontroll
    - CACHE-KONTROLL
    - cachE-kontroll
- När du anger ett rubriknamn använder du bara alfanumeriska tecken, streck eller understreck.
- Om du tar bort ett huvud förhindras att det vidarebefordras till en ursprungsserver av POP:erna.
- Följande rubriker är reserverade och kan inte ändras av den här funktionen:
    - Vidarebefordras
    - värd
    - Via
    - varning
    - x-vidarebefordras-för
    - Alla rubriknamn som börjar med "x-ec" är reserverade.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Ändra klientsvarshuvud

Varje svar innehåller en uppsättning svarsrubriker som beskriver det. Den här funktionen kan antingen:

- Lägg till eller skriva över värdet som tilldelats ett svarshuvud. Om det angivna svarshuvudet inte finns lägger den här funktionen till det i svaret.
- Ta bort ett svarshuvud från svaret.

Som standard definieras svarshuvudvärdena av en ursprungsserver och av POP.The Default, response header values are defined by an origin server and by the POP.

En av följande åtgärder kan utföras på ett svarshuvud:

Alternativ|Beskrivning|Exempel
-|-|-
Lägg till|Det angivna värdet läggs till i slutet av det befintliga svarshuvudvärdet.|**Svarshuvudvärde (klient):**<br />Värde1<br/>**Svarshuvudvärde (regelmotor):**<br/>Värde2<br/>**Nytt svarshuvudvärde:**<br/>Värde1Värde2
Skriv över|Värdet för svarshuvudet ställs in på det angivna värdet.|**Svarshuvudvärde (klient):**<br/>Värde1<br/>**Svarshuvudvärde (regelmotor):**<br/>Värde2 <br/>**Nytt svarshuvudvärde:**<br/>Värde2 <br/>
Ta bort|Tar bort det angivna svarshuvudet.|**Svarshuvudvärde (klient):**<br/>Värde1<br/>**Ändra klientsvarshuvudets konfiguration:**<br/>Ta bort svarshuvudet i fråga.<br/>**Resultatet:**<br/>Det angivna svarshuvudet vidarebefordras inte till beställaren.

Viktig information:

- Kontrollera att värdet som anges i alternativet Namn är en exakt matchning för önskat svarshuvud.
- Ärendet beaktas inte vid identifieringen av ett huvud. Någon av följande varianter av `Cache-Control` sidhuvudets namn kan till exempel användas för att identifiera det:
    - cache-kontroll
    - CACHE-KONTROLL
    - cachE-kontroll
- Om du tar bort ett huvud kan det inte vidarebefordras till beställaren.
- Följande rubriker är reserverade och kan inte ändras av den här funktionen:
    - acceptera-kodning
    - ålder
    - anslutning
    - innehållskodning
    - innehållslängd
    - innehållsintervall
    - date
    - server
    - Släpvagn
    - överföring-kodning
    - uppgradera
    - Variera
    - Via
    - varning
    - Alla rubriknamn som börjar med "x-ec" är reserverade.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Partiell cachedelning

**Syfte:** Avgör om en begäran kan generera delvis cachelagrat innehåll.

Den här partiella cachen kan sedan användas för att uppfylla nya begäranden för innehållet tills det begärda innehållet har cachelagrats helt.

Värde|Resultat
-|-
Enabled|Begäranden kan generera delvis cachelagrad innehåll.
Disabled|Begäranden kan bara generera en fullständigt cachelagd version av det begärda innehållet.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Förvalidat cachelande innehåll

**Syfte:** Avgör om cachelagrat innehåll kan förnyas i förtid innan dess TTL upphör att gälla.

Definiera hur lång tid som ska betalas före utgången av det begärda innehållets TTL under vilken det kommer att vara berättigat till tidig förlängning.

Viktig information:

- Välja "Av" som tidsenhet kräver förlängning ske efter att det cachelagrade innehållets TTL har upphört att gälla. Tiden bör inte anges och ignoreras.

**Standardbeteende:** Av. Förlängning får endast ske efter att det cachelagrade innehållets TTL har upphört att gälla.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Särskilda proxyrubriker

**Syfte:** Definierar uppsättningen [Verizon-specifika HTTP-begäranden](cdn-verizon-http-headers.md) som ska vidarebefordras från en POP till en ursprungsserver.

Viktig information:

- Varje CDN-specifikt begärandehuvud som definieras i den här funktionen vidarebefordras till en ursprungsserver. Uteslutna rubriker vidarebefordras inte.
- Om du vill förhindra att ett CDN-specifikt begärandehuvud vidarebefordras tar du bort det från den utrymmesavgränsade listan i rubriklistfältet.

Följande HTTP-huvuden ingår i standardlistan:
- Via
- X-vidarebefordrad-för
- X-vidarebefordrad-Proto
- X-värd
- X-Midgress
- X-Gateway-lista
- X-EG-namn
- Värd

**Standardbeteende:** Alla CDN-specifika begäranderubriker vidarebefordras till ursprungsservern.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Uppdatera cachefiler med noll byte

**Syfte:** Bestämmer hur en HTTP-klients begäran om en cachetillgång med 0 byte hanteras av POP:erna.

Giltiga värden är:

Värde|Resultat
--|--
Enabled|Gör att POP-referensvärdet för tillgången från ursprungsservern.
Disabled|Återställer standardbeteendet. Standardbeteendet är att visa giltiga cachetillgångar på begäran.

Den här funktionen krävs inte för korrekt cachelagring och innehållsleverans, men kan vara användbar som en lösning. Generatorer med dynamiskt innehåll på ursprungsservrar kan till exempel oavsiktligt resultera i att svar på 0 byte skickas till POP:erna. Dessa typer av svar cachelagras vanligtvis av POP.These types of responses are typically cached by the POP. Om du vet att ett 0-byte svar är aldrig ett giltigt svar för sådant innehåll, kan den här funktionen förhindra att dessa typer av tillgångar från att betjänas till dina kunder.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Ange cacherbara statuskoder

**Syfte:** Definierar den uppsättning statuskoder som kan resultera i cachelagrat innehåll.

Som standard är cachelagring endast aktiverat för 200 OK-svar.

Definiera en utrymmesavgränsad uppsättning av önskade statuskoder.

Viktig information:

- Aktivera funktionen Ignorera ursprungsnr cache. Om den här funktionen inte är aktiverad kanske svaren som inte är 200 OK cachelagras.
- Uppsättningen giltiga statuskoder för den här funktionen är: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 och 505.
- Den här funktionen kan inte användas för att inaktivera cachelagring för svar som genererar en 200 OK-statuskod.

**Standardbeteende:** Cachelagring är endast aktiverat för svar som genererar en 200 OK-statuskod.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Ange anpassad klient-IP-huvud

**Syfte:** Lägger till ett anpassat huvud som identifierar den begärande klienten via IP-adress i begäran.

Alternativet Sidhuvudnamn definierar namnet på det anpassade begärandehuvudet där klientens IP-adress lagras.

Med den här funktionen kan en kundursprungsserver ta reda på klient-IP-adresser via ett anpassat begärandehuvud. Om begäran visas från cacheminnet kommer ursprungsservern inte att informeras om klientens IP-adress. Därför rekommenderas att den här funktionen används med tillgångar som inte cachelagras.

Kontrollera att det angivna rubriknamnet inte matchar något av följande namn:

- Rubriknamn för standardbegäran. En lista över standardrubriknamn finns i [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Reserverade rubriknamn:
    - vidarebefordras för
    - värd
    - Variera
    - Via
    - varning
    - x-vidarebefordras-för
    - Alla rubriknamn som börjar med "x-ec" är reserverade.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Inaktuell innehållsleverans vid fel

**Syfte:** Avgör om utgånget cachelagrat innehåll ska levereras när ett fel inträffar under cacheåtervalideringen eller när det begärda innehållet hämtas från kundens ursprungsserver.

Värde|Resultat
-|-
Enabled|Inaktuellt innehåll visas till beställaren när ett fel uppstår under en anslutning till en ursprungsserver.
Disabled|Ursprungsserverns fel vidarebefordras till beställaren.

**Standardbeteende:** Inaktiverad

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Inaktuella medan Revalidate

**Syfte:** Förbättrar prestanda genom att låta POP-adresser att tjäna inaktuella innehåll till beställaren medan förlängning sker.

Viktig information:

- Funktionens beteende varierar beroende på den valda tidsenheten.
    - **Tidsenhet:** Ange en tid och välj en tidsenhet (till exempel Sekunder, Minuter, Timmar osv.) för att tillåta inaktuella innehållsleveranser. Den här typen av installation gör att CDN kan förlänga den tid som det kan leverera innehåll innan validering krävs enligt följande formel: **TTL** + **Inaktuella medan förnya tid**
    - **Av:** Välj "Av" om du vill kräva förlängning innan en begäran om inaktuellt innehåll kan visas.
        - Ange inte en tid eftersom den inte är tillämplig och kommer att ignoreras.

**Standardbeteende:** Av. Förlängning måste ske innan det begärda innehållet kan delges.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token Auth

**Syfte:** Avgör om tokenbaserad autentisering ska tillämpas på en begäran.

Om tokenbaserad autentisering är aktiverad, kommer endast begäranden som tillhandahåller en krypterad token och uppfyller de krav som anges av den token att uppfyllas.

Krypteringsnyckeln som används för att kryptera och dekryptera tokenvärden bestäms av primärnyckeln och alternativen för säkerhetskopieringsnyckeln på sidan Token Auth. Tänk på att krypteringsnycklar är plattformsspecifika.

**Standardbeteende:** Inaktiverad.

Den här funktionen har företräde framför de flesta funktioner med undantag för funktionen Omskrivning av webbadresser.

Värde | Resultat
------|---------
Enabled | Skyddar det begärda innehållet med tokenbaserad autentisering. Endast begäranden från klienter som tillhandahåller en giltig token och uppfyller dess krav kommer att uppfyllas. FTP-transaktioner exkluderats från tokenbaserad autentisering.
Disabled| Återställer standardbeteendet. Standardbeteendet är att låta konfigurationen för tokenbaserad autentisering avgöra om en begäran ska skyddas.

#### <a name="compatibility"></a>Kompatibilitet

Använd inte Token Auth med ett alltid matchningsvillkor.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Avslagskod för tokenauth

**Syfte:** Bestämmer vilken typ av svar som ska returneras till en användare när en begäran nekas på grund av tokenbaserad autentisering.

De tillgängliga svarskoderna visas i följande tabell.

Svarskod|Svarsnamn|Beskrivning
-------------|-------------|--------
301|Flyttad permanent|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i platshuvudet.
302|Hittade|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i platshuvudet. Den här statuskoden är branschstandardmetoden för att utföra en omdirigering.
307|Tillfällig omdirigering|Den här statuskoden omdirigerar obehöriga användare till den URL som anges i platshuvudet.
401|Behörighet saknas|Genom att kombinera den här statuskoden med svarshuvudet WWW-Autentisera kan du fråga en användare om autentisering.
403|Förbjudet|Det här meddelandet är standardmeddelandet 403 Forbidden status som en obehörig användare ser när han eller hon försöker komma åt skyddat innehåll.
404|Filen hittades inte|Den här statuskoden anger att HTTP-klienten kunde kommunicera med servern, men det begärda innehållet hittades inte.

#### <a name="compatibility"></a>Kompatibilitet

Använd inte Token Auth Denial Code med ett alltid matchningsvillkor. Använd i stället avsnittet **Anpassad förnekelsehantering** på sidan **Token Auth** på hantera-portalen. **Manage** Mer information finns i [Skydda Azure CDN-resurser med tokenautentisering](cdn-token-auth.md).

#### <a name="url-redirection"></a>Omdirigering av URL

Den här funktionen stöder URL-omdirigering till en användardefinierad URL när den är konfigurerad för att returnera en 3xx-statuskod. Den här användardefinierade URL:en kan anges genom att utföra följande steg:

1. Välj en 3xx-svarskod för funktionen Token Auth Denial Code.
2. Välj "Plats" i alternativet Valfritt sidhuvudnamn.
3. Ange alternativet Valfritt sidhuvudvärde till önskad URL.

Om en URL inte har definierats för en 3xx-statuskod returneras standardsvarssidan för en 3xx-statuskod till användaren.

URL-omdirigering gäller endast för 3xx-svarskoder.

Alternativet Valfritt sidhuvudvärde stöder alfanumeriska tecken, citattecken och blanksteg.

#### <a name="authentication"></a>Autentisering

Den här funktionen stöder möjligheten att inkludera WWW-Autentisera-huvudet när du svarar på en obehörig begäran om innehåll som skyddas av tokenbaserad autentisering. Om WWW-Autentisera-huvudet har ställts in på "grundläggande" i konfigurationen, kommer den obehöriga användaren att uppmanas att ange kontouppgifter.

Ovanstående konfiguration kan uppnås genom att utföra följande steg:

1. Välj "401" som svarskod för funktionen Token Auth Denial Code.
2. Välj "WWW-Autentisera" i alternativet Valfritt sidhuvudnamn.
3. Ange alternativet Valfritt sidhuvudvärde till "grundläggande".

WWW-Autentisera-huvudet gäller endast för 401 svarskoder.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Url-ärende för tokenauth ignorera

**Syfte:** Avgör om URL-jämförelser som görs av tokenbaserad autentisering är skiftlägeskänsliga.

De parametrar som påverkas av den här funktionen är:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Giltiga värden är:

Värde|Resultat
---|----
Enabled|Gör att POP ignorerar ärendet när webbadresser för tokenbaserade autentiseringsparametrar jämförs.
Disabled|Återställer standardbeteendet. Standardbeteendet är att URL-jämförelser för tokenautentisering ska vara skiftlägeskänsliga.

**Standardbeteende:** Inaktiverad.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Parametern Token Auth

**Syfte:** Avgör om frågesträngparametern Token-baserad autentisering ska döpas om.

Viktig information:

- Alternativet Värde definierar frågesträngparameternamnet genom vilket en token kan anges.
- Alternativet Värde kan inte anges till "ec_token".
- Kontrollera att namnet som definieras i alternativet Värde endast innehåller giltiga URL-tecken.

Värde|Resultat
----|----
Enabled|Alternativet Värde definierar frågesträngparameternamnet genom vilket token ska definieras.
Disabled|En token kan anges som en odefinierad frågesträngparameter i begäran-URL:en.

**Standardbeteende:** Inaktiverad. En token kan anges som en odefinierad frågesträngparameter i begäran-URL:en.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>Url-omdirigering

**Syfte:** Omdirigerar begäranden via platshuvudet.

Konfigurationen av den här funktionen kräver att du anger följande alternativ:

Alternativ|Beskrivning
-|-
Kod|Välj den svarskod som ska returneras till beställaren.
Käll- & mönster| Dessa inställningar definierar ett URI-mönstret för begäran som identifierar vilken typ av begäranden som kan omdirigeras. Endast begäranden vars webbadress uppfyller båda följande kriterier omdirigeras: <br/> <br/> **Källa (eller åtkomstpunkt för innehåll):** Välj en relativ sökväg som identifierar en ursprungsserver. Den här sökvägen är avsnittet _/XXXX/_ och ditt slutpunktsnamn. <br/><br/> **Källa (mönster):** Ett mönster som identifierar begäranden efter relativ sökväg måste definieras. Det här mönstret för reguljära uttryck måste definiera en sökväg som startar direkt efter den tidigare valda åtkomstpunkten för innehåll (se ovan). <br/> - Se till att uri-kriterierna för begäran (det vill & mönster) som tidigare definierats inte strider mot eventuella matchningsvillkor som definierats för den här funktionen. <br/> - Ange ett mönster. Om du använder ett tomt värde som mönster matchas alla strängar.
Mål| Definiera webbadressen som ovanstående begäranden ska omdirigeras till. <br/><br/> Konstruera webbadressen dynamiskt med hjälp av: <br/> - Ett mönster för reguljära uttryck <br/>- [HTTP-variabler](cdn-http-variables.md) <br/><br/> Ersätt de värden som fångas i källmönstret i målmönstret med $_n_ där _n_ identifierar ett värde efter den ordning i vilken det fångades. 100 kronor representerar till exempel det första värdet som fångas i källmönstret, medan $2 representerar det andra värdet. <br/>

Vi rekommenderar starkt att du använder en absolut webbadress. Användningen av en relativ URL kan omdirigera CDN-url:er till en ogiltig sökväg.

**Exempelscenario**

I det här exemplet visas hur du omdirigerar en CNAME-URL för\/kant som matchas till den här bas-CDN-URL:en: http: /marketing.azureedge.net/brochures

Kvalificerande begäranden omdirigeras till den här CNAME-url:en för baskanten: http:\//cdn.mydomain.com/resources

Den här url-omdirigeringen kan ![uppnås genom följande konfiguration: URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Viktiga punkter:**

- Funktionen URL-omdirigering definierar de url:er som ska omdirigeras. Därför krävs inte ytterligare matchningsvillkor. Även om matchningsvillkoret definierades som "Alltid" omdirigeras endast begäranden som pekar på mappen "broschyrer" på kundens ursprung "marknadsföring".
- Alla matchande begäranden omdirigeras till den CNAME-URL som är definierad i målalternativet.
    - Exempelscenario #1:
        - Exempelbegäran (CDN-URL):\/http: /marketing.azureedge.net/brochures/widgets.pdf
        - Begär URL (efter omdirigering): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Exempelscenario #2:
        - Exempelbegäran (Edge CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf
        - Begär URL (efter omdirigering): http:\//cdn.mydomain.com/resources/widgets.pdf exempelscenario
    - Exempelscenario #3:
        - Exempelbegäran (Edge CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - Begär URL (efter omdirigering): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- Variabeln Begär schema (%{schema}) utnyttjas i alternativet Mål, vilket säkerställer att begärans schema förblir oförändrat efter omdirigering.
- URL-segmenten som hämtades från begäran läggs till i den nya webbadressen via "$1".

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL-omskrivning

**Syfte:** Skriver om url:en för begäran.

Viktig information:

- Konfigurationen av den här funktionen kräver att du anger följande alternativ:

Alternativ|Beskrivning
-|-
 Käll- & mönster | Dessa inställningar definierar ett URI-mönstret för begäran som identifierar vilken typ av begäranden som kan skrivas om. Endast förfrågningar vars webbadress uppfyller båda följande kriterier kommer att skrivas om: <br/><br/>  - **Källa (eller åtkomstpunkt för innehåll):** Välj en relativ sökväg som identifierar en ursprungsserver. Den här sökvägen är avsnittet _/XXXX/_ och ditt slutpunktsnamn. <br/><br/> - **Källa (mönster):** Ett mönster som identifierar begäranden efter relativ sökväg måste definieras. Det här mönstret för reguljära uttryck måste definiera en sökväg som startar direkt efter den tidigare valda åtkomstpunkten för innehåll (se ovan). <br/> Kontrollera att uri-kriterierna för begäran (det vill & mönster) som tidigare definierats inte står i konflikt med något av de matchningsvillkor som definierats för den här funktionen. Ange ett mönster. Om du använder ett tomt värde som mönster matchas alla strängar.
 Mål  |Definiera den relativa URL som ovanstående begäranden ska skrivas om på av: <br/>    1. Välja en åtkomstpunkt för innehåll som identifierar en ursprungsserver. <br/>    2. Definiera en relativ sökväg med hjälp av: <br/>        - Ett mönster för reguljära uttryck <br/>        - [HTTP-variabler](cdn-http-variables.md) <br/> <br/> Ersätt de värden som fångas i källmönstret i målmönstret med $_n_ där _n_ identifierar ett värde efter den ordning i vilken det fångades. 100 kronor representerar till exempel det första värdet som fångas i källmönstret, medan $2 representerar det andra värdet.

 Med den här funktionen kan POP:erna skriva om webbadressen utan att utföra en traditionell omdirigering. Det vill än, får beställaren samma svarskod som om den omskrivna webbadressen hade begärts.

**Exempelscenario 1**

I det här exemplet visas hur du omdirigerar en CNAME-URL\/för kant som matchas till den här bas-CDN-URL:en: http: /marketing.azureedge.net/brochures/

Kvalificerande begäranden omdirigeras till den här CNAME-url:en för baskanten: http:\//MyOrigin.azureedge.net/resources/

Den här url-omdirigeringen kan ![uppnås genom följande konfiguration: URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Exempel scenario 2**

Det här exemplet visar hur du omdirigerar en CNAME-URL för kant från versaler till gemener med reguljära uttryck.

Den här url-omdirigeringen kan ![uppnås genom följande konfiguration: URL-omdirigering](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Viktiga punkter:**

- Funktionen URL-omskrivning definierar de url:er för begäran som ska skrivas om. Därför krävs inte ytterligare matchningsvillkor. Även om matchningsvillkoret definierades som "Alltid" skrivs endast begäranden som pekar på mappen "broschyrer" på kundens ursprung "marknadsföring".

- URL-segmenten som hämtades från begäran läggs till i den nya webbadressen via "$1".

#### <a name="compatibility"></a>Kompatibilitet

Den här funktionen innehåller matchande villkor som måste uppfyllas innan den kan tillämpas på en begäran. För att förhindra att matchningsvillkor ställs in i konflikt är den här funktionen inte kompatibel med följande matchningsvillkor:

- AS-nummer
- CDN Ursprung
- Klientens IP-adress
- Kundens ursprung
- Schema för begäran
- URL-sökvägskatalog
- Url-sökvägstillägg
- Filnamn för URL-sökväg
- URL-sökväg Litteral
- URL-sökväg Regex
- Jokertecken för URL-sökväg
- URL-fråga litteral
- Parameter för URL-fråga
- URL-fråga Regex
- Jokertecken för URL-fråga

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Användarvariabel

**Syfte:** Endast för internt bruk.

[Överst på sidan](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Nästa steg

- [Referens för regler motor](cdn-verizon-premium-rules-engine-reference.md)
- [Regler motor villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regler motor match villkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Åsidosätt HTTP-beteende med hjälp av regelmotorn](cdn-verizon-premium-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)