---
title: HTTP-variabler för Azure CDN-regelmotor | Microsoft-dokument
description: Med HTTP-variabler kan du hämta HTTP-begärande- och svarsmetadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593819"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>HTTP-variabler för Azure CDN-regelmotor
HTTP-variabler ger de metoder för att hämta HTTP-begäran och svar metadata. Dessa metadata kan sedan användas för att dynamiskt ändra en begäran eller ett svar. Användningen av HTTP-variabler är begränsad till följande regler motorfunktioner:

- [Cache-nyckel skriva](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Ändra klientbegäranshuvud](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Ändra klientsvarshuvud](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Url-omdirigering](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL-omskrivning](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definitioner
I följande tabell beskrivs http-variablerna som stöds. Ett tomt värde returneras när GEO-metadata (till exempel postnummer) inte är tillgängliga för en viss begäran.


| Namn | Variabel | Beskrivning | Exempelvärde |
| ---- | -------- | ----------- | ------------ |
| ASN (Beställare) | %{geo_asnum} | Anger beställarens AS-nummer. <br /><br />**Inaktuell:** %{virt_dst_asnum}. <br />Den här variabeln har inaktuellts till förmån för %{geo_asnum}. Även om en regel som använder den här inaktuella variabeln fortsätter att fungera, bör du uppdatera den för att använda den nya variabeln. | AS15133 |
| Ort (Beställare) | %{geo_city} | Anger beställarens stad. | Los Angeles |
| Kontinent (Beställare) | %{geo_continent} | Anger beställarens kontinent genom förkortningen. <br />Giltiga värden är: <br />AF: Afrika<br />AS: Asien<br />EU: Europa<br />NA: Nordamerika<br />OC: Oceanien<br />SA: Sydamerika<br /><br />**Inaktuell:** %{virt_dst_continent}. <br />Den här variabeln har inaktuellts till förmån för %{geo_continent}. <br />Även om en regel som använder den här inaktuella variabeln fortsätter att fungera, bör du uppdatera den för att använda den nya variabeln.| Ej tillämpligt |
| Cookie-värde | %{cookie_Cookie} | Returnerar värdet som motsvarar cookie-nyckeln som identifieras av cookie-termen. | Exempel på användning: <br />%{cookie__utma}<br /><br />Exempelvärde:<br />111662281.2.10.1222100123 |
| Land (Beställare) | %{geo_country} | Anger beställarens ursprungsland genom landskoden. <br />**Inaktuell:** %{virt_dst_country}. <br /><br />Den här variabeln har inaktuellts till förmån för %{geo_country}. Även om en regel som använder den här inaktuella variabeln fortsätter att fungera, bör du uppdatera den för att använda den nya variabeln. | USA |
| Utsett marknadsområde (beställare) | %{geo_dma_code} |Anger beställarens mediemarknad med dess regionkod. <br /><br />Det här fältet gäller endast för begäranden som kommer från USA.| 745 |
| HTTP-begäran metod | %{request_method} | Anger http-begäransmetoden. | HÄMTA |
| HTTP-statuskod | %{status} | Anger HTTP-statuskoden för svaret. | 200 |
| IP-adress (beställare) | %{virt_dst_addr} | Anger beställarens IP-adress. | 192.168.1.1 |
| Latitud (beställare) | %{geo_latitude} | Anger beställarens latitud. | 34.0995 |
| Longitud (Beställare) | %{geo_longitude} | Anger beställarens longitud. | -118.4143 |
| Storstadsområde (Beställare) | %{geo_metro_code} | Anger beställarens storstadsområde. <br /><br />Det här fältet gäller endast för begäranden som kommer från USA.<br />| 745 |
| Port (beställare) | %{virt_dst_port} | Anger beställarens tillfälliga port. | 55885 |
| Postnummer (beställare) | %{geo_postal_code} | Anger beställarens postnummer. | 90210 |
| Frågesträng hittades | %{is_args} | Värdet för den här variabeln varierar beroende på om begäran innehåller en frågesträng.<br /><br />- Frågesträng hittades: ?<br />- Ingen frågesträng: NULL | ? |
| Frågesträngparameter hittades | %{is_amp} | Värdet för den här variabeln varierar beroende på om begäran innehåller minst en frågesträngparameter.<br /><br />- Parameter Hittades: &<br />- Inga parametrar: NULL | & |
| Parametervärde för frågesträng | Parametern %{arg_&lt;&gt;} | Returnerar värdet som motsvarar frågesträngparametern som identifieras av parametertermen. &lt;&gt; | Exempel på användning: <br />%{arg_language}<br /><br />Exempel på frågesträngparameter: <br />?language=en<br /><br />Exempelvärde: en |
| Frågesträngvärde | %{query_string} | Anger hela frågesträngvärdet som definierats i begäran-URL:en. |key1=val1&key2=val2&key3=val3 |
| Domän för värvare | %{referring_domain} | Anger den domän som definierats i begärandehuvudet för värvare. | <www.google.com> |
| Region (Beställare) | %{geo_region} | Anger beställarens region (till exempel stat eller provins) genom dess alfanumeriska förkortning. | CA |
| Värde för begäranhuvud | %{http_RequestHeader} | Returnerar värdet som motsvarar det begärandehuvud som identifieras av termen RequestHeader. <br /><br />Om namnet på begäranden innehåller ett streck (till exempel User-Agent) ersätter du det med ett understreck (till exempel User_Agent).| Exempel på användning: %{http_Connection}<br /><br />Exempelvärde: Håll dig vid liv | 
| Begär värd | %{värd} | Anger värden som definierats i url:en för begäran. | <www.mydomain.com> |
| Protokoll för begäran | %{request_protocol} | Anger protokollet för begäran. | HTTP/1.1 |
| Schema för begäran | %{schema} | Anger förfrågerschemat. |http |
| Begär URI (relativ) | %{request_uri} | Anger den relativa sökvägen, inklusive frågesträngen, som definierats i URI-begäran. | /marketing/foo.js?loggedin=true /marketing/foo.js?loggedin=true /marketing/foo.js?loggedin=true /marketing |
| Begär URI (Relativ utan frågesträng) | %{uri} | Anger den relativa sökvägen till det begärda innehållet. <br /><br/>Viktig information:<br />- Den här relativa sökvägen utesluter frågesträngen.<br />- Den här relativa sökvägen återspeglar url-omskrivningar. En webbadress kommer att skrivas om på följande villkor:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Skriv om funktion: Den här funktionen skriver om den relativa sökvägen som definierats i URI-begäran.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: Den här typen av begäran skrivs om till motsvarande CDN URL. |/800001/corigin/rewrittendir/foo.js |
| URI för förfrågan | %{begäran} | Beskriver begäran. <br />Syntax: &lt;&gt; &lt;HTTP-metodens relativa sökväg&gt; &lt;HTTP-protokoll&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Svarshuvudvärde | %{resp_&lt;ResponseHeader&gt;} | Returnerar värdet som motsvarar det &lt;svarshuvud&gt; som identifieras av termen ResponseHeader. <br /><br />Om namnet på svarshuvudet innehåller ett streck (till exempel User-Agent) ersätter du det med ett understreck (till exempel User_Agent). | Exempel på användning: %{resp_Content_Length}<br /><br />Provvärde: 100 |

## <a name="usage"></a>Användning
I följande tabell beskrivs rätt syntax för att ange en HTTP-variabel.


| Syntax | Exempel | Beskrivning |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{värd} | Använd den här syntaxen om du &lt;vill hämta&gt;hela värdet som motsvarar det angivna HTTPVariable . |
| %{&lt;HTTPVariableDelimiter&gt;} | %{värd,} | Använd den här syntaxen om du vill ange &lt;skiftläge för&gt;hela värdet som motsvarar den angivna HTTPVariableDelimiter . |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.\.([^ :]+)/cdn.$2.$3:80} | Använd ett reguljärt uttryck för &lt;HTTPVariableDelimiterExpression&gt; för att ersätta, ta bort eller ändra en HTTP-variabels värde. |

