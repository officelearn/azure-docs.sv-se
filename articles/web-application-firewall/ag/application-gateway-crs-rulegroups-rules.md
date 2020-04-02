---
title: CRS regelgrupper och regler
titleSuffix: Azure Web Application Firewall
description: Den här sidan innehåller information om crs-regelgrupper och regler för brandväggen för webbprogram.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 563aa701c1403a1ef26c6073496e7e59c7c5096c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521843"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Crs-regelgrupper och regler för brandvägg för webbprogram

Application Gateway web application firewall (WAF) skyddar webbprogram från vanliga sårbarheter och bedrifter. Detta görs genom regler som definieras baserat på OWASP-kärnregeluppsättningarna 3.1, 3.0 eller 2.2.9. Dessa regler kan inaktiveras regel för regel. Den här artikeln innehåller de aktuella regler och regeluppsättningar som erbjuds.

## <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway WAF levereras förkonfigurerad med CRS 3.0 som standard. Men du kan välja att använda CRS 3.1 eller CRS 2.2.9 istället. CRS 3.1 erbjuder nya regeluppsättningar som försvarar mot Java-infektioner, en första uppsättning filuppladdningskontroller, fasta falska positiva identifieringar och mycket mer. CRS 3.0 erbjuder minskade falska positiva resultat jämfört med CRS 2.2.9. Du kan också [anpassa regler efter dina behov.](application-gateway-customize-waf-rules-portal.md)

