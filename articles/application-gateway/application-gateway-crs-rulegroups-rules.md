---
title: Azure Application Gateway Brandvägg för webbaserade program CR regel grupper och regler
description: Den här sidan innehåller information om web brandväggen CR regeln programgrupper och regler.
documentationcenter: na
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 4/16/2018
ms.author: victorh
ms.openlocfilehash: 5ba291eaa93a48e3aadc11b1f0f7b48b01683b07
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Lista med web brandväggen CR regeln programgrupper och regler

Brandvägg för programmet Gateway webbaserade program (Brandvägg) skyddar webbprogram från vanliga säkerhetsrisker och trojaner. Detta görs via regler som har definierats baserat på OWASP core regeluppsättningarna 2.2.9 eller 3.0. De här reglerna kan inaktiveras på grundval av regeln av regeln. Den här artikeln innehåller aktuella regler och regeluppsättningar som erbjuds.

Följande tabeller är regelgrupper och regler som är tillgängliga när du använder Application Gateway med Brandvägg för webbaserade program.  Varje tabell representerar de regler som finns i en grupp för en viss CR-version.

## <a name="owasp30"></a> OWASP_3.0


### <a name="crs911"></a> <p x-ms-format-detection="none">BEGÄRAN-911-METODEN-TVINGANDE</p>

|ruleId|Beskrivning|
|---|---|
|911011|Regeln 911011|
|911012|Regeln 911012|
|911100|Metoden tillåts inte av en princip|
|911013|Regeln 911013|
|911014|Regeln 911014|
|911015|Regeln 911015|
|911016|Regeln 911016|
|911017|Regeln 911017|
|911018|Regeln 911018|


### <a name="crs913"></a> <p x-ms-format-detection="none">BEGÄRAN 913-SKANNER-IDENTIFIERING</p>

|ruleId|Beskrivning|
|---|---|
|913011|Regeln 913011|
|913012|Regeln 913012|
|913100|Hitta användaragenten som är associerade med säkerhetsskannern|
|913110|Hitta huvudet i begäran som är associerade med säkerhetsskannern|
|913120|Hitta begäran filename/argument som är associerade med säkerhetsskannern|
|913013|Regeln 913013|
|913014|Regeln 913014|
|913101|Hitta användaragenten som är associerade med skript/allmänna http-klienten|
|913102|Hitta användaragenten som är associerade med web crawler/bot|
|913015|Regeln 913015|
|913016|Regeln 913016|
|913017|Regeln 913017|
|913018|Regeln 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">BEGÄRAN-920-PROTOKOLL-TVINGANDE</p>

|ruleId|Beskrivning|
|---|---|
|920011|Regeln 920011|
|920012|Regeln 920012|
|920100|Ogiltig HTTP-begäran rad|
|920130|Det gick inte att tolka brödtexten i begäran.|
|920140|Multipart begäran brödtext kunde inte verifiera strikt = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} IH %@{MULTIPART_INVALID_HEADER_FOLDING} FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|920160|Content-Length-HTTP-huvudet är inte numerisk.|
|920170|GET eller HEAD begäran med Brödtextinnehåll.|
|920180|POST-begäran saknar Content-Length-huvud.|
|920190|Intervallet = ogiltig sista Byte-värde.|
|920210|Flera/motstridiga anslutningen huvud Data hittades.|
|920220|URL-kodning missbruk Attack försök|
|920240|URL-kodning missbruk Attack försök|
|920250|UTF8-Kodning används missbruk Attack försök|
|920260|Unicode-Full/halvbreddssiffror missbruk Attack försök|
|920270|Ogiltigt tecken i begäran (null-tecken)|
|920280|Begäran saknar ett värdhuvud|
|920290|Tom värdhuvud|
|920310|Begäran har en tom acceptera sidhuvud|
|920311|Begäran har en tom acceptera sidhuvud|
|920330|Tom användaren Agent sidhuvud|
|920340|Begäran som innehåller innehåll men saknar Content-Type-huvud|
|920350|Värdadressen är en numerisk IP-adress|
|920380|För många argument i begäran|
|920360|Argumentnamn är för långt|
|920370|Argumentvärdet är för lång|
|920390|Totalt antal argument storlek överskrider|
|920400|Överförda filen är för stor|
|920410|Totalt antal överförda filer är för stort|
|920420|Content-type för begäran tillåts inte av en princip|
|920430|HTTP-protokollversion tillåts inte av en princip|
|920440|URL-filnamnstillägget begränsas av princip|
|920450|HTTP-huvudet är begränsad av en princip (%@{MATCHED_VAR})|
|920013|Regeln 920013|
|920014|Regeln 920014|
|920200|Intervallet = för många fält (6 eller högre)|
|920201|Intervallet = för många fält för PDF-begäran (35 eller fler)|
|920230|Flera URL-kodning upptäcktes|
|920300|Begäran saknar en acceptera sidhuvud|
|920271|Ogiltigt tecken i begäran (icke utskrivbara tecken)|
|920320|Agent-huvud saknas användare|
|920015|Regeln 920015|
|920016|Regeln 920016|
|920272|Ogiltigt tecken i begäran (utanför utskrivbara tecken nedan ascii 127)|
|920017|Regeln 920017|
|920018|Regeln 920018|
|920202|Intervallet = för många fält för PDF-begäran (6 eller högre)|
|920273|Ogiltigt tecken i begäran (utanför mycket strikta)|
|920274|Ogiltigt tecken i begärandehuvuden (utanför mycket strikta)|
|920460|Regeln 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">BEGÄRAN-921-PROTOKOLL-ATTACK</p>