HTTP-variabelnamn stöder bara alfabetiska tecken och understreck. Konvertera tecken som inte stöds till understreck.

## <a name="delimiter-reference"></a>Referens för avgränsare
En avgränsare kan anges efter en HTTP-variabel för att uppnå någon av följande effekter:

- Omvandla värdet som är associerat med variabeln.

     Exempel: Konvertera hela värdet till gemener.

- Ta bort värdet som är associerat med variabeln.

- Manipulera värdet som är associerat med variabeln.

     Exempel: Använd reguljära uttryck för att ändra värdet som är associerat med HTTP-variabeln.

Avgränsare beskrivs i följande tabell.

| Avgränsare | Beskrivning |
| --------- | ----------- |
| := | Anger att variabeln tilldelas ett standardvärde när det är följande: <br />- Saknas <br />- Ställ in på NULL. |
| :+ | Anger att ett standardvärde ska tilldelas variabeln när ett värde har tilldelats den. |
| : | Anger att en delsträng av värdet som tilldelats variabeln ska utökas. |
| # | Anger att det mönster som anges efter den här avgränsare ska tas bort när den hittas i början av värdet som är associerat med variabeln. |
| % | Anger att det mönster som anges efter den här avgränsare ska tas bort när den hittas i slutet av värdet som är associerat med variabeln. <br />Denna definition är endast tillämplig när %-symbolen används som avgränsare. |
| / | Avgränsar en HTTP-variabel eller ett mönster. |
| // | Söka efter och ersätta alla förekomster av det angivna mönstret. |
| /= | Söka efter, kopiera och skriva om alla förekomster av det angivna mönstret. |
| , | Konvertera värdet som är associerat med HTTP-variabeln till gemener. |
| ^ | Konvertera värdet som är associerat med HTTP-variabeln till versaler. |
| ,, | Konvertera alla förekomster av det angivna tecknet i värdet som är associerat med HTTP-variabeln till gemener. |
| ^^ | Konvertera alla förekomster av det angivna tecknet i värdet som är associerat med HTTP-variabeln till versaler. |

