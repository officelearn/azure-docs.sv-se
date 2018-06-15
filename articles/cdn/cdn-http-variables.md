---
title: HTTP-variabler för Azure CDN regelmotor | Microsoft Docs
description: HTTP-variabler kan du hämta metadata för HTTP-begäran och svar.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: v-deasim
ms.openlocfilehash: 36c1b20219fabd1b7c02247d9a93bb7b7cfc898d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011466"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>HTTP-variabler för Azure CDN regelmotor
HTTP-variabler innehåller det sätt som du kan hämta metadata för HTTP-begäran och svar. Dessa metadata kan sedan användas för att ändra dynamiskt en begäran eller ett svar. Användning av HTTP-variabler är begränsat till följande regler motorn funktioner:

- [Omarbetning av cache-nyckel](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Ändra klienten huvudet i begäran](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Ändra klienten svarshuvud](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [URL: en omdirigering](cdn-rules-engine-reference-features.md#url-redirect)
- [URL-omskrivning](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definitioner
I följande tabell beskrivs de HTTP-variablerna som stöds. Ett tomt värde returneras vid GEO metadata (till exempel postnummer) är inte tillgänglig för en viss begäran.


| Namn | Variabel | Beskrivning | Exempelvärde |
| ---- | -------- | ----------- | ------------ |
| ASN (beställaren) | % {geo_asnum} | Anger förfrågarens som tal. <br /><br />**Inaktuella:** % {virt_dst_asnum}. <br />Den här variabeln har ersatts av % {geo_asnum}. Även om en regel som använder den här föråldrad variabeln fortsätter att fungera, bör du uppdatera den om du vill använda den nya variabeln. | AS15133 |
| Stad (beställaren) | % {geo_city} | Anger den som begär stad. | Los Angeles |
| Kontinent (beställaren) | % {geo_continent} | Anger den som begär kontinent via en förkortning. <br />Giltiga värden är: <br />AF: Afrika<br />AS: Asien<br />Europa: Europa<br />NA: Nordamerika<br />OC: Oceanien<br />SA: Sydamerika<br /><br />**Inaktuella:** % {virt_dst_continent}. <ber />Den här variabeln har ersatts av % {geo_continent}. <br />Även om en regel som använder den här föråldrad variabeln fortsätter att fungera, bör du uppdatera den om du vill använda den nya variabeln.| Gäller inte |
| Cookie-värde | % {cookie_Cookie} | Returnerar värdet som motsvarar cookie-nyckel som identifieras av Cookie-term. | Exempel: <br />% {cookie__utma}<br /><br />Värde för prov:<br />111662281.2.10.1222100123 |
| Land (beställaren) | % {geo_country} | Anger förfrågarens land för ursprung via dess landskod. <br />**Inaktuella:** % {virt_dst_country}. <br /><br />Den här variabeln har ersatts av % {geo_country}. Även om en regel som använder den här föråldrad variabeln fortsätter att fungera, bör du uppdatera den om du vill använda den nya variabeln. | USA |
| Avsedda marknaden området (beställaren) | % {geo_dma_code} |Anger den som begär media marknaden av dess regionkod. <br /><br />Det här fältet används endast på förfrågningar som kommer från USA.| 745 |
| HTTP-begäran-metoden | % {request_method} | Anger metoden som HTTP-begäran. | HÄMTA |
| HTTP-statuskod | % {status} | Anger HTTP-statuskod för svaret. | 200 |
| IP-adress (beställaren) | % {virt_dst_addr} | Anger förfrågarens IP-adress. | 192.168.1.1 |
| Latitud (beställaren) | % {geo_latitude} | Anger den som begär latitud. | 34.0995 |
| Longitud (beställaren) | % {geo_longitude} | Anger den som begär longitud. | -118.4143 |
| Stad statistiska område (beställaren) | % {geo_metro_code} | Anger den som begär stad område. <br /><br />Det här fältet används endast på förfrågningar som kommer från USA.<br />| 745 |
| Port (beställaren) | % {virt_dst_port} | Anger den som begär tillfälliga portar. | 55885 |
| Postnummer (beställaren) | % {geo_postal_code} | Anger den som begär postnummer. | 90210 |
| Frågesträngen hittades | % {is_args} | Värdet för den här variabeln varierar beroende på om begäran innehåller en frågesträng.<br /><br />-Frågesträng hittas:?<br />-Ingen frågesträng: NULL | ? |
| Frågesträngparametern hittades | % {is_amp} | Värdet för den här variabeln varierar beroende på om begäran innehåller minst en frågesträngparametern.<br /><br />-Parametern hittades: &<br />-Inga parametrar: NULL | & |
| Frågesträngens parametervärde | % {arg_&lt;parametern&gt;} | Returnerar värdet som motsvarar frågesträngparametern som identifieras av den &lt;parametern&gt; har löpt ut. | Exempel: <br />% {arg_language}<br /><br />Exempel frågesträngparametern: <br />? språk = SV<br /><br />Exempel på värde: en |
| Frågesträngsvärdet | % {query_string} | Anger hela frågesträngsvärdet som definierats i begärande-URL. |key1 = vär1 & key2 = vär2 & key3 = val3 |
| Referentdomän | % {referring_domain} | Anger den domän som definierats i begärandehuvudet referent. | www.google.com |
| Region (beställaren) | % {geo_region} | Anger den som begär region (till exempel tillstånd eller provins) via en alfanumeriska förkortning. | CA |
| Värde för huvudet i begäran | % {http_RequestHeader} | Returnerar värdet som motsvarar begärandehuvudet identifieras av RequestHeader termen. <br /><br />Om namnet på begäran-huvud innehåller ett streck (till exempel användaragent) kan du ersätta det med ett understreck (till exempel User_Agent).| Exempel: % {http_Connection}<br /><br />Värde för prov: Keep-Alive | 
| Värden för begäran | % {värden} | Anger värden som definierats i begärande-URL. | www.mydomain.com |
| Protokoll för begäran | % {request_protocol} | Anger det begärt-protokollet. | HTTP/1.1 |
| Schemat för begäran | % {schema} | Anger schemat för begäran. |http |
| Begärd URI (relativ) | % {request_uri} | Anger den relativa sökvägen, inklusive frågesträngen definieras i URI-begäran. | /Marketing/foo.js?loggedin=true |
| Begärd URI (relativ utan frågesträngen) | % {URI: n} | Anger den relativa sökvägen till det begärda innehållet. <br /><br/>Viktig information:<br />-Den här relativa sökvägen undantar frågesträngen.<br />-URL-omskrivning visar den här relativa sökväg. En URL ska skrivas under följande förhållanden:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL omskrivning funktion: Den här funktionen skriver om den relativa sökvägen som definierats i URI-begäran.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME-URL: Om den här typen av begäran till motsvarande CDN-URL. |/800001/corigin/rewrittendir/foo.js |
| Förfrågans URI | % {begäran} | Beskriver begäran. <br />Syntax: &lt;HTTP-metoden&gt; &lt;relativa sökvägen&gt; &lt;HTTP-protokoll&gt; | Hämta /marketing/foo.js?loggedin=true HTTP/1.1 |
| Svaret huvudets värde | % {resp_&lt;ResponseHeader&gt;} | Returnerar värdet som motsvarar det svarshuvudet som identifieras av den &lt;ResponseHeader&gt; har löpt ut. <br /><br />Om namnet på rubriken innehåller ett streck (till exempel användaragent) kan du ersätta det med ett understreck (till exempel User_Agent). | Exempel: % {resp_Content_Length}<br /><br />Exempel på värde: 100 |

## <a name="usage"></a>Användning
I följande tabell beskrivs korrekt syntax för en HTTP-variabel.


| Syntax | Exempel | Beskrivning |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | % {värden} | Använd följande syntax för att få hela värdet som motsvarar det angivna &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | % {värden} | Använd följande syntax för att ange fallet för hela värdet som motsvarar det angivna &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{Host/=^www\.([^\.] +)\.([^\.:] +) /cdn.$2.$3:80} | Använd ett reguljärt uttryck för &lt;HTTPVariableDelimiterExpression&gt; om du vill ersätta, ta bort eller ändra en HTTP-variabelns värde. |

HTTP-variabelnamn stöder endast alfabetiska tecken och understreck. Konvertera tecken som inte stöds till understreck.

## <a name="delimiter-reference"></a>En avgränsare för referens
En avgränsare kan anges när en HTTP-variabeln för att åstadkomma något av följande effekter:

- Omvandla till värdet som associeras med variabeln.

     Exempel: Konvertera hela värdet till gemener.

- Ta bort värdet som associeras med variabeln.

- Ändra värdet som associeras med variabeln.

     Exempel: Använda reguljära uttryck för att ändra värdet som associeras med HTTP-variabeln.

I följande tabell beskrivs avgränsarna.

| Avgränsare | Beskrivning |
| --------- | ----------- |
| := | Anger att ett standardvärde kommer att tilldelas variabeln när den är antingen: <br />-Saknas <br />-Värdet NULL. |
| :+ | Anger att ett standardvärde kommer att tilldelas variabeln när den har tilldelats ett värde. |
| : | Anger att en understräng av värdet som tilldelas variabeln ska expanderas. |
| # | Anger att det mönster som anges efter avgränsare ska tas bort när det hittas i början av det värde som associeras med variabeln. |
| % | Anger att det mönster som anges efter avgränsare ska tas bort när det hittas i slutet av värdet som associeras med variabeln. <br />Den här definitionen gäller endast när symbolen % används som avgränsare. |
| / | Avgränsar en HTTP-variabel eller ett mönster. |
| // | Sök och Ersätt alla förekomster av det angivna mönstret. |
| /= | Hitta, kopiera och skriva om alla förekomster av det angivna mönstret. |
| , | Konvertera det värdet som associeras med HTTP-variabeln till gemener. |
| ^ | Konvertera det värdet som associeras med HTTP-variabeln till versaler. |
| ,, | Konvertera alla instanser av det angivna tecknet i värdet som associeras med HTTP-variabeln till gemener. |
| ^^ | Konvertera alla instanser av det angivna tecknet i värdet som associeras med HTTP-variabeln till versaler. |

## <a name="exceptions"></a>Undantag
I följande tabell beskrivs omständigheter som den angivna texten inte behandlas som en HTTP-variabel.

| Villkor | Beskrivning | Exempel |
| --------- | ----------- | --------|
| Undantagstecken % symbol | Procentandel symbolen kan hoppas genom att använda ett omvänt snedstreck. <br />Exempelvärde till höger behandlas som litteralt värde och inte som en HTTP-variabel.| \%{värden} |
| Okänd variabler | En tom sträng returneras alltid för okänd variabler. | % {unknownvariable} |
| Ogiltiga tecken eller ogiltig syntax | Variabler som innehåller ogiltiga tecken eller syntaxen behandlas som litteralvärden. <br /><br />Exempel #1: Det angivna värdet innehåller ogiltiga tecken (till exempel-). <br /><br />Exempel #2: Det angivna värdet innehåller en dubbel uppsättning klammerparenteser. <br /><br />Exempel #3: Det angivna värdet saknar avslutande klammerparentes.<br /> | Exempel #1: % {resp_user-agent} <br /><br />Exempel #2: % {{värden}} <br /><br />Exempel #3: % {värden |
| Variabelnamnet som saknas | Ett nullvärde returneras alltid när en variabel inte har angetts. | %{} |
| Överskridande tecken | Tecken som löper en variabel behandlas som litteralvärden. <br />Exempelvärde till höger innehåller en avslutande klammerparentes som kommer att behandlas som litteralt värde. | % {värden}} |

## <a name="setting-default-header-values"></a>Ange standardvärden för sidhuvud
Ett standardvärde kan tilldelas till en rubrik när den uppfyller något av följande villkor:
- Saknas/inaktivera
- Värdet NULL.

I följande tabell beskrivs hur du definierar ett standardvärde.

| Villkor | Syntax | Exempel | Beskrivning |
| --------- | ------ | --------| ----------- |
| Ange ett sidhuvud till ett standardvärde när det uppfyller något av följande villkor: <br /><br />-Huvud saknas <br /><br />-Huvudets värde har angetts till NULL.| % {Variabel: = Value} | % {http_referer: = okänt} | Referent huvudet kan endast anges till *Ospecificerad* när den är antingen saknas eller null. Ingen åtgärd sker om den har ställts in. |
| Ange ett sidhuvud till ett standardvärde när det saknas. | % {Variabel = Value} | % {http_referer = okänt} | Referent huvudet kan endast anges till *Ospecificerad* när den saknas. Ingen åtgärd sker om den har ställts in. |
| Ange rubriken till ett standardvärde när den inte uppfyller något av följande villkor: <br /><br />-Saknas<br /><br /> -Värdet NULL. | % {Variabel: värdet +} | % {http_referer: + Ospecificerad} | Referent huvudet kan endast anges till *Ospecificerad* när ett värde har tilldelats till den. Ingen åtgärd sker om den saknas eller null. |

## <a name="manipulating-variables"></a>Ändra variabler
Variabler kan hanteras på följande sätt:

- Expandera delsträngar
- Ta bort mönster

### <a name="substring-expansion"></a>Delsträngen expanderades
Som standard kommer att expandera en variabel till fullständig värde. Använd följande syntax för att enbart expandera en understräng av variabelns värde.

`%<Variable>:<Offset>:<Length>}`

Viktig information:

- Värdet som tilldelas förskjutning termen avgör det första tecknet i delsträngen:

     - Positivt: Det första tecknet i delsträngen beräknas från det första tecknet i strängen.
     - Noll: Det första tecknet i delsträngen är det första tecknet i strängen.
     - Negativt: Det första tecknet i delsträngen beräknas från det sista tecknet i strängen.

- Längden på delsträngen bestäms av den *längd* termen:

     - Utelämnas: Utelämna på lång sikt kan delsträngen som ska innehålla alla tecken mellan tecknet början och slutet av strängen.
     - Positivt: Anger längden på delsträngen från det första tecknet till höger.
     - Negativt: Anger längden på delsträngen från det första tecknet till vänster.

#### <a name="example"></a>Exempel:

I följande exempel är beroende av följande exempel begäran URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Följande sträng visar olika metoder för att ändra variabler:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Baserat på exemplet begärd URL, genererar ovan variabeln modifieringen följande värde:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Borttagning av mönster
Text som matchar ett specifikt mönster kan tas bort från början eller slutet av värdet för en variabel.

| Syntax | Åtgärd |
| ------ | ------ |
| % {Variabeln #Pattern} | Ta bort text när det angivna mönstret hittas i början av en variabelns värde. |
| % {Variabeln % mönster} | Ta bort text när det angivna mönstret hittas i slutet av värdet för en variabel. |

#### <a name="example"></a>Exempel:

I det här exempelscenariot den *request_uri* variabeln anges till:

`/800001/myorigin/marketing/product.html?language=en-US`

Följande tabell visar hur den här syntaxen fungerar.

| Exempelsyntax | Resultat |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/Marketing/Product.HTML?Language=en-us | Eftersom variabeln börjar med mönstret, har ersatts. |
| % {request_uri % html} htm | /800001/myorigin/Marketing/Product.HTML?Language=en-us | Eftersom variabeln inte avslutas med mönstret, var det ingen ändring.|

### <a name="find-and-replace"></a>Sök och Ersätt
Sök och Ersätt syntaxen beskrivs i följande tabell.

| Syntax | Åtgärd |
| ------ | ------ |
| %{Variable/Find/replace} | Sök och Ersätt första förekomsten av det angivna mönstret. |
| %{Variable//Find/replace} | Sök och Ersätt alla förekomster av det angivna mönstret. |
| % {Variabeln ^} |Konvertera hela värdet till versaler. |
| % {Variabeln ^ hitta} | Den första förekomsten av det angivna mönstret att konvertera till versaler. |
| % {Variabel} | Konvertera hela värdet till gemener. |
| % {Variabel, hitta} | Den första förekomsten av det angivna mönstret att konvertera till gemener. |

### <a name="find-and-rewrite"></a>Sök och skriv om
Använd den text som matchar det angivna mönstret när skriva om den för en variation av Sök och ersätt. Sök och skriv om syntaxen beskrivs i följande tabell.

| Syntax | Åtgärd |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Hitta, kopiera och skriva om alla förekomster av det angivna mönstret. |
| %{Variable/^Find/Rewrite} | Hitta, kopiera och skriva om det angivna mönstret när det uppstår i början av variabeln. |
| %{Variable/$Find/Rewrite} | Hitta, kopiera och skriva om det angivna mönstret när det uppstår i slutet av variabeln. |
| %{Variable/Find} | Hitta och ta bort alla förekomster av det angivna mönstret. |

Viktig information:

- Expandera text som matchar det angivna mönstret genom att ange ett dollartecken följt av ett hela heltal (till exempel $1).

- Du kan ange flera mönster. Ordningen i vilken mönstret anges anger det heltal som ska tilldelas till den. I följande exempel visas de första matchar mönstret ”www.”, det andra mönstret matchar andranivådomän och tredje mönstret matchar toppnivådomänen:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Värdet för omskrivet kan bestå av en kombination av text och dessa platshållare.

    I det förra exemplet värdnamnet skrivs till `cdn.$2.$3:80` (till exempel cdn.mydomain.com:80).

- I fallet med en platshållare för mönster (till exempel 1 USD) kan ändras via följande flaggor:
     - U: versaler expanderade värdet.

         Exempelsyntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: gemener expanderade värdet.

         Exempelsyntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- En operator måste anges innan mönstret. Den angivna operatorn bestämmer avbildning av mönster:

     - `=`: Anger att alla förekomster av det angivna mönstret måste skapas och skrivas om.
     - `^`: Anger att endast text som börjar med det angivna mönstret kommer att sparas.
     - `$`: Anger texten som slutar med det angivna mönstret kommer att avbilda.
 
- Om du utelämnar de */skriva om* värde, den text som matchar mönstret tas bort.


