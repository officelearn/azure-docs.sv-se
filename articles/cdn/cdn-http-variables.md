---
title: HTTP-variabler för Azure CDN regelmotor | Microsoft Docs
description: HTTP-variabler kan du hämta metadata för HTTP-begäranden och svar.
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
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 42fd28f2a18ecf81c7846abdc7b3159a275a9cd7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013557"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>HTTP-variabler för Azure CDN regelmotor
HTTP-variabler innehåller innebär genom vilka du kan hämta metadata för HTTP-begäranden och svar. Dessa metadata kan sedan användas för att ändra dynamiskt en begäran eller ett svar. Användning av HTTP-variabler är begränsat till följande regler motorn funktioner:

- [Cache-Key Rewrite](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Ändra begärandehuvud i klienten](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Ändra klienten svarshuvud](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [URL-omdirigering](cdn-rules-engine-reference-features.md#url-redirect)
- [URL-omskrivning](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definitioner
I följande tabell beskrivs de HTTP-variablerna. Ett tomt värde returneras när GEO-metadata (till exempel postnummer) är inte tillgänglig för en viss begäran.


| Namn | Variabel | Beskrivning | Exempelvärde |
| ---- | -------- | ----------- | ------------ |
| ASN (begär) | %{geo_asnum} | Anger den som begär som tal. <br /><br />**Inaktuella:** % {virt_dst_asnum}. <br />Den här variabeln är inaktuell och ersatts med % {geo_asnum}. Även om en regel som använder den här föråldrad variabeln fortsätter att fungera, bör du uppdatera den om du vill använda den nya variabeln. | AS15133 |
| Stad (begär) | %{geo_city} | Anger den som begär stad. | Los Angeles |
| Kontinent (begär) | %{geo_continent} | Anger den som begär kontinent via en förkortning. <br />Giltiga värden är: <br />AF: Afrika<br />PRECIS SOM: Asien<br />EU: Europa<br />NA: Nordamerika<br />OC: Oceanien<br />SA: Sydamerika<br /><br />**Inaktuella:** % {virt_dst_continent}. <ber />Den här variabeln är inaktuell och ersatts med % {geo_continent}. <br />Även om en regel som använder den här föråldrad variabeln fortsätter att fungera, bör du uppdatera den om du vill använda den nya variabeln.| Gäller inte |
| Cookie-värde | %{cookie_Cookie} | Returnerar värdet för cookie-nyckeln som identifieras av Cookie-perioden. | Exempel: <br />%{cookie__utma}<br /><br />Exempelvärde:<br />111662281.2.10.1222100123 |
| Land (begär) | %{geo_country} | Anger den som begär ursprungslandet via dess landskod. <br />**Inaktuella:** % {virt_dst_country}. <br /><br />Den här variabeln är inaktuell och ersatts med % {geo_country}. Även om en regel som använder den här föråldrad variabeln fortsätter att fungera, bör du uppdatera den om du vill använda den nya variabeln. | USA |
| Avsedda marknadsområde (begär) | %{geo_dma_code} |Anger den som begär media marknaden genom dess regionskod. <br /><br />Det här fältet används endast på förfrågningar som kommer från USA.| 745 |
| HTTP-frågemetod | %{request_method} | Anger metoden för HTTP-begäran. | HÄMTA |
| HTTP-statuskod | %{status} | Anger HTTP-statuskod för svaret. | 200 |
| IP-adress (begär) | %{virt_dst_addr} | Anger den som begär IP-adress. | 192.168.1.1 |
| Latitud (begär) | %{geo_latitude} | Anger den som begär latitud. | 34.0995 |
| Longitud (begär) | %{geo_longitude} | Anger den som begär longitud. | -118.4143 |
| Statistisk Huvudstadsregionen (begär) | %{geo_metro_code} | Anger den som begär huvudstadsregionen. <br /><br />Det här fältet används endast på förfrågningar som kommer från USA.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Anger den som begär tillfällig port. | 55885 |
| Postnummer (begär) | %{geo_postal_code} | Anger den som begär postnummer. | 90210 |
| Frågesträngen hittades | %{is_args} | Värdet för den här variabeln varierar beroende på om förfrågan innehåller en frågesträng.<br /><br />-Frågesträng hittades:?<br />– Ingen frågesträng: NULL | ? |
| Parametern för frågesträngen hittades | %{is_amp} | Värdet för den här variabeln varierar beroende på om begäran innehåller minst en frågesträngsparameter.<br /><br />-Parametern hittades: &<br />– Ingen parametrar: NULL | & |
| Parametern för Frågesträngsvärdet | %{arg_&lt;parameter&gt;} | Returnerar värdet för frågesträngparametern som identifieras av den &lt;parametern&gt; termen. | Exempel: <br />%{arg_language}<br /><br />Exemplet frågesträngparametern: <br />? språk = SV<br /><br />Exempel på värde: en |
| Frågesträngsvärdet | %{query_string} | Anger hela frågesträngsvärdet som definierats i fråge-URL. |key1 = val1 & key2 = val2 & key3 = val3 |
| Referentdomän | %{referring_domain} | Anger den domän som definierats i referent rubriken. | <www.google.com> |
| Region (begär) | %{geo_region} | Anger den som begär region (till exempel region) via en alfanumeriska förkortning. | CA |
| Värde på begärandehuvud | %{http_RequestHeader} | Returnerar värdet för huvudet för begäran som identifieras av RequestHeader termen. <br /><br />Om namnet på huvudet för begäran innehåller ett bindestreck (till exempel användar-Agent) kan du ersätta det med ett understreck (till exempel User_Agent).| Exempel: % {http_Connection}<br /><br />Exempelvärde: Keep-Alive | 
| Begär värden | %{host} | Anger värden som definierats i fråge-URL. | <www.mydomain.com> |
| Begäran-protokollet | %{request_protocol} | Anger protokollet som begäran. | HTTP/1.1 |
| Begäran-schema | %{scheme} | Anger schemat för begäran. |http |
| Begärande-URI (relativ) | %{request_uri} | Anger den relativa sökvägen, inklusive frågesträngen som definierats i begärande-URI. | /Marketing/foo.js?loggedin=true |
| Begärande-URI (relativ utan frågesträng) | % {URI: n} | Anger den relativa sökvägen till det begärda innehållet. <br /><br/>Viktig information:<br />– I den här relativa sökvägen utesluter frågesträngen.<br />– I den här relativa sökvägen återspeglar URL omskrivningar. En URL kommer skrivas på följande villkor:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL omskrivning funktion: Den här funktionen skriver om den relativa sökvägen som definierats i begärande-URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: Den här typen av begäran om att motsvarande CDN-URL. |/800001/corigin/rewrittendir/foo.js |
| Förfrågans URI | % {begäran} | Beskriver begäran. <br />Syntax: &lt;HTTP-metoden&gt; &lt;relativ sökväg&gt; &lt;HTTP-protokollet&gt; | Hämta /marketing/foo.js?loggedin=true HTTP/1.1 |
| Svaret huvudvärde | % {resp_&lt;ResponseHeader&gt;} | Returnerar värdet för rubriken identifieras av den &lt;ResponseHeader&gt; termen. <br /><br />Om namnet på svarshuvudet innehåller ett bindestreck (till exempel användar-Agent) kan du ersätta det med ett understreck (till exempel User_Agent). | Exempel: % {resp_Content_Length}<br /><br />Exempelvärde: 100 |

## <a name="usage"></a>Användning
I följande tabell beskrivs korrekt syntax för att ange en HTTP-variabel.


| Syntax | Exempel | Beskrivning |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Använd den här syntaxen för att få hela värdet för den angivna &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | % {värden} | Använd den här syntaxen för att ange fallet för hela värdet för den angivna &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Använda ett reguljärt uttryck för &lt;HTTPVariableDelimiterExpression&gt; om du vill ersätta, ta bort eller ändra en HTTP-variabelns värde. |

HTTP-variabelnamn har endast stöd för alfabetiska tecken och understreck. Konvertera tecken som inte stöds till understreck.

## <a name="delimiter-reference"></a>Referens för avgränsare
En avgränsare kan anges när en HTTP-variabel för att åstadkomma något av följande effekter:

- Omvandla värdet kopplat till variabeln.

     Exempel: Konvertera hela värdet till gemener.

- Ta bort värdet kopplat till variabeln.

- Ändra värdet kopplat till variabeln.

     Exempel: Använda reguljära uttryck för att ändra värdet som associeras med HTTP-variabel.

Avgränsare beskrivs i följande tabell.

| Avgränsare | Beskrivning |
| --------- | ----------- |
| := | Anger ett standardvärde kommer att tilldelas till variabeln när den är antingen: <br />-Saknas <br />-Värdet NULL. |
| :+ | Anger ett standardvärde kommer att tilldelas till variabeln när den har tilldelats ett värde. |
| : | Anger att en delsträng av värdet som tilldelas variabeln ska expanderas. |
| # | Anger att det mönster som anges efter den här avgränsare ska tas bort när det hittas i början av det värde som är associerade med variabeln. |
| % | Anger att det mönster som anges efter den här avgränsare ska tas bort när det hittas i slutet av värdet kopplat till variabeln. <br />Den här definitionen gäller endast när symbolen % används som avgränsare. |
| / | Avgränsar en HTTP-variabel eller ett mönster. |
| // | Sök och Ersätt alla förekomster av det angivna mönstret. |
| /= | Hitta, kopiera och skriva om alla förekomster av det angivna mönstret. |
| ,  | Konvertera värdet som associeras med HTTP-variabel till gemener. |
| ^ | Konvertera värdet som associeras med HTTP-variabel till versaler. |
| ,, | Konvertera alla förekomster av det angivna tecknet i värdet som associeras med HTTP-variabel till gemener. |
| ^^ | Konvertera alla förekomster av det angivna tecknet i värdet som associeras med HTTP-variabel till versaler. |

## <a name="exceptions"></a>Undantag
I följande tabell beskrivs omständigheter som den angivna texten inte behandlas som en HTTP-variabel.

| Tillstånd | Beskrivning | Exempel |
| --------- | ----------- | --------|
| Undantagstecken % symbol | Symbolen procent kan undantas genom att använda ett omvänt snedstreck. <br />Exempelvärde till höger behandlas som ett exakt värde och inte som en HTTP-variabel.| \%{host} |
| Okänd variabler | En tom sträng returneras alltid för okänd variabler. | % {unknownvariable} |
| Ogiltiga tecken eller syntax | Variabler som innehåller ogiltiga tecken eller syntax behandlas som exakta värden. <br /><br />Exempel #1: Det angivna värdet innehåller ett ogiltigt tecken (till exempel-). <br /><br />Exempel #2: Det angivna värdet innehåller en dubbel uppsättning av klammerparenteser. <br /><br />Exempel #3: Det angivna värdet saknar avslutande klammerparentes.<br /> | Exempel #1: % {resp_user-agent} <br /><br />Exempel #2: % {{värden}} <br /><br />Exempel #3: % {värden |
| Variabelnamnet som saknas | Ett NULL-värde returneras alltid när en variabel inte har angetts. | %{} |
| Överskridande tecken | Tecken som löper en variabel behandlas som exakta värden. <br />Exempelvärde till höger innehåller en avslutande klammerparentes som kommer att behandlas som ett literalvärde. | %{host}} |

## <a name="setting-default-header-values"></a>Ange standardvärden för rubrik
Ett standardvärde kan tilldelas till en rubrik när den uppfyller något av följande villkor:
- Saknas/Odefinierad
- Ange som NULL.

I följande tabell beskrivs hur du definierar ett standardvärde.

| Tillstånd | Syntax | Exempel | Beskrivning |
| --------- | ------ | --------| ----------- |
| Ange en rubrik till ett standardvärde när den uppfyller något av följande villkor: <br /><br />-Huvud saknas <br /><br />-Huvudets värde anges till NULL.| %{Variable:=Value} | %{http_referer:=unspecified} | Referer-rubrik kommer endast anges till *Ospecificerad* när den är saknas eller är inställd på NULL. Ingen åtgärd ska ske om den har ställts in. |
| Ange en rubrik till ett standardvärde när det saknas. | %{Variable=Value} | %{http_referer=unspecified} | Referer-rubrik kommer endast anges till *Ospecificerad* när det saknas. Ingen åtgärd ska ske om den har ställts in. |
| Ange rubriken till ett standardvärde när den inte uppfyller något av följande villkor: <br /><br />-Saknas<br /><br /> -Värdet NULL. | %{Variable:+Value} | % {http_referer: + Ospecificerad} | Referer-rubrik kommer endast anges till *Ospecificerad* när ett värde har tilldelats till den. Ingen åtgärd utförs om den saknas eller null. |

## <a name="manipulating-variables"></a>Ändra variabler
Variabler kan hanteras på följande sätt:

- Expandera delsträngar
- Ta bort mönster

### <a name="substring-expansion"></a>Delsträngen expansion
Som standard kommer att expandera en variabel till dess hela värdet. Använd följande syntax för att endast expandera en delsträng av variabelns värde.

`%<Variable>:<Offset>:<Length>}`

Viktig information:

- Värdet som tilldelas förskjutning termen avgör det första tecknet delsträngens:

     - Positivt: Det första tecknet delsträngens beräknas från det första tecknet i strängen.
     - Zero: Det första tecknet delsträngens är det första tecknet i strängen.
     - Negativt: Det första tecknet delsträngens beräknas från det sista tecknet i strängen.

- Delsträngens längd bestäms av den *längd* termen:

     - Utelämnas: Om du utesluter längd termen kan delsträngen som ska innehålla alla tecken mellan det första tecknet och slutet av strängen.
     - Positivt: Anger längden för delsträngen från det första tecknet till höger.
     - Negativt: Anger längden för delsträngen från det första tecknet till vänster.

#### <a name="example"></a>Exempel:

I följande exempel är beroende av följande exempel begärans-URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

Följande sträng visar olika metoder för att ändra variabler:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Baserat på exemplet fråge-URL, skapas ovan variabeln manipulering följande värde:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Borttagning av mönster
Text som matchar ett specifikt mönster kan tas bort från början eller slutet av en variabelns värde.

| Syntax | Åtgärd |
| ------ | ------ |
| % {Variabeln #Pattern} | Ta bort texten när det angivna mönstret hittas i början av en variabelns värde. |
| % {Variable % mönstret} | Ta bort texten när det angivna mönstret hittas i slutet av en variabelns värde. |

#### <a name="example"></a>Exempel:

I det här exempelscenariot i *request_uri* variabeln anges till:

`/800001/myorigin/marketing/product.html?language=en-US`

I följande tabell visar hur den här syntaxen fungerar.

| Exempelsyntax | Resultat | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Eftersom variabeln börjar med mönstret, ersattes. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Eftersom variabeln inte få med mönstret, var det ingen ändring.|

### <a name="find-and-replace"></a>Sök och Ersätt
Sök och Ersätt syntaxen beskrivs i följande tabell.

| Syntax | Åtgärd |
| ------ | ------ |
| %{Variable/Find/replace} | Sök och Ersätt första förekomsten av det angivna mönstret. |
| %{Variable//Find/replace} | Sök och Ersätt alla förekomster av det angivna mönstret. |
| %{Variable^} |Konvertera hela värdet till versaler. |
| %{Variable^Find} | Konvertera den första förekomsten av det angivna mönstret till versaler. |
| % {Variabeln} | Konvertera hela värdet till gemener. |
| % {Variabeln, hitta} | Konvertera den första förekomsten av det angivna mönstret till gemener. |

### <a name="find-and-rewrite"></a>Leta upp och skriv om
Använd den text som matchar det angivna mönstret när skriva om det för en variant på Sök och ersätt. Leta upp och skriv om syntaxen beskrivs i följande tabell.

| Syntax | Åtgärd |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Hitta, kopiera och skriva om alla förekomster av det angivna mönstret. |
| %{Variable/^Find/Rewrite} | Hitta, kopiera och skriva om det angivna mönstret när det uppstår i början av variabeln. |
| %{Variable/$Find/Rewrite} | Hitta, kopiera och skriva om det angivna mönstret när det uppstår i slutet av variabeln. |
| %{Variable/Find} | Hitta och ta bort alla förekomster av det angivna mönstret. |

Viktig information:

- Expandera text som matchar det angivna mönstret genom att ange ett dollartecken följt av ett hela heltal (till exempel $1).

- Du kan ange flera mönster. Mönstret nämnd anger det heltal som ska tilldelas till den. I följande exempel de första mönstret matchar ”www”,. det andra mönstret matchar andranivådomän och tredje mönstret matchar den översta domänen:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Ny värdet kan bestå av valfri kombination av text och dessa platshållare.

    I exemplet ovan värdnamnet är har skrivits till `cdn.$2.$3:80` (till exempel cdn.mydomain.com:80).

- I fallet med platshållare mönstret (till exempel $1) kan ändras via följande flaggor:
     - U: Versaler expanderade värdet.

         Exemplet syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Gemener expanderade värdet.

         Exemplet syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Mönstret måste ange en operator. Den angivna operatorn anger beteendet avbildning av mönstret:

     - `=`: Anger att alla förekomster av det angivna mönstret måste skapas och har skrivits.
     - `^`: Anger att endast text som börjar med det angivna mönstret ska hämtas.
     - `$`: Anger att endast text som slutar med det angivna mönstret är capture.
 
- Om du utelämnar den */omarbetning* värde, den text som matchar mönstret tas bort.