## <a name="exceptions"></a>Undantag
I följande tabell beskrivs omständigheter under vilka den angivna texten inte behandlas som en HTTP-variabel.

| Villkor | Beskrivning | Exempel |
| --------- | ----------- | --------|
| Att fly % symbol | Den procentuella symbolen kan undgås med hjälp av ett omvänt snedstreck. <br />Exempelvärdet till höger behandlas som ett litteralt värde och inte som en HTTP-variabel.| \%{värd} |
| Okända variabler | En tom sträng returneras alltid för okända variabler. | %{unknown_variable} |
| Ogiltiga tecken eller syntax | Variabler som innehåller ogiltiga tecken eller syntax behandlas som litterala värden. <br /><br />Exempel #1: Det angivna värdet innehåller ett ogiltigt tecken (till exempel -). <br /><br />Exempel #2: Det angivna värdet innehåller en dubbel uppsättning klammerparenteser. <br /><br />Exempel #3: Det angivna värdet saknar ett avslutande klammerparentes.<br /> | Exempel #1: %{resp_user-agent} <br /><br />Exempel #2: %{{host}} <br /><br />Exempel #3: %{värd |
| Variabelnamn saknas | Ett NULL-värde returneras alltid när en variabel inte har angetts. | %{} |
| Avslutande tecken | Tecken som spårar en variabel behandlas som litterala värden. <br />Exempelvärdet till höger innehåller ett avslutande klammerparentes som ska behandlas som ett litteralt värde. | %{host}} |

## <a name="setting-default-header-values"></a>Ange standardrubrikvärden
Ett standardvärde kan tilldelas ett huvud när det uppfyller något av följande villkor:
- Saknas/inte
- Ställ in på NULL.

I följande tabell beskrivs hur du definierar ett standardvärde.

| Villkor | Syntax | Exempel | Beskrivning |
| --------- | ------ | --------| ----------- |
| Ange ett huvud till ett standardvärde när det uppfyller något av följande villkor: <br /><br />- Header saknas <br /><br />- Huvudvärdet är inställt på NULL.| %{Variabel:=Värde} | %{http_referrer:=ospecificerad} | Hänvisningshuvudet anges endast till *ospecificerat* när det antingen saknas eller ställs in på NULL. Inga åtgärder kommer att vidtas om den har fastställts. |
| Ange ett huvud till ett standardvärde när det saknas. | %{Variable=Värde} | %{http_referrer=ospecificerad} | Hänvisningshuvudet anges endast till *ospecificerat* när det saknas. Inga åtgärder kommer att vidtas om den har fastställts. |
| Ange huvudet till ett standardvärde när det inte uppfyller något av följande villkor: <br /><br />- Saknas<br /><br /> - Ställ in på NULL. | %{Variabel:+Värde} | %{http_referrer:+ospecificerad} | Hänvisningshuvudet anges endast till *ospecificerat* när ett värde har tilldelats det. Ingen åtgärd kommer att vidtas om den antingen saknas eller är inställd på NULL. |

## <a name="manipulating-variables"></a>Manipulera variabler
Variabler kan manipuleras på följande sätt:

- Expanderande delsträngar
- Ta bort mönster

### <a name="substring-expansion"></a>Expansion av delsträng
Som standard expanderas en variabel till sitt fulla värde. Använd följande syntax om du bara vill expandera en delsträng av variabelns värde.

`%<Variable>:<Offset>:<Length>}`

Viktig information:

