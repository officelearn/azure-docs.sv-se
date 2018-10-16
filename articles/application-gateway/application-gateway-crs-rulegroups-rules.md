---
title: Azure Application Gateway Brandvägg för webbaserade program CRS regeln grupper och regler
description: Den här sidan innehåller information om web application firewall tillhandahållna CRS-regelgrupper och -regler.
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
ms.openlocfilehash: 15a86410e8ca853c2ca2431cb9a62de628972703
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320109"
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Lista med web application firewall tillhandahållna CRS-regelgrupper och -regler

Application Gateway waf (WAF) skyddar webbprogram mot vanliga säkerhetsproblem och hot på Internet. Detta görs via regler som har definierats utifrån OWASP core rule sets 2.2.9 eller 3.0. Dessa regler kan inaktiveras på basis av regeln av regeln. Den här artikeln innehåller de aktuella regler och regeluppsättningar som erbjuds.

Följande tabeller är regelgrupper och -regler som är tillgängliga när du använder Application Gateway med brandväggen för webbaserade program.  Varje tabell representerar de regler som finns i en regelgrupp för en specifik CRS-version.

## <a name="owasp30"></a> OWASP_3.0


### <a name="crs911"></a> <p x-ms-format-detection="none">BEGÄRAN-911-METODEN-TVINGANDE</p>

|regel-ID|Beskrivning|
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

|regel-ID|Beskrivning|
|---|---|
|913011|Regeln 913011|
|913012|Regeln 913012|
|913100|Hitta användar-Agent som är associerade med Säkerhetsskanner|
|913110|Hitta huvudet i begäran som är associerade med Säkerhetsskanner|
|913120|Hitta begäran filename/argument som är associerade med Säkerhetsskanner|
|913013|Regeln 913013|
|913014|Regeln 913014|
|913101|Hitta användar-Agent som är associerade med skript/generisk HTTP-klient|
|913102|Hitta användar-Agent som är associerade med web crawler/robot|
|913015|Regeln 913015|
|913016|Regeln 913016|
|913017|Regeln 913017|
|913018|Regeln 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">BEGÄRAN-920-PROTOCOL-TVINGANDE</p>

|regel-ID|Beskrivning|
|---|---|
|920011|Regeln 920011|
|920012|Regeln 920012|
|920100|Ogiltig HTTP-begäran-rad|
|920130|Det gick inte att parsa begärandetexten.|
|920140|Flera delar begäran brödtext misslyckades strikt verifiering = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} IH %@{MULTIPART_INVALID_HEADER_FOLDING} FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|920160|Content-Length-HTTP-huvud är inte numeriskt.|
|920170|GET eller HEAD-begäran med innehåll i Begärandetexten.|
|920180|POST-begäran saknas Content-Length-huvud.|
|920190|Intervall = ogiltig sista Byte-värde.|
|920210|Flera/motstridiga anslutning rubrik Data hittades.|
|920220|URL-kodning missbruk Angreppsförsök|
|920240|URL-kodning missbruk Angreppsförsök|
|920250|UTF8-Kodning missbruk Angreppsförsök|
|920260|Angreppsförsök för Unicode fullständig/halv bredd missbruk|
|920270|Ogiltigt tecken i begäran (null-tecken)|
|920280|Begäran saknar en värdrubrik|
|920290|Tom värdhuvud|
|920310|Begäran har en tom acceptera rubrik|
|920311|Begäran har en tom acceptera rubrik|
|920330|Tom användaren Agent-huvud|
|920340|Begäran som innehåller innehåll men saknas Content-Type-huvud|
|920350|Värdhuvud är en numerisk IP-adress|
|920380|För många argument i begäran|
|920360|Argumentnamnet är för långt|
|920370|Argumentvärdet är för långt|
|920390|Totalt antal argument storlek har överskridits|
|920400|Överförda filstorleken är för stor|
|920410|Totalt antal överförda filer är för stort|
|920420|Innehållstyp för begäran tillåts inte av princip|
|920430|HTTP-protokollversion tillåts inte av princip|
|920440|URL-filtillägg är begränsad av princip|
|920450|HTTP-huvud är begränsad av principen (%@{MATCHED_VAR})|
|920013|Regeln 920013|
|920014|Regeln 920014|
|920200|Intervall = för många fält (6 eller mer)|
|920201|Intervall = för många fält för pdf-begäran (35 eller mer)|
|920230|Flera URL-kodning har identifierats|
|920300|Begäran saknar en acceptera rubrik|
|920271|Ogiltigt tecken i begäran (icke utskrivbara tecken)|
|920320|Agent-huvud saknas användare|
|920015|Regeln 920015|
|920016|Regeln 920016|
|920272|Ogiltigt tecken i begäran (utanför utskrivbara tecken nedan ascii 127)|
|920017|Regeln 920017|
|920018|Regeln 920018|
|920202|Intervall = för många fält för pdf-begäran (6 eller mer)|
|920273|Ogiltigt tecken i begäran (utanför strikt set)|
|920274|Ogiltigt tecken i begärandehuvuden (utanför strikt set)|
|920460|Regeln 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">BEGÄRAN-921-PROTOCOL-ATTACK</p>