|ruleId|Beskrivning|
|---|---|
|921011|Regeln 921011|
|921012|Regeln 921012|
|921100|HTTP-begäransmuggling Attack.|
|921110|HTTP-begäran Smuggling Attack|
|921120|HTTP-svar dela Attack|
|921130|HTTP-svar dela Attack|
|921140|HTTP-huvudet Injection Attack via rubriker|
|921150|HTTP-huvudet Injection Attack via nyttolasten (CR/LF identifierat)|
|921160|HTTP-huvudet Injection Attack via nyttolasten (CR/LF och rubriknamnet identifierat)|
|921013|Regeln 921013|
|921014|Regeln 921014|
|921151|HTTP-huvudet Injection Attack via nyttolasten (CR/LF identifierat)|
|921015|Regeln 921015|
|921016|Regeln 921016|
|921170|Regeln 921170|
|921180|HTTP-parametern avseende (% @{TX.1})|
|921017|Regeln 921017|
|921018|Regeln 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">BEGÄRAN-930-PROGRAM-ATTACK-LFI</p>

|ruleId|Beskrivning|
|---|---|
|930011|Regeln 930011|
|930012|Regeln 930012|
|930100|Sökvägen Traversal Attack (/... /)|
|930110|Sökvägen Traversal Attack (/... /)|
|930120|OS-filen åtkomstförsök|
|930130|Begränsad filen åtkomstförsök|
|930013|Regeln 930013|
|930014|Regeln 930014|
|930015|Regeln 930015|
|930016|Regeln 930016|
|930017|Regeln 930017|
|930018|Regeln 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">BEGÄRAN-931-PROGRAM-ATTACK-RFI</p>

|ruleId|Beskrivning|
|---|---|
|931011|Regeln 931011|
|931012|Regeln 931012|
|931100|Möjliga Remote filen inkludering (RFI)-Attack = URL-parametern med IP-adress|
|931110|Möjliga Remote filen inkludering (RFI)-Attack = vanliga RFI sårbara parameternamnet används w-URL nyttolast|
|931120|Möjliga fjärrfil inkludering (RFI) Attack = URL nyttolast används w/efterföljande fråga Markera tecken (?)|
|931013|Regeln 931013|
|931014|Regeln 931014|
|931130|Möjliga fjärrfil inkludering (RFI) Attack = inaktivera domän referenslänk|
|931015|Regeln 931015|
|931016|Regeln 931016|
|931017|Regeln 931017|
|931018|Regeln 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">BEGÄRAN-932-PROGRAM-ATTACK-M</p>

|ruleId|Beskrivning|
|---|---|
|932011|Regeln 932011|
|932012|Regeln 932012|
|932120|Fjärrkörning = finns Windows PowerShell-kommandot|
|932130|Fjärrkörning = Unix uttrycket hittades|
|932140|Fjärrkörning = Windows för / om kommandot kunde hittas|
|932160|Fjärrkörning = Unix Shell-kod hittades|
|932170|Fjärrkörning = Shellshock (CVE-2014-6271)|
|932171|Fjärrkörning = Shellshock (CVE-2014-6271)|
|932013|Regeln 932013|
|932014|Regeln 932014|
|932015|Regeln 932015|
|932016|Regeln 932016|
|932017|Regeln 932017|
|932018|Regeln 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">BEGÄRAN-933-PROGRAM-ATTACK-PHP</p>

