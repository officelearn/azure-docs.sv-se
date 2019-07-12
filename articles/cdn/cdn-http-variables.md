---
title: HTTP-variabler för Azure CDN regelmotor | Microsoft Docs
description: HTTP-variabler kan du hämta metadata för HTTP-begäranden och svar.
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
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593819"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>HTTP-variabler för Azure CDN regelmotor
HTTP-variabler innehåller innebär genom vilka du kan hämta metadata för HTTP-begäranden och svar. Dessa metadata kan sedan användas för att ändra dynamiskt en begäran eller ett svar. Användning av HTTP-variabler är begränsat till följande regler motorn funktioner:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Ändra begärandehuvud i klienten](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Ändra klienten svarshuvud](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL-omdirigering](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL-omskrivning](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definitioner
I följande tabell beskrivs de HTTP-variablerna. Ett tomt värde returneras när GEO-metadata (till exempel postnummer) är inte tillgänglig för en viss begäran.


| Namn | Variabel | Beskrivning | Exempelvärde |
| ---- | -------- | ----------- | ------------ |
| ASN (begär) | %{geo_asnum} | Anger den som begär som tal. <br /><br />**Inaktuella:** % {virt_dst_asnum}. <br />Den här variabeln är inaktuell och ersatts med % {geo_asnum}. Även om en regel som använder den här föråldrad variabeln fortsätter att fungera, bör du uppdatera den om du vill använda den nya variabeln. | AS15133 |
| Stad (begär) | %{geo_city} | Anger den som begär stad. | Los Angeles |
| Kontinent (begär) | %{geo_continent} | Anger den som begär kontinent via en förkortning. <br />Giltiga värden är: <br />AF: Afrika<br />PRECIS SOM: Asien<br />EU: Europa<br />NA: Nordamerika<br />OC: Oceanien<br />SA: Sydamerika<br /><br />**Inaktuella:** % {virt_dst_continent}. <br />Den här variabeln är inaktuell och ersatts med % {geo_continent}. <br />Även om en regel som använder den här föråldrad variabeln fortsätter att fungera, bör du uppdatera den om du vill använda den nya variabeln.| Gäller inte |
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
| Begärande-URI | % {begäran} | Beskriver begäran. <br />Syntax: &lt;HTTP-metoden&gt; &lt;relativ sökväg&gt; &lt;HTTP-protokollet&gt; | Hämta /marketing/foo.js?loggedin=true HTTP/1.1 |
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
| , | Konvertera värdet som associeras med HTTP-variabel till gemener. |
| ^ | Konvertera värdet som associeras med HTTP-variabel till versaler. |
| ,, | Konvertera alla förekomster av det angivna tecknet i värdet som associeras med HTTP-variabel till gemener. |
| ^^ | Konvertera alla förekomster av det angivna tecknet i värdet som associeras med HTTP-variabel till versaler. |

## <a name="exceptions"></a>Undantag
I följande tabell beskrivs omständigheter som den angivna texten inte behandlas som en HTTP-variabel.

| Tillstånd | Beskrivning | Exempel |
| --------- | ----------- | --------|
| Undantagstecken % symbol | Symbolen procent kan undantas genom att använda ett omvänt snedstreck. <br />Exempelvärde till höger behandlas som ett exakt värde och inte som en HTTP-variabel.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| Okänd variabler | En tom sträng returneras alltid för okänd variabler. | % {unknown_variable} |
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
| Ange en rubrik till ett standardvärde när den uppfyller något av följande villkor: <br /><br />-Huvud saknas <br /><br />-Huvudets värde anges till NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | Referent-huvudet kan endast anges till *Ospecificerad* när den är saknas eller är inställd på NULL. Ingen åtgärd ska ske om den har ställts in. |
| Ange en rubrik till ett standardvärde när det saknas. | %{Variable=Value} | %{http_referrer=unspecified} | Referent-huvudet kan endast anges till *Ospecificerad* när det saknas. Ingen åtgärd ska ske om den har ställts in. |
| Ange rubriken till ett standardvärde när den inte uppfyller något av följande villkor: <br /><br />-Saknas<br /><br /> -Värdet NULL. | %{Variable:+Value} | % {http_referrer: + Ospecificerad} | Referent-huvudet kan endast anges till *Ospecificerad* när ett värde har tilldelats till den. Ingen åtgärd utförs om den saknas eller null. |

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

| Syntax | Action |
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

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/replace} | Sök och Ersätt första förekomsten av det angivna mönstret. |
| %{Variable//Find/replace} | Sök och Ersätt alla förekomster av det angivna mönstret. |
| %{Variable^} |Konvertera hela värdet till versaler. |
| %{Variable^Find} | Konvertera den första förekomsten av det angivna mönstret till versaler. |
| % {Variabeln} | Konvertera hela värdet till gemener. |
| % {Variabeln, hitta} | Konvertera den första förekomsten av det angivna mönstret till gemener. |

### <a name="find-and-rewrite"></a>Leta upp och skriv om
Använd den text som matchar det angivna mönstret när skriva om det för en variant på Sök och ersätt. Leta upp och skriv om syntaxen beskrivs i följande tabell.

| Syntax | Action |
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