|regel-ID|Beskrivning|
|---|---|
|921011|Regeln 921011|
|921012|Regeln 921012|
|921100|HTTP-begäran som Kommandoinmatning Attack.|
|921110|HTTP-begäran Huvudinmatning Attack|
|921120|HTTP-svar uppdelningen av Attack|
|921130|HTTP-svar uppdelningen av Attack|
|921140|HTTP-huvud-angrepp via rubriker|
|921150|HTTP-huvud-angrepp via nyttolast (CR/LF har identifierats)|
|921160|HTTP-huvud-angrepp via nyttolast (CR/LF och rubriknamn har identifierats)|
|921013|Regeln 921013|
|921014|Regeln 921014|
|921151|HTTP-huvud-angrepp via nyttolast (CR/LF har identifierats)|
|921015|Regeln 921015|
|921016|Regeln 921016|
|921170|Regeln 921170|
|921180|HTTP-parametern avseende (% @{TX.1})|
|921017|Regeln 921017|
|921018|Regeln 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">BEGÄRAN-930-PROGRAM-ATTACK-LFI</p>

|regel-ID|Beskrivning|
|---|---|
|930011|Regeln 930011|
|930012|Regeln 930012|
|930100|Path Traversal Attack (/... /)|
|930110|Path Traversal Attack (/... /)|
|930120|OS-filen åtkomstförsök|
|930130|Begränsad filen åtkomstförsök|
|930013|Regeln 930013|
|930014|Regeln 930014|
|930015|Regeln 930015|
|930016|Regeln 930016|
|930017|Regeln 930017|
|930018|Regeln 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">BEGÄRAN-931-PROGRAM-ATTACK-RFI</p>

|regel-ID|Beskrivning|
|---|---|
|931011|Regeln 931011|
|931012|Regeln 931012|
|931100|Möjliga Remote File inkludering (RFI)-Attack = URL-Parameter med IP-adress|
|931110|Möjliga Remote File inkludering (RFI)-Attack = vanliga RFI sårbara parameternamnet används w/URL nyttolast|
|931120|Möjliga fjärrfil inkludering (RFI) Attack = URL: en nyttolast används w/avslutande fråga Mark tecken (?)|
|931013|Regeln 931013|
|931014|Regeln 931014|
|931130|Möjliga fjärrfil inkludering (RFI) Attack = av domän/referenslänk|
|931015|Regeln 931015|
|931016|Regeln 931016|
|931017|Regeln 931017|
|931018|Regeln 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">BEGÄRAN-932-PROGRAM-ATTACK-M</p>