|ruleId|Beskrivning|
|---|---|
|933011|Regeln 933011|
|933012|Regeln 933012|
|933100|PHP Injection Attack = inledande/avslutande tagg hittades|
|933110|PHP Injection Attack = PHP-skript ladda upp filen hittades|
|933120|PHP Injection Attack = Configuration direktiv hittades|
|933130|PHP Injection Attack = variabler som finns|
|933150|PHP Injection Attack = hög risk PHP funktionsnamn hittades|
|933160|PHP Injection Attack = hög risk PHP-funktionsanrop som finns|
|933180|PHP Injection Attack = variabeln funktionsanrop som finns|
|933013|Regeln 933013|
|933014|Regeln 933014|
|933151|PHP Injection Attack = medelstor Risk PHP funktionsnamn hittades|
|933015|Regeln 933015|
|933016|Regeln 933016|
|933131|PHP Injection Attack = variabler som finns|
|933161|PHP Injection Attack = låg-värde PHP-funktionsanrop som finns|
|933111|PHP Injection Attack = PHP-skript ladda upp filen hittades|
|933017|Regeln 933017|
|933018|Regeln 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">BEGÄRAN-941-PROGRAM-ATTACK-XSS</p>

|ruleId|Beskrivning|
|---|---|
|941011|Regeln 941011|
|941012|Regeln 941012|
|941100|XSS Attack identifieras via libinjection|
|941110|XSS Filter - kategori 1 = skript taggen Vector|
|941130|XSS Filter - kategori 3 = attributet Vector|
|941140|XSS Filter - kategori 4 = Javascript-URI-vektorn|
|941150|XSS Filter - kategori 5 = otillåten HTML-attribut|
|941180|Noden verifieraren svartlistat nyckelord|
|941190|IE XSS filter - Attack upptäcktes.|
|941200|IE XSS filter - Attack upptäcktes.|
|941210|IE XSS filter - Attack upptäcktes.|
|941220|IE XSS filter - Attack upptäcktes.|
|941230|IE XSS filter - Attack upptäcktes.|
|941240|IE XSS filter - Attack upptäcktes.|
|941260|IE XSS filter - Attack upptäcktes.|
|941270|IE XSS filter - Attack upptäcktes.|
|941280|IE XSS filter - Attack upptäcktes.|
|941290|IE XSS filter - Attack upptäcktes.|
|941300|IE XSS filter - Attack upptäcktes.|
|941310|US-ASCII felaktig kodning XSS Filter - Attack upptäcktes.|
|941350|UTF-7 kodning IE XSS - Attack upptäcktes.|
|941013|Regeln 941013|
|941014|Regeln 941014|
|941320|Möjliga XSS Attack påträffas - hanterare för HTML-tagg|
|941015|Regeln 941015|
|941016|Regeln 941016|
|941017|Regeln 941017|
|941018|Regeln 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">BEGÄRAN-942-PROGRAM-ATTACK-SQLI</p>

|ruleId|Beskrivning|
|---|---|
|942011|Regeln 942011|
|942012|Regeln 942012|
|942100|SQL Injection Attack identifieras via libinjection|
|942140|SQL Injection Attack = DB namn upptäcktes|
|942160|Identifierar hemlig sqli testerna via sleep() eller benchmark().|
|942170|Identifierar SQL injection prestandamått och viloläge försöker inklusive villkorlig frågor|
|942230|Upptäcker om villkorlig SQL injection|
|942270|Söker efter grundläggande sql injection. Vanliga attack sträng för mysql oracle och andra.|
|942290|Söker efter grundläggande MongoDB SQL injection försök|
|942320|Identifierar MySQL och PostgreSQL lagrad procedur eller funktion injektioner|
|942350|Identifierar MySQL UDF injection och andra datastruktur/modifiering försöker|
|942013|Regeln 942013|
|942014|Regeln 942014|
|942150|SQL Injection Attack|
|942410|SQL Injection Attack|
|942440|SQL-kommentar sekvens upptäcktes.|
|942450|SQL hexadecimal kodning identifieras|
|942015|Regeln 942015|
|942016|Regeln 942016|
|942251|Identifierar HAVING injektioner|
|942460|Avisering för identifiering av meta tecknet Avvikelseidentifiering - repetitiva icke-alfanumeriska tecken|
|942017|Regeln 942017|
|942018|Regeln 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|ruleId|Beskrivning|
|---|---|
|943011|Regeln 943011|
|943012|Regeln 943012|
|943100|En Session upptagningen Attack = ange värden för cookies i HTML|
|943110|En Session upptagningen Attack = SessionID parameternamnet med referent av domän|
|943120|En Session upptagningen Attack = SessionID parameternamnet med ingen referent|
|943013|Regeln 943013|
|943014|Regeln 943014|
|943015|Regeln 943015|
|943016|Regeln 943016|
|943017|Regeln 943017|
|943018|Regeln 943018|