- Värdet som tilldelats motliggande termen bestämmer delsträngens starttecken:

     - Positivt: Delsträngens starttecken beräknas utifrån det första tecknet i strängen.
     - Noll: Delsträngens starttecken är det första tecknet i strängen.
     - Negativt: Delsträngens starttecken beräknas utifrån det sista tecknet i strängen.

- Längden på delsträngen bestäms av *Length* längdtermen:

     - Utelämnad: Om du utelämnar längdtermen kan delsträngen inkludera alla tecken mellan starttecknet och slutet av strängen.
     - Positivt: Bestämmer längden på delsträngen från starttecknet till höger.
     - Negativt: Bestämmer längden på delsträngen från starttecknet till vänster.

#### <a name="example"></a>Exempel:

Följande exempel bygger på följande URL för exempelbegäran:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Följande sträng visar olika metoder för att manipulera variabler:

https:\/https: /www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Baserat på url:en för exempelbegäran ger ovanstående variabelmanipulering följande värde:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Borttagning av mönster
Text som matchar ett visst mönster kan tas bort från antingen början eller slutet av en variabels värde.

| Syntax | Åtgärd |
| ------ | ------ |
| %{Variabel#Mönster} | Ta bort text när det angivna mönstret hittas i början av en variabels värde. |
| %{variabeln%mönster} | Ta bort text när det angivna mönstret hittas i slutet av en variabels värde. |

#### <a name="example"></a>Exempel:

I det här exempelscenariot är *request_uri* variabeln inställd på:

`/800001/myorigin/marketing/product.html?language=en-US`

Följande tabell visar hur syntaxen fungerar.

| Exempel syntax | Resultat | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Eftersom variabeln börjar med mönstret ersattes den. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Eftersom variabeln inte slutar med mönstret gjordes ingen ändring.|

### <a name="find-and-replace"></a>Sök och ersätt
Syntaxen för sök och ersätt beskrivs i följande tabell.

| Syntax | Åtgärd |
| ------ | ------ |
| %{Variabel/Sök/ersätt} | Söka efter och ersätt den första förekomsten av det angivna mönstret. |
| %{Variabel//Sök/ersätt} | Söka efter och ersätta alla förekomster av det angivna mönstret. |
| %{Variabel^} |Konvertera hela värdet till versaler. |
| %{Variabel^Sök} | Konvertera den första förekomsten av det angivna mönstret till versaler. |
| %{Variabel,} | Konvertera hela värdet till gemener. |
| %{Variabel,Sök} | Konvertera den första förekomsten av det angivna mönstret till gemener. |

### <a name="find-and-rewrite"></a>Hitta och skriva om
Om du vill ha en variant på sök och ersätt använder du den text som matchar det angivna mönstret när du skriver om det. Syntaxen för sök- och skriv om beskrivs i följande tabell.

| Syntax | Åtgärd |
| ------ | ------ |
| %{Variabel/=Sök/skriva om} | Söka efter, kopiera och skriva om alla förekomster av det angivna mönstret. |
| %{Variabel/^Sök/skriva om} | Söka efter, kopiera och skriva om det angivna mönstret när det inträffar i början av variabeln. |
| %{Variabel/$Find/Skriv om} | Söka efter, kopiera och skriva om det angivna mönstret när det inträffar i slutet av variabeln. |
| %{Variabel/Sök} | Söka efter och ta bort alla förekomster av det angivna mönstret. |

Viktig information:

- Expandera text som matchar det angivna mönstret genom att ange ett dollartecken följt av ett helt heltal (till exempel $1).

- Flera mönster kan anges. Den ordning i vilken mönstret anges bestämmer det heltal som ska tilldelas det. I följande exempel matchar det första mönstret "www.", det andra mönstret matchar domänen på andra nivån och det tredje mönstret matchar den översta domänen:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Det omskrivna värdet kan bestå av valfri kombination av text och dessa platshållare.

    I föregående exempel skrivs värdnamnet `cdn.$2.$3:80` om till (till exempel cdn.mydomain.com:80).

- Fallet med en mönsterplatshållare (till exempel $1) kan ändras med hjälp av följande flaggor:
     - U: Versaler det utökade värdet.

         Exempel syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Ge ut det utökade värdet.

         Exempel syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- En operator måste anges före mönstret. Den angivna operatorn bestämmer beteendet för mönsterbefångande:

     - `=`: Anger att alla förekomster av det angivna mönstret måste fångas in och skrivas om.
     - `^`: Anger att endast text som börjar med det angivna mönstret kommer att fångas.
     - `$`: Anger att endast text som slutar med det angivna mönstret ska fångas in.
 
- Om du utelämnar värdet */Omskrivning* tas texten som matchar mönstret bort.