|regel-ID|Beskrivning|
|---|---|
|932011|Regeln 932011|
|932012|Regeln 932012|
|932120|Fjärrkörning = Windows PowerShell-kommandot hittades|
|932130|Fjärrkörning = Unix Shell uttryck hittades|
|932140|Fjärrkörning = Windows för / om kommandot hittades|
|932160|Fjärrkörning = Unix Shell kod hittades|
|932170|Fjärrkörning = Shellshock (CVE-2014-6271)|
|932171|Fjärrkörning = Shellshock (CVE-2014-6271)|
|932013|Regeln 932013|
|932014|Regeln 932014|
|932015|Regeln 932015|
|932016|Regeln 932016|
|932017|Regeln 932017|
|932018|Regeln 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">BEGÄRAN-933-PROGRAM-ATTACK – PHP</p>

|regel-ID|Beskrivning|
|---|---|
|933011|Regeln 933011|
|933012|Regeln 933012|
|933100|PHP-angrepp = inledande/avslutande tagg hittades|
|933110|PHP-angrepp = Filuppladdning för PHP-skript hittades|
|933120|PHP-angrepp = konfiguration direktiv hittades|
|933130|PHP-angrepp = variabler hittades|
|933150|PHP-angrepp = med hög risk PHP funktionsnamn hittades|
|933160|PHP-angrepp = med hög risk PHP-funktionsanrop som finns|
|933180|PHP-angrepp = variabeln funktionsanrop som finns|
|933013|Regeln 933013|
|933014|Regeln 933014|
|933151|PHP-angrepp = sådan PHP funktionsnamn hittades|
|933015|Regeln 933015|
|933016|Regeln 933016|
|933131|PHP-angrepp = variabler hittades|
|933161|PHP-angrepp = Versionsprocess PHP-funktionsanrop som finns|
|933111|PHP-angrepp = Filuppladdning för PHP-skript hittades|
|933017|Regeln 933017|
|933018|Regeln 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">BEGÄRAN-941-PROGRAM-ATTACK-XSS</p>

|regel-ID|Beskrivning|
|---|---|
|941011|Regeln 941011|
|941012|Regeln 941012|
|941100|XSS Attack har identifierats via libinjection|
|941110|XSS Filter - kategori 1 = skriptet taggen vektor|
|941130|XSS Filter - kategori 3 = attributet vektor|
|941140|XSS Filter - kategori 4 = Javascript-URI-vektor|
|941150|XSS Filter - kategori 5 = otillåtna HTML-attribut|
|941180|Nod-verifieraren svartlistat nyckelord|
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

|regel-ID|Beskrivning|
|---|---|
|942011|Regeln 942011|
|942012|Regeln 942012|
|942100|SQL-inmatning attacker har identifierats via libinjection|
|942140|SQL-angrepp = vanliga DB-namn som har identifierats|
|942160|Identifierar hemlig sqli tester med hjälp av sleep() eller benchmark().|
|942170|Identifierar SQL-inmatning för prestandamått och viloläge försöker, inklusive villkorlig|
|942230|Upptäcker om villkorlig SQL-inmatning|
|942270|Söker efter grundläggande sql-inmatning. Vanliga attack sträng för mysql oracle och andra.|
|942290|Söker efter grundläggande MongoDB SQL injection försök|
|942320|Identifierar MySQL och PostgreSQL lagrad procedur eller funktion inmatningar|
|942350|Identifierar MySQL UDF-inmatning och andra manipulering av datastruktur/försöker|
|942013|Regeln 942013|
|942014|Regeln 942014|
|942150|SQL-angrepp|
|942410|SQL-angrepp|
|942440|SQL-kommentar sekvens har identifierats.|
|942450|SQL hexadecimal kodning identifieras|
|942015|Regeln 942015|
|942016|Regeln 942016|
|942251|Identifierar HAVING-inmatningar|
|942460|Avisering för identifiering av meta tecknet Avvikelseidentifiering - repetitiva icke-alfanumeriska tecken|
|942017|Regeln 942017|
|942018|Regeln 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|regel-ID|Beskrivning|
|---|---|
|943011|Regeln 943011|
|943012|Regeln 943012|
|943100|En Session upptagningen Attack = ange värden för cookies i HTML|
|943110|En Session upptagningen Attack = SessionID parameternamn med av domän referent|
|943120|En Session upptagningen Attack = SessionID parameternamn med inga referent|
|943013|Regeln 943013|
|943014|Regeln 943014|
|943015|Regeln 943015|
|943016|Regeln 943016|
|943017|Regeln 943017|
|943018|Regeln 943018|