##<a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|ruleId|Beskrivning|
|---|---|
|960911|Ogiltig HTTP-begäran rad|
|981227|Apache fel = ogiltig URI i begäran.|
|960912|Det gick inte att tolka brödtexten i begäran.|
|960914|Multipart begäran brödtext kunde inte verifiera strikt = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} IH %@{MULTIPART_INVALID_HEADER_FOLDING} FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|960915|Multipart-parsern upptäckte en möjliga omatchade gräns.|
|960016|Content-Length-HTTP-huvudet är inte numerisk.|
|960011|GET eller HEAD begäran med Brödtextinnehåll.|
|960012|POST-begäran saknar Content-Length-huvud.|
|960902|Ogiltig användning av identiteten kodning.|
|960022|Förvänta dig huvud är inte tillåtet för HTTP 1.0.|
|960020|Pragma-rubriken kräver Cache-Control-huvudet för HTTP/1.1-begäranden.|
|958291|Intervallet = fältet finns och börjar med 0.|
|958230|Intervallet = ogiltig sista Byte-värde.|
|958295|Flera/motstridiga anslutningen huvud Data hittades.|
|950107|URL-kodning missbruk Attack försök|
|950109|Flera URL-kodning upptäcktes|
|950108|URL-kodning missbruk Attack försök|
|950801|UTF8-Kodning används missbruk Attack försök|
|950116|Unicode-Full/halvbreddssiffror missbruk Attack försök|
|960901|Ogiltigt tecken i begäran|
|960018|Ogiltigt tecken i begäran|

### <a name="crs21"></a> crs_21_protocol_anomalies

|ruleId|Beskrivning|
|---|---|
|960008|Begäran saknar ett värdhuvud|
|960007|Tom värdhuvud|
|960015|Begäran saknar en acceptera sidhuvud|
|960021|Begäran har en tom acceptera sidhuvud|
|960009|Begäran saknar en Agent rubrik för användaren|
|960006|Tom användaren Agent sidhuvud|
|960904|Begäran som innehåller innehåll men saknar Content-Type-huvud|
|960017|Värdadressen är en numerisk IP-adress|

### <a name="crs23"></a> crs_23_request_limits

|ruleId|Beskrivning|
|---|---|
|960209|Argumentnamn är för långt|
|960208|Argumentvärdet är för lång|
|960335|För många argument i begäran|
|960341|Totalt antal argument storlek överskrider|
|960342|Överförda filen är för stor|
|960343|Totalt antal överförda filer är för stort|

### <a name="crs30"></a> crs_30_http_policy

|ruleId|Beskrivning|
|---|---|
|960032|Metoden tillåts inte av en princip|
|960010|Content-type för begäran tillåts inte av en princip|
|960034|HTTP-protokollversion tillåts inte av en princip|
|960035|URL-filnamnstillägget begränsas av princip|
|960038|HTTP-huvudet är begränsad av princip|

### <a name="crs35"></a> crs_35_bad_robots

|ruleId|Beskrivning|
|---|---|
|990002|Begäran anger en Säkerhetsskannern genomsöks platsen|
|990901|Begäran anger en Säkerhetsskannern genomsöks platsen|
|990902|Begäran anger en Säkerhetsskannern genomsöks platsen|
|990012|Falsk webbplats crawler|

### <a name="crs40"></a> crs_40_generic_attacks