> [!div class="mx-imgBorder"]
> ![Hanterar regler](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF skyddar mot följande webbsårbarheter:

- SQL-injektionsattacker
- Skriptattacker över flera webbplatser
- Andra vanliga attacker, till exempel kommandoinjektion, HTTP-begäransmuggling, HTTP-svarsdelning och integrering av fjärrfiler
- ÖVERTRÄDELSER av HTTP-protokoll
- HTTP-protokollavvikelser, till exempel saknad värdanvändaragent och acceptera rubriker
- Robotar, crawlers och skannrar
- Vanliga felkonfigurationer för program (till exempel Apache och IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3,1

CRS 3.1 innehåller 13 regelgrupper, som visas i följande tabell. Varje grupp innehåller flera regler som kan inaktiveras.

|Regelgrupp|Beskrivning|
|---|---|
|**[Allmänt](#general-31)**|Allmän grupp|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Lock-down metoder (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Skydda mot port- och miljöskannrar|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Skydda mot protokoll- och kodningsproblem|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Skydda mot huvudinsprutning, begäran om smuggling och svarsdelning|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Skydda mot fil- och sökvägsattacker|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Skydda mot RFI-attacker (Remote File Inclusion)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Skydda igen fjärrkodkörningsattacker|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Skydda mot PHP-injektionsattacker|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Skydda mot skriptattacker över flera webbplatser|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Skydda mot SQL-injektionsattacker|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Skydda mot sessionsfixeringsattacker|
|**[REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-31)**|Skydda mot JAVA-attacker|

### <a name="owasp-crs-30"></a>OWASP CRS 3,0

CRS 3.0 innehåller 12 regelgrupper, som visas i följande tabell. Varje grupp innehåller flera regler som kan inaktiveras.

|Regelgrupp|Beskrivning|
|---|---|
|**[Allmänt](#general-30)**|Allmän grupp|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Lock-down metoder (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Skydda mot port- och miljöskannrar|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Skydda mot protokoll- och kodningsproblem|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Skydda mot huvudinsprutning, begäran om smuggling och svarsdelning|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Skydda mot fil- och sökvägsattacker|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Skydda mot RFI-attacker (Remote File Inclusion)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Skydda igen fjärrkodkörningsattacker|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Skydda mot PHP-injektionsattacker|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Skydda mot skriptattacker över flera webbplatser|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Skydda mot SQL-injektionsattacker|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Skydda mot sessionsfixeringsattacker|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 innehåller 10 regelgrupper, som visas i följande tabell. Varje grupp innehåller flera regler som kan inaktiveras.

|Regelgrupp|Beskrivning|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Skydda mot regelöverträdelser (till exempel ogiltiga tecken eller en GET med en begäranhet)|
|**[crs_21_protocol_anomalies](#crs21)**|Skydda mot felaktig rubrikinformation|
|**[crs_23_request_limits](#crs23)**|Skydda mot argument eller filer som överskrider begränsningar|
|**[crs_30_http_policy](#crs30)**|Skydda mot begränsade metoder, rubriker och filtyper|
|**[crs_35_bad_robots](#crs35)**|Skydda mot sökrobotar och skannrar|
|**[crs_40_generic_attacks](#crs40)**|Skydda mot generiska attacker (till exempel sessionsfixering, fjärrfilinkludering och PHP-injektion)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Skydda mot SQL-injektionsattacker|
|**[crs_41_xss_attacks](#crs41xss)**|Skydda mot skriptattacker över flera webbplatser|
|**[crs_42_tight_security](#crs42)**|Skydda mot path-traversal attacker|
|**[crs_45_trojans](#crs45)**|Skydda mot bakdörr trojaner|

Följande regelgrupper och regler är tillgängliga när du använder brandvägg för webbprogram på Application Gateway.

# <a name="owasp-31"></a>[OWASP 3.1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a>Regeluppsättningar

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Allmänt</p>

|RuleId (härska)|Beskrivning|
|---|---|
|200004|Möjlig multipart oöverträffad gräns.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId (härska)|Beskrivning|
|---|---|
|911100|Metoden tillåts inte av principen|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId (härska)|Beskrivning|
|---|---|
|913100|Hittade user-agent kopplad till säkerhetsskanner|
|913101|Hittade User-Agent associerad med skript/allmän HTTP-klient|
|913102|Hittade user-agent i samband med sökrobot/robot|
|913110|Hittade begäranden som är associerad med säkerhetsskanner|
|913120|Hittade filnamn/argument för begäran som är associerat med säkerhetsskanner|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId (härska)|Beskrivning|
|---|---|
|920100|Ogiltig HTTP-begäranderad|
|920120|Försök till förbikoppling mellan flera delar/formulärdata|
|920121|Försök till förbikoppling mellan flera delar/formulärdata|
|920130|Det gick inte att tolka begäranden.|
|920140|Body för begäran om flera delar misslyckades med strikt validering|
|920160|HTTP-huvudet för innehållslängd är inte numeriskt.|
|920170|GET eller HEAD Request med kroppsinnehåll.|
|920171|GET- eller HEAD-begäran med överföringskodning.|
|920180|POST-begäran saknas Innehållslängdsrubrik.|
|920190|Intervall = Ogiltigt senaste bytevärde.|
|920200|Intervall = För många fält (6 eller fler)|
|920201|Intervall = För många fält för pdf-begäran (35 eller fler)|
|920202|Intervall = För många fält för pdf-begäran (6 eller fler)|
|920210|Flera/motstridiga anslutningshuvuddata hittades.|
|920220|URL-kodning av attackförsök för otillåten användning|
|920230|Flera URL-kodning har identifierats|
|920240|URL-kodning av attackförsök för otillåten användning|
|920250|UTF8 Kodning missbruk attack försök|
|920260|Unicode Hel/ Halv bredd Missbruk Attack Försök|
|920270|Ogiltigt tecken i begäran (null-tecken)|
|920271|Ogiltigt tecken i begäran (tecken som inte kan skrivas ut)|
|920272|Ogiltigt tecken i begäran (utanför utskrivbara tecken nedan för ascii 127)|
|920273|Ogiltigt tecken i begäran (utanför mycket strikt uppsättning)|
|920274|Ogiltigt tecken i begäranden (utanför mycket strikt uppsättning)|
|920280|Begäran saknas ett värdhuvud|
|920290|Tomt värdhuvud|
|920300|Begäran saknas ett accepterande huvud|
|920310|Begäran har ett tomt accepterande huvud|
|920311|Begäran har ett tomt accepterande huvud|
|920320|Namn på användaragent saknas|
|920330|Tomt användaragenthuvud|
|920340|Begäran som innehåller innehåll men namn av innehållstyp saknas|
|920341|Begäran som innehåller innehåll kräver innehållstypsrubrik|
|920350|Värdhuvudet är en numerisk IP-adress|
|920360|Argumentnamnet är för långt|
|920370|Argumentvärdet är för långt|
|920380|För många argument i begäran|
|920390|Total argumentstorlek har överskridits|
|920400|Uppladdad filstorlek är för stor|
|920410|Den totala uppladdade filstorleken är för stor|
|920420|Begäran om innehållstyp tillåts inte av principen|
|920430|HTTP-protokollversion tillåts inte av principen|
|920440|Filtillägget url begränsas av principen|
|920450|HTTP-huvudet begränsas av principen (%@{MATCHED_VAR})|
|920460|Onormala escape-tecken|
|920470|Ogiltigt sidhuvud av innehållstyp|
|920480|Begränsa parametern för teckenuppsättning i innehållstypshuvudet|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId (härska)|Beskrivning|
|---|---|
|921110|HTTP-begäran smuggling attack|
|921120|HTTP-svarsdelningsattack|
|921130|HTTP-svarsdelningsattack|
|921140|HTTP Header Injection Attack via rubriker|
|921150|HTTP Header Injection Attack via nyttolast (CR / LF upptäckt)|
|921151|HTTP Header Injection Attack via nyttolast (CR / LF upptäckt)|
|921160|HTTP Header Injection Attack via nyttolast (CR/LF och rubriknamn upptäckt)|
|921170|HTTP-parameterföroreningar|
|921180|HTTP-parameterföroreningar (%{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId (härska)|Beskrivning|
|---|---|
|930100|Path Traversal Attack (/.. /)|
|930110|Path Traversal Attack (/.. /)|
|930120|Försök till filåtkomst för OS|
|930130|Försök till begränsad filåtkomst|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId (härska)|Beskrivning|
|---|---|
|931100|RFI-attack (Possible Remote File Inclusion) = URL-parameter med IP-adress|
|931110|RFI-attack (Possible Remote File Inclusion) = Common RFI Vulnerable Parameter Name used w/URL Payload|
|931120|Möjlig RFI-attack (Remote File Inclusion) = URL Nyttolast som används med efterföljande frågeteckentecken (?)|
|931130|Möjlig RFI-attack (Remote File Inclusion) = Referens/länk utanför domänen|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId (härska)|Beskrivning|
|---|---|
|932100|Fjärrkommando körning: Unix kommandoinjektion|
|932105|Fjärrkommando körning: Unix kommandoinjektion|
|932106|Fjärrkommando körning: Unix kommandoinjektion|
|932110|Fjärrkommandokörning: Windows-kommandoinjektion|
|932115|Fjärrkommandokörning: Windows-kommandoinjektion|
|932120|Fjärrkommandokörning = Kommandot Windows PowerShell hittades|
|932130|Fjärrkommandokörning = Unix-skaluttryck hittades|
|932140|Fjärrkommandokörning = kommandot Windows FOR/IF hittades|
|932160|Fjärrkommandokörning = Unix-skalkod hittades|
|932170|Fjärrkommandoutförande = Shellshock (CVE-2014-6271)|
|932171|Fjärrkommandoutförande = Shellshock (CVE-2014-6271)|
|932180|Försök med begränsad filöverföring|
|932190|Fjärrkommando körning: Jokertecken bypass teknik försök|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId (härska)|Beskrivning|
|---|---|
|933100|PHP Injection Attack = Öppning / Closing Tag Found|
|933110|PHP Injection Attack = PHP Script File Ladda upp Hittade|
|933111|PHP Injection Attack: PHP Script File Ladda upp hittade|
|933120|PHP Injection Attack = Konfigurationsdirektiv Hittades|
|933130|PHP Injection Attack = variabler hittades|
|933131|PHP Injection Attack: Variabler hittades|
|933140|PHP Injection Attack: I / O Stream Hittades|
|933150|PHP Injection Attack = Hög risk PHP funktionsnamn hittades|
|933151|PHP Injection Attack: Medium-Risk PHP funktionsnamn hittades|
|933160|PHP Injection Attack = Hög risk PHP funktion samtal hittades|
|933161|PHP Injection Attack: Lågt värde PHP-funktion Samtal hittades|
|933170|PHP Injection Attack: Serialized Objekt Injection|
|933180|PHP Injection Attack = Variabel funktion Samtal Hittades|
|933190|PHP Injection Attack: PHP Closing Tag Found|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId (härska)|Beskrivning|
|---|---|
|941100|XSS Attack upptäckts via libinjection|
|941101|XSS Attack upptäckts via libinjection|
|941110|XSS-filter – kategori 1 = skripttaggvektor|
|941130|XSS-filter - Kategori 3 = Attributvektor|
|941140|XSS-filter - Kategori 4 = Javascript URI Vector|
|941150|XSS-filter - Kategori 5 = Otillåtna HTML-attribut|
|941160|NoScript XSS InjectionChecker: HTML-injektion|
|941170|NoScript XSS InjectionChecker: Attributinjektion|
|941180|Nod-Validerare Svartlista Nyckelord|
|941190|XSS med formatmallar|
|941200|XSS med VML-ramar|
|941210|XSS med fördunklad Javascript|
|941220|XSS med fördunklad VB-skript|
|941230|XSS med hjälp av "bädda in"-taggen|
|941240|XSS med attributet "import" eller "implementation"|
|941250|IE XSS-filter - Attack upptäckt|
|941260|XSS med hjälp av "meta"-tagg|
|941270|XSS med "länk" href|
|941280|XSS med hjälp av "bas"-tagg|
|941290|XSS med hjälp av "applet"-tagg|
|941300|XSS med hjälp av "objekt"-tagg|
|941310|US-ASCII Missbildat kodning XSS-filter - Attack upptäckt.|
|941320|Möjlig XSS-attack upptäckt - HTML-tagghanterare|
|941330|IE XSS filter - attack upptäckt.|
|941340|IE XSS filter - attack upptäckt.|
|941350|UTF-7 Kodning IE XSS - Attack Upptäckt.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId (härska)|Beskrivning|
|---|---|
|942100|SQL Injection Attack upptäckts via libinjection|
|942110|SQL Injection Attack: Vanliga injektionstester upptäckta|
|942120|SQL Injection Attack: SQL-operatör upptäckt|
|942130|SQL Injection Attack: SQL Tautology upptäckts.|
|942140|SQL Injection Attack = Vanliga DB-namn upptäckta|
|942150|SQL-injektionsattack|
|942160|Identifierar blinda sqli-tester med sleep() eller benchmark().|
|942170|Identifierar SQL-benchmark- och sömninjektionsförsök inklusive villkorliga frågor|
|942180|Identifierar grundläggande SQL-autentiseringsavstrollningsförsök 1/3|
|942190|Identifierar MSSQL-kodkörning och informationsinsamlingsförsök|
|942200|Upptäcker MySQL kommentar-/rymddunklad injektioner och backtick uppsägning|
|942210|Upptäcker kedjade SQL-injektionsförsök 1/2|
|942220|Letar du efter heltal spill attacker, dessa tas från skipfish, utom 3.0.00738585072|
|942230|Identifierar villkorsstyrda SQL-injektionsförsök|
|942240|Identifierar MySQL-charset-switch och MSSQL DoS-försök|
|942250|Upptäcker MATCH MOT, SAMMANFOGA OCH UTFÖR OMEDELBARA injektioner|
|942251|Upptäcker att ha injektioner|
|942260|Identifierar grundläggande SQL-autentiseringsavstrollningsförsök 2/3|
|942270|Letar du efter grundläggande sql injektion. Vanliga attacksträng för mysql orakel och andra|
|942280|Upptäcker Postgres pg_sleep injektion, väntar på fördröjningsattacker och databasavstängningsförsök|
|942290|Hittar grundläggande MongoDB SQL-injektionsförsök|
|942300|Upptäcker MySQL kommentarer, villkor och ch(a) r injektioner|
|942310|Upptäcker kedjade SQL-injektionsförsök 2/2|
|942320|Känner av MySQL och PostgreSQL lagrade ingrepp/funktionsinjektioner|
|942330|Upptäcker klassiska SQL-injektionsde sonder 1/2|
|942340|Identifierar grundläggande SQL-autentiseringsavstrollningsförsök 3/3|
|942350|Upptäcker MySQL UDF-injektion och andra data-/strukturmanipulationsförsök|
|942360|Identifierar sammanfogade grundläggande SQL-injektion och SQLLFI-försök|
|942361|Identifierar grundläggande SQL-injektion baserat på nyckelordsändring eller union|
|942370|Upptäcker klassiska SQL-injektionsde sonder 2/2|
|942380|SQL-injektionsattack|
|942390|SQL-injektionsattack|
|942400|SQL-injektionsattack|
|942410|SQL-injektionsattack|
|942420|Begränsad SQL-tecken avvikelseidentifiering (cookies): # av specialtecken överskrids (8)|
|942421|Begränsad SQL-tecken avvikelseidentifiering (cookies): # av specialtecken överskrids (3)|
|942430|Begränsad avvikelseidentifiering av SQL-tecken (args): # av specialtecken som överskridits (12)|
|942431|Begränsad avvikelseidentifiering av SQL-tecken (args): # av specialtecken som överskridits (6)|
|942432|Begränsad avvikelseidentifiering av SQL-tecken (args): # av specialtecken som överskridits (2)|
|942440|SQL-kommentarsekvens har identifierats.|
|942450|SQL Hex-kodning identifierad|
|942460|Identifieringsvarning för metateckensan anomali – upprepade tecken som inte är ord|
|942470|SQL-injektionsattack|
|942480|SQL-injektionsattack|
|942490|Upptäcker klassiska SQL-injektionsde sonder 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId (härska)|Beskrivning|
|---|---|
|943100|Möjlig sessionsfixeringsattack = Ange cookie-värden i HTML|
|943110|Möjlig sessionskorrigeringsattack = SessionID-parameternamn med referent utanför domänen|
|943120|Möjlig sessionsfixeringsattack = SessionID-parameternamn utan referrer|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">REQUEST-944-APPLICATION-ATTACK-SESSION-JAVA</p>

|RuleId (härska)|Beskrivning|
|---|---|
|944120|Möjlig körning av nyttolast och fjärrkommandokörning|
|944130|Misstänkta Java-klasser|
|944200|Utnyttjande av Java avserialisering Apache Commons|

# <a name="owasp-30"></a>[OWASP 3,0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a>Regeluppsättningar

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Allmänt</p>

|RuleId (härska)|Beskrivning|
|---|---|
|200004|Möjlig multipart oöverträffad gräns.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId (härska)|Beskrivning|
|---|---|
|911100|Metoden tillåts inte av principen|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId (härska)|Beskrivning|
|---|---|
|913100|Hittade user-agent kopplad till säkerhetsskanner|
|913110|Hittade begäranden som är associerad med säkerhetsskanner|
|913120|Hittade filnamn/argument för begäran som är associerat med säkerhetsskanner|
|913101|Hittade User-Agent associerad med skript/allmän HTTP-klient|
|913102|Hittade user-agent i samband med sökrobot/robot|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId (härska)|Beskrivning|
|---|---|
|920100|Ogiltig HTTP-begäranderad|
|920130|Det gick inte att tolka begäranden.|
|920140|Body för begäran om flera delar misslyckades med strikt validering|
|920160|HTTP-huvudet för innehållslängd är inte numeriskt.|
|920170|GET eller HEAD Request med kroppsinnehåll.|
|920180|POST-begäran saknas Innehållslängdsrubrik.|
|920190|Intervall = Ogiltigt senaste bytevärde.|
|920210|Flera/motstridiga anslutningshuvuddata hittades.|
|920220|URL-kodning av attackförsök för otillåten användning|
|920240|URL-kodning av attackförsök för otillåten användning|
|920250|UTF8 Kodning missbruk attack försök|
|920260|Unicode Hel/ Halv bredd Missbruk Attack Försök|
|920270|Ogiltigt tecken i begäran (null-tecken)|
|920280|Begäran saknas ett värdhuvud|
|920290|Tomt värdhuvud|
|920310|Begäran har ett tomt accepterande huvud|
|920311|Begäran har ett tomt accepterande huvud|
|920330|Tomt användaragenthuvud|
|920340|Begäran som innehåller innehåll men namn av innehållstyp saknas|
|920350|Värdhuvudet är en numerisk IP-adress|
|920380|För många argument i begäran|
|920360|Argumentnamnet är för långt|
|920370|Argumentvärdet är för långt|
|920390|Total argumentstorlek har överskridits|
|920400|Uppladdad filstorlek är för stor|
|920410|Den totala uppladdade filstorleken är för stor|
|920420|Begäran om innehållstyp tillåts inte av principen|
|920430|HTTP-protokollversion tillåts inte av principen|
|920440|Filtillägget url begränsas av principen|
|920450|HTTP-huvudet begränsas av principen (%@{MATCHED_VAR})|
|920200|Intervall = För många fält (6 eller fler)|
|920201|Intervall = För många fält för pdf-begäran (35 eller fler)|
|920230|Flera URL-kodning har identifierats|
|920300|Begäran saknas ett accepterande huvud|
|920271|Ogiltigt tecken i begäran (tecken som inte kan skrivas ut)|
|920320|Namn på användaragent saknas|
|920272|Ogiltigt tecken i begäran (utanför utskrivbara tecken nedan för ascii 127)|
|920202|Intervall = För många fält för pdf-begäran (6 eller fler)|
|920273|Ogiltigt tecken i begäran (utanför mycket strikt uppsättning)|
|920274|Ogiltigt tecken i begäranden (utanför mycket strikt uppsättning)|
|920460|Onormala escape-tecken|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId (härska)|Beskrivning|
|---|---|
|921100|HTTP-begäran smuggling attack.|
|921110|HTTP-begäran smuggling attack|
|921120|HTTP-svarsdelningsattack|
|921130|HTTP-svarsdelningsattack|
|921140|HTTP Header Injection Attack via rubriker|
|921150|HTTP Header Injection Attack via nyttolast (CR / LF upptäckt)|
|921160|HTTP Header Injection Attack via nyttolast (CR/LF och rubriknamn upptäckt)|
|921151|HTTP Header Injection Attack via nyttolast (CR / LF upptäckt)|
|921170|HTTP-parameterföroreningar|
|921180|HTTP-parameterföroreningar (%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId (härska)|Beskrivning|
|---|---|
|930100|Path Traversal Attack (/.. /)|
|930110|Path Traversal Attack (/.. /)|
|930120|Försök till filåtkomst för OS|
|930130|Försök till begränsad filåtkomst|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId (härska)|Beskrivning|
|---|---|
|931100|RFI-attack (Possible Remote File Inclusion) = URL-parameter med IP-adress|
|931110|RFI-attack (Possible Remote File Inclusion) = Common RFI Vulnerable Parameter Name used w/URL Payload|
|931120|Möjlig RFI-attack (Remote File Inclusion) = URL Nyttolast som används med efterföljande frågeteckentecken (?)|
|931130|Möjlig RFI-attack (Remote File Inclusion) = Referens/länk utanför domänen|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId (härska)|Beskrivning|
|---|---|
|932120|Fjärrkommandokörning = Kommandot Windows PowerShell hittades|
|932130|Fjärrkommandokörning = Unix-skaluttryck hittades|
|932140|Fjärrkommandokörning = kommandot Windows FOR/IF hittades|
|932160|Fjärrkommandokörning = Unix-skalkod hittades|
|932170|Fjärrkommandoutförande = Shellshock (CVE-2014-6271)|
|932171|Fjärrkommandoutförande = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId (härska)|Beskrivning|
|---|---|
|933100|PHP Injection Attack = Öppning / Closing Tag Found|
|933110|PHP Injection Attack = PHP Script File Ladda upp Hittade|
|933120|PHP Injection Attack = Konfigurationsdirektiv Hittades|
|933130|PHP Injection Attack = variabler hittades|
|933150|PHP Injection Attack = Hög risk PHP funktionsnamn hittades|
|933160|PHP Injection Attack = Hög risk PHP funktion samtal hittades|
|933180|PHP Injection Attack = Variabel funktion Samtal Hittades|
|933151|PHP Injection Attack = Medium-Risk PHP funktionsnamn hittades|
|933131|PHP Injection Attack = variabler hittades|
|933161|PHP Injection Attack = lågt värde PHP funktion samtal hittades|
|933111|PHP Injection Attack = PHP Script File Ladda upp Hittade|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId (härska)|Beskrivning|
|---|---|
|941100|XSS Attack upptäckts via libinjection|
|941110|XSS-filter – kategori 1 = skripttaggvektor|
|941130|XSS-filter - Kategori 3 = Attributvektor|
|941140|XSS-filter - Kategori 4 = Javascript URI Vector|
|941150|XSS-filter - Kategori 5 = Otillåtna HTML-attribut|
|941180|Nod-Validerare Svartlista Nyckelord|
|941190|XSS med formatmallar|
|941200|XSS med VML-ramar|
|941210|XSS med fördunklad Javascript|
|941220|XSS med fördunklad VB-skript|
|941230|XSS med hjälp av "bädda in"-taggen|
|941240|XSS med attributet "import" eller "implementation"|
|941260|XSS med hjälp av "meta"-tagg|
|941270|XSS med "länk" href|
|941280|XSS med hjälp av "bas"-tagg|
|941290|XSS med hjälp av "applet"-tagg|
|941300|XSS med hjälp av "objekt"-tagg|
|941310|US-ASCII Missbildat kodning XSS-filter - Attack upptäckt.|
|941330|IE XSS filter - attack upptäckt.|
|941340|IE XSS filter - attack upptäckt.|
|941350|UTF-7 Kodning IE XSS - Attack Upptäckt.|
|941320|Möjlig XSS-attack upptäckt - HTML-tagghanterare|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId (härska)|Beskrivning|
|---|---|
|942100|SQL Injection Attack upptäckts via libinjection|
|942110|SQL Injection Attack: Vanliga injektionstester upptäckta|
|942130|SQL Injection Attack: SQL Tautology upptäckts.|
|942140|SQL Injection Attack = Vanliga DB-namn upptäckta|
|942160|Identifierar blinda sqli-tester med sleep() eller benchmark().|
|942170|Identifierar SQL-benchmark- och sömninjektionsförsök inklusive villkorliga frågor|
|942190|Identifierar MSSQL-kodkörning och informationsinsamlingsförsök|
|942200|Upptäcker MySQL kommentar-/rymddunklad injektioner och backtick uppsägning|
|942230|Identifierar villkorsstyrda SQL-injektionsförsök|
|942260|Identifierar grundläggande SQL-autentiseringsavstrollningsförsök 2/3|
|942270|Letar du efter grundläggande sql injektion. Vanliga attacksträngar för mysql orakel och andra.|
|942290|Hittar grundläggande MongoDB SQL-injektionsförsök|
|942300|Upptäcker MySQL kommentarer, villkor och ch(a) r injektioner|
|942310|Upptäcker kedjade SQL-injektionsförsök 2/2|
|942320|Känner av MySQL och PostgreSQL lagrade ingrepp/funktionsinjektioner|
|942330|Upptäcker klassiska SQL-injektionsde sonder 1/2|
|942340|Identifierar grundläggande SQL-autentiseringsavstrollningsförsök 3/3|
|942350|Upptäcker MySQL UDF-injektion och andra data-/strukturmanipulationsförsök|
|942360|Identifierar sammanfogade grundläggande SQL-injektion och SQLLFI-försök|
|942370|Upptäcker klassiska SQL-injektionsde sonder 2/2|
|942150|SQL-injektionsattack|
|942410|SQL-injektionsattack|
|942430|Begränsad avvikelseidentifiering av SQL-tecken (args): # av specialtecken som överskridits (12)|
|942440|SQL-kommentarsekvens har identifierats.|
|942450|SQL Hex-kodning identifierad|
|942251|Upptäcker att ha injektioner|
|942460|Identifieringsvarning för metateckensan anomali – upprepade tecken som inte är ord|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId (härska)|Beskrivning|
|---|---|
|943100|Möjlig sessionsfixeringsattack = Ange cookie-värden i HTML|
|943110|Möjlig sessionskorrigeringsattack = SessionID-parameternamn med referent utanför domänen|
|943120|Möjlig sessionsfixeringsattack = SessionID-parameternamn utan referrer|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a>Regeluppsättningar

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a>crs_20_protocol_violations

|RuleId (härska)|Beskrivning|
|---|---|
|960911|Ogiltig HTTP-begäranderad|
|981227|Apache-fel = Ogiltig URI i begäran.|
|960912|Det gick inte att tolka begäranden.|
|960914|Body för begäran om flera delar misslyckades med strikt validering|
|960915|Flerdelstolkaren upptäckte en möjlig oöverträffad gräns.|
|960016|HTTP-huvudet för innehållslängd är inte numeriskt.|
|960011|GET eller HEAD Request med kroppsinnehåll.|
|960012|POST-begäran saknas Innehållslängdsrubrik.|
|960902|Ogiltig användning av identitetskodning.|
|960022|Förvänta header inte tillåtet för HTTP 1.0.|
|960020|Pragma Header kräver cache-kontrollhuvud för HTTP/1.1-begäranden.|
|958291|Intervall = fältet finns och börjar med 0.|
|958230|Intervall = Ogiltigt senaste bytevärde.|
|958295|Flera/motstridiga anslutningshuvuddata hittades.|
|950107|URL-kodning av attackförsök för otillåten användning|
|950109|Flera URL-kodning har identifierats|
|950108|URL-kodning av attackförsök för otillåten användning|
|950801|UTF8 Kodning missbruk attack försök|
|950116|Unicode Hel/ Halv bredd Missbruk Attack Försök|
|960901|Ogiltigt tecken i begäran|
|960018|Ogiltigt tecken i begäran|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a>crs_21_protocol_anomalies

|RuleId (härska)|Beskrivning|
|---|---|
|960008|Begäran saknas ett värdhuvud|
|960007|Tomt värdhuvud|
|960015|Begäran saknas ett accepterande huvud|
|960021|Begäran har ett tomt accepterande huvud|
|960009|Begär saknade ett användaragenthuvud|
|960006|Tomt användaragenthuvud|
|960904|Begäran som innehåller innehåll men namn av innehållstyp saknas|
|960017|Värdhuvudet är en numerisk IP-adress|

### <a name="crs_23_request_limits"></a><a name="crs23"></a>crs_23_request_limits

|RuleId (härska)|Beskrivning|
|---|---|
|960209|Argumentnamnet är för långt|
|960208|Argumentvärdet är för långt|
|960335|För många argument i begäran|
|960341|Total argumentstorlek har överskridits|
|960342|Uppladdad filstorlek är för stor|
|960343|Den totala uppladdade filstorleken är för stor|

### <a name="crs_30_http_policy"></a><a name="crs30"></a>crs_30_http_policy

|RuleId (härska)|Beskrivning|
|---|---|
|960032|Metoden tillåts inte av principen|
|960010|Begäran om innehållstyp tillåts inte av principen|
|960034|HTTP-protokollversion tillåts inte av principen|
|960035|Filtillägget url begränsas av principen|
|960038|HTTP-huvudet begränsas av principen|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a>crs_35_bad_robots

|RuleId (härska)|Beskrivning|
|---|---|
|990002|Begäran anger en säkerhetsskanner som skannats av platsen|
|990901|Begäran anger en säkerhetsskanner som skannats av platsen|
|990902|Begäran anger en säkerhetsskanner som skannats av platsen|
|990012|Rogue webbplats sökrobot|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a>crs_40_generic_attacks

|RuleId (härska)|Beskrivning|
|---|---|
|960024|Identifieringsvarning för metateckensan anomali – upprepade tecken som inte är ord|
|950008|Injektion av papperslösa ColdFusion Taggar|
|950010|LDAP injektion attack|
|950011|SSI injektion Attack|
|950018|Universell PDF XSS-URL har identifierats.|
|950019|E-post injektion Attack|
|950012|HTTP-begäran smuggling attack.|
|950910|HTTP-svarsdelningsattack|
|950911|HTTP-svarsdelningsattack|
|950117|Attack av fjärrfilsinkluder|
|950118|Attack av fjärrfilsinkluder|
|950119|Attack av fjärrfilsinkluder|
|950120|Möjlig RFI-attack (Remote File Inclusion) = Referens/länk utanför domänen|
|981133|Regel 981133|
|981134|Regel 981134|
|950009|Attack mot sessionsfixering|
|950003|Korrigering av sessions|
|950000|Korrigering av sessions|
|950005|Försök till fjärråtkomst|
|950002|Åtkomst till systemkommando|
|950006|Injektion av systemkommando|
|959151|PHP Injektion Attack|
|958976|PHP Injektion Attack|
|958977|PHP Injektion Attack|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a>crs_41_sql_injection_attacks

|RuleId (härska)|Beskrivning|
|---|---|
|981231|SQL-kommentarsekvens har identifierats.|
|981260|SQL Hex-kodning identifierad|
|981320|SQL Injection Attack = Vanliga DB-namn upptäckta|
|981300|Regel 981300|
|981301|Regel 981301|
|981302|Regel 981302|
|981303|Regel 981303|
|981304|Regel 981304|
|981305|Regel 981305|
|981306|Regel 981306|
|981307|Regel 981307|
|981308|Regel 981308|
|981309|Regel 981309|
|981310|Regel 981310|
|981311|Regel 981311|
|981312|Regel 981312|
|981313|Regel 981313|
|981314|Regel 981314|
|981315|Regel 981315|
|981316|Regel 981316|
|981317|Identifiering av SQL SELECT-uttrycksavvikelse|
|950007|Blind SQL Injektion Attack|
|950001|SQL-injektionsattack|
|950908|SQL Injection Attack.|
|959073|SQL-injektionsattack|
|981272|Identifierar blinda sqli-tester med sleep() eller benchmark().|
|981250|Identifierar SQL-benchmark- och sömninjektionsförsök inklusive villkorliga frågor|
|981241|Identifierar villkorsstyrda SQL-injektionsförsök|
|981276|Letar du efter grundläggande sql injektion. Vanliga attacksträngar för mysql orakel och andra.|
|981270|Hittar grundläggande MongoDB SQL-injektionsförsök|
|981253|Känner av MySQL och PostgreSQL lagrade ingrepp/funktionsinjektioner|
|981251|Upptäcker MySQL UDF-injektion och andra data-/strukturmanipulationsförsök|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a>crs_41_xss_attacks

|RuleId (härska)|Beskrivning|
|---|---|
|973336|XSS-filter – kategori 1 = skripttaggvektor|
|973338|XSS-filter - Kategori 3 = Javascript URI Vector|
|981136|Regel 981136|
|981018|Regel 981018|
|958016|XSS-attack (Cross-site Scripting)|
|958414|XSS-attack (Cross-site Scripting)|
|958032|XSS-attack (Cross-site Scripting)|
|958026|XSS-attack (Cross-site Scripting)|
|958027|XSS-attack (Cross-site Scripting)|
|958054|XSS-attack (Cross-site Scripting)|
|958418|XSS-attack (Cross-site Scripting)|
|958034|XSS-attack (Cross-site Scripting)|
|958019|XSS-attack (Cross-site Scripting)|
|958013|XSS-attack (Cross-site Scripting)|
|958408|XSS-attack (Cross-site Scripting)|
|958012|XSS-attack (Cross-site Scripting)|
|958423|XSS-attack (Cross-site Scripting)|
|958002|XSS-attack (Cross-site Scripting)|
|958017|XSS-attack (Cross-site Scripting)|
|958007|XSS-attack (Cross-site Scripting)|
|958047|XSS-attack (Cross-site Scripting)|
|958410|XSS-attack (Cross-site Scripting)|
|958415|XSS-attack (Cross-site Scripting)|
|958022|XSS-attack (Cross-site Scripting)|
|958405|XSS-attack (Cross-site Scripting)|
|958419|XSS-attack (Cross-site Scripting)|
|958028|XSS-attack (Cross-site Scripting)|
|958057|XSS-attack (Cross-site Scripting)|
|958031|XSS-attack (Cross-site Scripting)|
|958006|XSS-attack (Cross-site Scripting)|
|958033|XSS-attack (Cross-site Scripting)|
|958038|XSS-attack (Cross-site Scripting)|
|958409|XSS-attack (Cross-site Scripting)|
|958001|XSS-attack (Cross-site Scripting)|
|958005|XSS-attack (Cross-site Scripting)|
|958404|XSS-attack (Cross-site Scripting)|
|958023|XSS-attack (Cross-site Scripting)|
|958010|XSS-attack (Cross-site Scripting)|
|958411|XSS-attack (Cross-site Scripting)|
|958422|XSS-attack (Cross-site Scripting)|
|958036|XSS-attack (Cross-site Scripting)|
|958000|XSS-attack (Cross-site Scripting)|
|958018|XSS-attack (Cross-site Scripting)|
|958406|XSS-attack (Cross-site Scripting)|
|958040|XSS-attack (Cross-site Scripting)|
|958052|XSS-attack (Cross-site Scripting)|
|958037|XSS-attack (Cross-site Scripting)|
|958049|XSS-attack (Cross-site Scripting)|
|958030|XSS-attack (Cross-site Scripting)|
|958041|XSS-attack (Cross-site Scripting)|
|958416|XSS-attack (Cross-site Scripting)|
|958024|XSS-attack (Cross-site Scripting)|
|958059|XSS-attack (Cross-site Scripting)|
|958417|XSS-attack (Cross-site Scripting)|
|958020|XSS-attack (Cross-site Scripting)|
|958045|XSS-attack (Cross-site Scripting)|
|958004|XSS-attack (Cross-site Scripting)|
|958421|XSS-attack (Cross-site Scripting)|
|958009|XSS-attack (Cross-site Scripting)|
|958025|XSS-attack (Cross-site Scripting)|
|958413|XSS-attack (Cross-site Scripting)|
|958051|XSS-attack (Cross-site Scripting)|
|958420|XSS-attack (Cross-site Scripting)|
|958407|XSS-attack (Cross-site Scripting)|
|958056|XSS-attack (Cross-site Scripting)|
|958011|XSS-attack (Cross-site Scripting)|
|958412|XSS-attack (Cross-site Scripting)|
|958008|XSS-attack (Cross-site Scripting)|
|958046|XSS-attack (Cross-site Scripting)|
|958039|XSS-attack (Cross-site Scripting)|
|958003|XSS-attack (Cross-site Scripting)|
|973300|Möjlig XSS-attack upptäckt - HTML-tagghanterare|
|973301|XSS-attack har upptäckts|
|973302|XSS-attack har upptäckts|
|973303|XSS-attack har upptäckts|
|973304|XSS-attack har upptäckts|
|973305|XSS-attack har upptäckts|
|973306|XSS-attack har upptäckts|
|973307|XSS-attack har upptäckts|
|973308|XSS-attack har upptäckts|
|973309|XSS-attack har upptäckts|
|973311|XSS-attack har upptäckts|
|973313|XSS-attack har upptäckts|
|973314|XSS-attack har upptäckts|
|973331|IE XSS filter - attack upptäckt.|
|973315|IE XSS filter - attack upptäckt.|
|973330|IE XSS filter - attack upptäckt.|
|973327|IE XSS filter - attack upptäckt.|
|973326|IE XSS filter - attack upptäckt.|
|973346|IE XSS filter - attack upptäckt.|
|973345|IE XSS filter - attack upptäckt.|
|973324|IE XSS filter - attack upptäckt.|
|973323|IE XSS filter - attack upptäckt.|
|973348|IE XSS filter - attack upptäckt.|
|973321|IE XSS filter - attack upptäckt.|
|973320|IE XSS filter - attack upptäckt.|
|973318|IE XSS filter - attack upptäckt.|
|973317|IE XSS filter - attack upptäckt.|
|973329|IE XSS filter - attack upptäckt.|
|973328|IE XSS filter - attack upptäckt.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a>crs_42_tight_security

|RuleId (härska)|Beskrivning|
|---|---|
|950103|Path Traversal Attack|

### <a name="crs_45_trojans"></a><a name="crs45"></a>crs_45_trojans

|RuleId (härska)|Beskrivning|
|---|---|
|950110|Tillgång till bakdörr|
|950921|Tillgång till bakdörr|
|950922|Tillgång till bakdörr|

---

## <a name="next-steps"></a>Nästa steg

- [Anpassa brandväggsregler för webbprogram med Azure-portalen](application-gateway-customize-waf-rules-portal.md)