## <a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|regel-ID|Beskrivning|
|---|---|
|960911|Ogiltig HTTP-begäran-rad|
|981227|Apache fel = ogiltig URI i begäran.|
|960912|Det gick inte att parsa begärandetexten.|
|960914|Flera delar begäran brödtext misslyckades strikt verifiering = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} HF %@{MULTIPART_HEADER_FOLDING} LF % @ {MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING} IQ %@{MULTIPART_INVALID_QUOTING} IH %@{MULTIPART_INVALID_HEADER_FOLDING} FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|960915|Flera delar parser har upptäckt en oöverträffad gräns som möjligt.|
|960016|Content-Length-HTTP-huvud är inte numeriskt.|
|960011|GET eller HEAD-begäran med innehåll i Begärandetexten.|
|960012|POST-begäran saknas Content-Length-huvud.|
|960902|Ogiltig användning av identitet kodning.|
|960022|Förvänta dig rubrik tillåts inte för HTTP 1.0.|
|960020|Pragma rubriken kräver Cache-Control-huvudet för HTTP/1.1-begäranden.|
|958291|Intervall = fältet finns och börjar med 0.|
|958230|Intervall = ogiltig sista Byte-värde.|
|958295|Flera/motstridiga anslutning rubrik Data hittades.|
|950107|URL-kodning missbruk Angreppsförsök|
|950109|Flera URL-kodning har identifierats|
|950108|URL-kodning missbruk Angreppsförsök|
|950801|UTF8-Kodning missbruk Angreppsförsök|
|950116|Angreppsförsök för Unicode fullständig/halv bredd missbruk|
|960901|Ogiltigt tecken i begäran|
|960018|Ogiltigt tecken i begäran|

### <a name="crs21"></a> crs_21_protocol_anomalies

|regel-ID|Beskrivning|
|---|---|
|960008|Begäran saknar en värdrubrik|
|960007|Tom värdhuvud|
|960015|Begäran saknar en acceptera rubrik|
|960021|Begäran har en tom acceptera rubrik|
|960009|Begäran saknar en användaren Agent rubrik|
|960006|Tom användaren Agent-huvud|
|960904|Begäran som innehåller innehåll men saknas Content-Type-huvud|
|960017|Värdhuvud är en numerisk IP-adress|

### <a name="crs23"></a> crs_23_request_limits

|regel-ID|Beskrivning|
|---|---|
|960209|Argumentnamnet är för långt|
|960208|Argumentvärdet är för långt|
|960335|För många argument i begäran|
|960341|Totalt antal argument storlek har överskridits|
|960342|Överförda filstorleken är för stor|
|960343|Totalt antal överförda filer är för stort|

### <a name="crs30"></a> crs_30_http_policy

|regel-ID|Beskrivning|
|---|---|
|960032|Metoden tillåts inte av en princip|
|960010|Innehållstyp för begäran tillåts inte av princip|
|960034|HTTP-protokollversion tillåts inte av princip|
|960035|URL-filtillägg är begränsad av princip|
|960038|HTTP-huvud är begränsad av princip|

### <a name="crs35"></a> crs_35_bad_robots

|regel-ID|Beskrivning|
|---|---|
|990002|Begäran anger en Säkerhetsskanner genomsöks platsen|
|990901|Begäran anger en Säkerhetsskanner genomsöks platsen|
|990902|Begäran anger en Säkerhetsskanner genomsöks platsen|
|990012|Falsk webbplats crawler|