|ruleId|Beskrivning|
|---|---|
|960024|Avisering för identifiering av meta tecknet Avvikelseidentifiering - repetitiva icke-alfanumeriska tecken|
|950008|Injektion av odokumenterade ColdFusion taggar|
|950010|LDAP-Injection Attack|
|950011|SSI injection Attack|
|950018|Universal PDF XSS URL upptäcktes.|
|950019|E-Injection Attack|
|950012|HTTP-begäransmuggling Attack.|
|950910|HTTP-svar dela Attack|
|950911|HTTP-svar dela Attack|
|950117|Fjärrlagring inkludering Attack|
|950118|Fjärrlagring inkludering Attack|
|950119|Fjärrlagring inkludering Attack|
|950120|Möjliga fjärrfil inkludering (RFI) Attack = inaktivera domän referenslänk|
|981133|Regeln 981133|
|981134|Regeln 981134|
|950009|Sessionen upptagningen Attack|
|950003|Sessionen upptagningen|
|950000|Sessionen upptagningen|
|950005|Fjärrlagring åtkomstförsök|
|950002|Systemåtkomst-kommando|
|950006|System kommandot Injection|
|959151|PHP Injection Attack|
|958976|PHP Injection Attack|
|958977|PHP Injection Attack|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|ruleId|Beskrivning|
|---|---|
|981231|SQL-kommentar sekvens upptäcktes.|
|981260|SQL hexadecimal kodning identifieras|
|981320|SQL Injection Attack = DB namn upptäcktes|
|981300|Regeln 981300|
|981301|Regeln 981301|
|981302|Regeln 981302|
|981303|Regeln 981303|
|981304|Regeln 981304|
|981305|Regeln 981305|
|981306|Regeln 981306|
|981307|Regeln 981307|
|981308|Regeln 981308|
|981309|Regeln 981309|
|981310|Regeln 981310|
|981311|Regeln 981311|
|981312|Regeln 981312|
|981313|Regeln 981313|
|981314|Regeln 981314|
|981315|Regeln 981315|
|981316|Regeln 981316|
|981317|Avisering om SQL SELECT-instruktion Avvikelseidentifiering identifiering|
|950007|Hemlig SQL Injection Attack|
|950001|SQL Injection Attack|
|950908|SQL Injection Attack.|
|959073|SQL Injection Attack|
|981272|Identifierar hemlig sqli testerna via sleep() eller benchmark().|
|981250|Identifierar SQL injection prestandamått och viloläge försöker inklusive villkorlig frågor|
|981241|Upptäcker om villkorlig SQL injection|
|981276|Söker efter grundläggande sql injection. Vanliga attack sträng för mysql oracle och andra.|
|981270|Söker efter grundläggande MongoDB SQL injection försök|
|981253|Identifierar MySQL och PostgreSQL lagrad procedur eller funktion injektioner|
|981251|Identifierar MySQL UDF injection och andra datastruktur/modifiering försöker|

### <a name="crs41xss"></a> crs_41_xss_attacks

|ruleId|Beskrivning|
|---|---|
|973336|XSS Filter - kategori 1 = skript taggen Vector|
|973338|XSS Filter - kategori 3 = Javascript-URI-vektorn|
|981136|Regeln 981136|
|981018|Regeln 981018|
|958016|Globala webbplatsskript (XSS) Attack|
|958414|Globala webbplatsskript (XSS) Attack|
|958032|Globala webbplatsskript (XSS) Attack|
|958026|Globala webbplatsskript (XSS) Attack|
|958027|Globala webbplatsskript (XSS) Attack|
|958054|Globala webbplatsskript (XSS) Attack|
|958418|Globala webbplatsskript (XSS) Attack|
|958034|Globala webbplatsskript (XSS) Attack|
|958019|Globala webbplatsskript (XSS) Attack|
|958013|Globala webbplatsskript (XSS) Attack|
|958408|Globala webbplatsskript (XSS) Attack|
|958012|Globala webbplatsskript (XSS) Attack|
|958423|Globala webbplatsskript (XSS) Attack|
|958002|Globala webbplatsskript (XSS) Attack|
|958017|Globala webbplatsskript (XSS) Attack|
|958007|Globala webbplatsskript (XSS) Attack|
|958047|Globala webbplatsskript (XSS) Attack|
|958410|Globala webbplatsskript (XSS) Attack|
|958415|Globala webbplatsskript (XSS) Attack|
|958022|Globala webbplatsskript (XSS) Attack|
|958405|Globala webbplatsskript (XSS) Attack|
|958419|Globala webbplatsskript (XSS) Attack|
|958028|Globala webbplatsskript (XSS) Attack|
|958057|Globala webbplatsskript (XSS) Attack|
|958031|Globala webbplatsskript (XSS) Attack|
|958006|Globala webbplatsskript (XSS) Attack|
|958033|Globala webbplatsskript (XSS) Attack|
|958038|Globala webbplatsskript (XSS) Attack|
|958409|Globala webbplatsskript (XSS) Attack|
|958001|Globala webbplatsskript (XSS) Attack|
|958005|Globala webbplatsskript (XSS) Attack|
|958404|Globala webbplatsskript (XSS) Attack|
|958023|Globala webbplatsskript (XSS) Attack|
|958010|Globala webbplatsskript (XSS) Attack|
|958411|Globala webbplatsskript (XSS) Attack|
|958422|Globala webbplatsskript (XSS) Attack|
|958036|Globala webbplatsskript (XSS) Attack|
|958000|Globala webbplatsskript (XSS) Attack|
|958018|Globala webbplatsskript (XSS) Attack|
|958406|Globala webbplatsskript (XSS) Attack|
|958040|Globala webbplatsskript (XSS) Attack|
|958052|Globala webbplatsskript (XSS) Attack|
|958037|Globala webbplatsskript (XSS) Attack|
|958049|Globala webbplatsskript (XSS) Attack|
|958030|Globala webbplatsskript (XSS) Attack|
|958041|Globala webbplatsskript (XSS) Attack|
|958416|Globala webbplatsskript (XSS) Attack|
|958024|Globala webbplatsskript (XSS) Attack|
|958059|Globala webbplatsskript (XSS) Attack|
|958417|Globala webbplatsskript (XSS) Attack|
|958020|Globala webbplatsskript (XSS) Attack|
|958045|Globala webbplatsskript (XSS) Attack|
|958004|Globala webbplatsskript (XSS) Attack|
|958421|Globala webbplatsskript (XSS) Attack|
|958009|Globala webbplatsskript (XSS) Attack|
|958025|Globala webbplatsskript (XSS) Attack|
|958413|Globala webbplatsskript (XSS) Attack|
|958051|Globala webbplatsskript (XSS) Attack|
|958420|Globala webbplatsskript (XSS) Attack|
|958407|Globala webbplatsskript (XSS) Attack|
|958056|Globala webbplatsskript (XSS) Attack|
|958011|Globala webbplatsskript (XSS) Attack|
|958412|Globala webbplatsskript (XSS) Attack|
|958008|Globala webbplatsskript (XSS) Attack|
|958046|Globala webbplatsskript (XSS) Attack|
|958039|Globala webbplatsskript (XSS) Attack|
|958003|Globala webbplatsskript (XSS) Attack|
|973300|Möjliga XSS Attack påträffas - hanterare för HTML-tagg|
|973301|XSS-angrepp upptäcktes|
|973302|XSS-angrepp upptäcktes|
|973303|XSS-angrepp upptäcktes|
|973304|XSS-angrepp upptäcktes|
|973305|XSS-angrepp upptäcktes|
|973306|XSS-angrepp upptäcktes|
|973307|XSS-angrepp upptäcktes|
|973308|XSS-angrepp upptäcktes|
|973309|XSS-angrepp upptäcktes|
|973311|XSS-angrepp upptäcktes|
|973313|XSS-angrepp upptäcktes|
|973314|XSS-angrepp upptäcktes|
|973331|IE XSS filter - Attack upptäcktes.|
|973315|IE XSS filter - Attack upptäcktes.|
|973330|IE XSS filter - Attack upptäcktes.|
|973327|IE XSS filter - Attack upptäcktes.|
|973326|IE XSS filter - Attack upptäcktes.|
|973346|IE XSS filter - Attack upptäcktes.|
|973345|IE XSS filter - Attack upptäcktes.|
|973324|IE XSS filter - Attack upptäcktes.|
|973323|IE XSS filter - Attack upptäcktes.|
|973348|IE XSS filter - Attack upptäcktes.|
|973321|IE XSS filter - Attack upptäcktes.|
|973320|IE XSS filter - Attack upptäcktes.|
|973318|IE XSS filter - Attack upptäcktes.|
|973317|IE XSS filter - Attack upptäcktes.|
|973329|IE XSS filter - Attack upptäcktes.|
|973328|IE XSS filter - Attack upptäcktes.|

### <a name="crs42"></a> crs_42_tight_security

|ruleId|Beskrivning|
|---|---|
|950103|Sökvägen Traversal Attack|

### <a name="crs45"></a> crs_45_trojans

|ruleId|Beskrivning|
|---|---|
|950110|Bakdörrsåtkomst|
|950921|Bakdörrsåtkomst|
|950922|Bakdörrsåtkomst|

## <a name="next-steps"></a>Nästa steg

Lär dig hur du inaktiverar Brandvägg regler genom att besöka: [anpassa Brandvägg regler](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png