### <a name="crs40"></a> crs_40_generic_attacks

|regel-ID|Beskrivning|
|---|---|
|960024|Avisering för identifiering av meta tecknet Avvikelseidentifiering - repetitiva icke-alfanumeriska tecken|
|950008|Inmatning av odokumenterade ColdFusion taggar|
|950010|LDAP-angrepp|
|950011|SSI inmatning Attack|
|950018|Universal PDF XSS Webbadress hittades.|
|950019|E-post-angrepp|
|950012|HTTP-begäran som Kommandoinmatning Attack.|
|950910|HTTP-svar uppdelningen av Attack|
|950911|HTTP-svar uppdelningen av Attack|
|950117|Fjärrlagring inkludering Attack|
|950118|Fjärrlagring inkludering Attack|
|950119|Fjärrlagring inkludering Attack|
|950120|Möjliga fjärrfil inkludering (RFI) Attack = av domän/referenslänk|
|981133|Regeln 981133|
|981134|Regeln 981134|
|950009|Sessionen upptagningen Attack|
|950003|Sessionsfixering|
|950000|Sessionsfixering|
|950005|Fjärrlagring åtkomstförsök|
|950002|Kommandot systemåtkomst|
|950006|System kommandot inmatning|
|959151|PHP-angrepp|
|958976|PHP-angrepp|
|958977|PHP-angrepp|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|regel-ID|Beskrivning|
|---|---|
|981231|SQL-kommentar sekvens har identifierats.|
|981260|SQL hexadecimal kodning identifieras|
|981320|SQL-angrepp = vanliga DB-namn som har identifierats|
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
|981317|SQL SELECT-instruktion Avvikelseidentifiering varning|
|950007|Hemlig SQL-angrepp|
|950001|SQL-angrepp|
|950908|SQL-angrepp.|
|959073|SQL-angrepp|
|981272|Identifierar hemlig sqli tester med hjälp av sleep() eller benchmark().|
|981250|Identifierar SQL-inmatning för prestandamått och viloläge försöker, inklusive villkorlig|
|981241|Upptäcker om villkorlig SQL-inmatning|
|981276|Söker efter grundläggande sql-inmatning. Vanliga attack sträng för mysql oracle och andra.|
|981270|Söker efter grundläggande MongoDB SQL injection försök|
|981253|Identifierar MySQL och PostgreSQL lagrad procedur eller funktion inmatningar|
|981251|Identifierar MySQL UDF-inmatning och andra manipulering av datastruktur/försöker|

### <a name="crs41xss"></a> crs_41_xss_attacks

|regel-ID|Beskrivning|
|---|---|
|973336|XSS Filter - kategori 1 = skriptet taggen vektor|
|973338|XSS Filter - kategori 3 = Javascript-URI-vektor|
|981136|Regeln 981136|
|981018|Regeln 981018|
|958016|Cross site Scripting (XSS) Attack|
|958414|Cross site Scripting (XSS) Attack|
|958032|Cross site Scripting (XSS) Attack|
|958026|Cross site Scripting (XSS) Attack|
|958027|Cross site Scripting (XSS) Attack|
|958054|Cross site Scripting (XSS) Attack|
|958418|Cross site Scripting (XSS) Attack|
|958034|Cross site Scripting (XSS) Attack|
|958019|Cross site Scripting (XSS) Attack|
|958013|Cross site Scripting (XSS) Attack|
|958408|Cross site Scripting (XSS) Attack|
|958012|Cross site Scripting (XSS) Attack|
|958423|Cross site Scripting (XSS) Attack|
|958002|Cross site Scripting (XSS) Attack|
|958017|Cross site Scripting (XSS) Attack|
|958007|Cross site Scripting (XSS) Attack|
|958047|Cross site Scripting (XSS) Attack|
|958410|Cross site Scripting (XSS) Attack|
|958415|Cross site Scripting (XSS) Attack|
|958022|Cross site Scripting (XSS) Attack|
|958405|Cross site Scripting (XSS) Attack|
|958419|Cross site Scripting (XSS) Attack|
|958028|Cross site Scripting (XSS) Attack|
|958057|Cross site Scripting (XSS) Attack|
|958031|Cross site Scripting (XSS) Attack|
|958006|Cross site Scripting (XSS) Attack|
|958033|Cross site Scripting (XSS) Attack|
|958038|Cross site Scripting (XSS) Attack|
|958409|Cross site Scripting (XSS) Attack|
|958001|Cross site Scripting (XSS) Attack|
|958005|Cross site Scripting (XSS) Attack|
|958404|Cross site Scripting (XSS) Attack|
|958023|Cross site Scripting (XSS) Attack|
|958010|Cross site Scripting (XSS) Attack|
|958411|Cross site Scripting (XSS) Attack|
|958422|Cross site Scripting (XSS) Attack|
|958036|Cross site Scripting (XSS) Attack|
|958000|Cross site Scripting (XSS) Attack|
|958018|Cross site Scripting (XSS) Attack|
|958406|Cross site Scripting (XSS) Attack|
|958040|Cross site Scripting (XSS) Attack|
|958052|Cross site Scripting (XSS) Attack|
|958037|Cross site Scripting (XSS) Attack|
|958049|Cross site Scripting (XSS) Attack|
|958030|Cross site Scripting (XSS) Attack|
|958041|Cross site Scripting (XSS) Attack|
|958416|Cross site Scripting (XSS) Attack|
|958024|Cross site Scripting (XSS) Attack|
|958059|Cross site Scripting (XSS) Attack|
|958417|Cross site Scripting (XSS) Attack|
|958020|Cross site Scripting (XSS) Attack|
|958045|Cross site Scripting (XSS) Attack|
|958004|Cross site Scripting (XSS) Attack|
|958421|Cross site Scripting (XSS) Attack|
|958009|Cross site Scripting (XSS) Attack|
|958025|Cross site Scripting (XSS) Attack|
|958413|Cross site Scripting (XSS) Attack|
|958051|Cross site Scripting (XSS) Attack|
|958420|Cross site Scripting (XSS) Attack|
|958407|Cross site Scripting (XSS) Attack|
|958056|Cross site Scripting (XSS) Attack|
|958011|Cross site Scripting (XSS) Attack|
|958412|Cross site Scripting (XSS) Attack|
|958008|Cross site Scripting (XSS) Attack|
|958046|Cross site Scripting (XSS) Attack|
|958039|Cross site Scripting (XSS) Attack|
|958003|Cross site Scripting (XSS) Attack|
|973300|Möjliga XSS Attack påträffas - hanterare för HTML-tagg|
|973301|XSS-Attack upptäcktes|
|973302|XSS-Attack upptäcktes|
|973303|XSS-Attack upptäcktes|
|973304|XSS-Attack upptäcktes|
|973305|XSS-Attack upptäcktes|
|973306|XSS-Attack upptäcktes|
|973307|XSS-Attack upptäcktes|
|973308|XSS-Attack upptäcktes|
|973309|XSS-Attack upptäcktes|
|973311|XSS-Attack upptäcktes|
|973313|XSS-Attack upptäcktes|
|973314|XSS-Attack upptäcktes|
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

|regel-ID|Beskrivning|
|---|---|
|950103|Path Traversal Attack|

### <a name="crs45"></a> crs_45_trojans

|regel-ID|Beskrivning|
|---|---|
|950110|Bakdörrsåtkomst|
|950921|Bakdörrsåtkomst|
|950922|Bakdörrsåtkomst|

## <a name="next-steps"></a>Nästa steg

Lär dig att inaktivera WAF-regler genom att besöka: [anpassa WAF-regler](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png
