---
title: Arbeta med strängar i Azure Monitor-loggfrågor | Microsoft-dokument
description: Beskriver hur du redigerar, jämför, söker i och utför en mängd andra åtgärder på strängar i Azure Monitor-loggfrågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397474"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Arbeta med strängar i Azure Monitor-loggfrågor


> [!NOTE]
> Du bör slutföra [Komma igång med Azure Monitor Log Analytics](get-started-portal.md) och Komma igång med Azure [Monitor-loggfrågor](get-started-queries.md) innan du slutför den här självstudien.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs hur du redigerar, jämför, söker i och utför en mängd andra åtgärder på strängar.

Varje tecken i en sträng har ett indexnummer, beroende på dess plats. Det första tecknet är på index 0, nästa tecken är 1 och så vidare. Olika strängfunktioner använder indexnummer som visas i följande avsnitt. Många av följande exempel använder **kommandot print** för att demonstrera strängmanipulering utan att använda en viss datakälla.


## <a name="strings-and-escaping-them"></a>Strängar och fly dem
Strängvärdena är raderade med antingen med enkla eller dubbla citattecken. Omvänt snedstreck (\\) används för att undkomma tecken till tecknet efter det, \" till exempel \t för fliken \n för nyrad och citattecknet själv.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Om du\\vill förhindra " " från\@att fungera som ett escape-tecken, lägg till " " som ett prefix i strängen:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Strängjämnningar

Operator       |Beskrivning                         |Skiftlägeskänsliga|Exempel (avkastning) `true`
---------------|------------------------------------|--------------|-----------------------
`==`           |Är lika med                              |Ja           |`"aBc" == "aBc"`
`!=`           |Inte lika med                          |Ja           |`"abc" != "ABC"`
`=~`           |Är lika med                              |Inga            |`"abc" =~ "ABC"`
`!~`           |Inte lika med                          |Inga            |`"aBc" !~ "xyz"`
`has`          |Höger sida är en hel term i vänster sida |Inga|`"North America" has "america"`
`!has`         |Höger sida är inte en hel term i vänster sida       |Inga            |`"North America" !has "amer"` 
`has_cs`       |Höger sida är en hel term i vänster sida |Ja|`"North America" has_cs "America"`
`!has_cs`      |Höger sida är inte en hel term i vänster sida       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Höger sida är ett termprefix i vänster sida         |Inga            |`"North America" hasprefix "ame"`
`!hasprefix`   |Höger sida är inte ett termprefix i vänster sida     |Inga            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Höger sida är ett termprefix i vänster sida         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Höger sida är inte ett termprefix i vänster sida     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Höger sida är ett termsuffix i vänster sida         |Inga            |`"North America" hassuffix "ica"`
`!hassuffix`   |Höger sida är inte ett termsuffix i vänster sida     |Inga            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Höger sida är ett termsuffix i vänster sida         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Höger sida är inte ett termsuffix i vänster sida     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Höger sida uppstår som en undersekvens av vänster sida  |Inga            |`"FabriKam" contains "BRik"`
`!contains`    |Höger sida förekommer inte i vänster sida           |Inga            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Höger sida uppstår som en undersekvens av vänster sida  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Höger sida förekommer inte i vänster sida           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Höger sida är en första delsekvens av vänster sida|Inga            |`"Fabrikam" startswith "fab"`
`!startswith`  |Höger sida är inte en första delsekvens av vänster sida|Inga        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Höger sida är en första delsekvens av vänster sida|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Höger sida är inte en första delsekvens av vänster sida|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Höger sida är en avslutande delsekvens av vänster sida|Inga             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Höger sida är inte en avslutande delsekvens av vänster sida|Inga         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Höger sida är en avslutande delsekvens av vänster sida|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Höger sida är inte en avslutande delsekvens av vänster sida|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|vänster sida innehåller en match för höger sida        |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Är lika med ett av elementen       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Inte lika med något av elementen   |Ja           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>antal

Räknar förekomster av en delsträng i en sträng. Kan matcha vanliga strängar eller använda regex. Vanliga strängmatchningar kan överlappa varandra medan regex-matchningar inte gör det.

### <a name="syntax"></a>Syntax
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argument:
- `text`- Ingångssträngen 
- `search`- Oformaterad sträng eller reguljärt uttryck för att matcha inuti text.
- `kind` - _normal_ | _regex_ (standard: normal).

### <a name="returns"></a>Returnerar

Antalet gånger som söksträngen kan matchas i behållaren. Vanliga strängmatchningar kan överlappa varandra medan regex-matchningar inte gör det.

### <a name="examples"></a>Exempel

#### <a name="plain-string-matches"></a>Vanliga strängmatchningar

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex matchar

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Extrahera

Får en matchning för ett reguljärt uttryck från en viss sträng. Du kan också konvertera den extraherade delsträngen till den angivna typen.

### <a name="syntax"></a>Syntax

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argument

- `regex`- Ett reguljärt uttryck.
- `captureGroup`- En positiv heltalskonstant som anger att fångstgruppen ska extraheras. 0 för hela matchen, 1 för det värde som matchas av det första ('parentesen')' i det reguljära uttrycket, 2 eller mer för efterföljande parenteser.
- `text`- En sträng att söka.
- `typeLiteral`- En valfri typlitteral (till exempel typeof(long)). Om det anges konverteras den extraherade delsträngen till den här typen.

### <a name="returns"></a>Returnerar
Delsträngen matchas mot den angivna capture-gruppen captureGroup, eventuellt konverterad till typeLiteral.
Om det inte finns någon matchning, eller om typkonverteringen misslyckas, returnerar du null.

### <a name="examples"></a>Exempel

I följande exempel extraheras den sista oktetten *computerIP* från en pulsslagspost:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

I följande exempel extraheras den sista oktetten, den kastas till en *verklig* typ (tal) och beräknar nästa IP-värde
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

I exemplet nedan genomsöks *strängspårningen* efter en definition av "Varaktighet". Matchen är *gjuten* till verklig och multipliceras med en tidskonstant (1 s) *som kastar Varaktighet för att skriva tidsspann*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* returnerar sant om argumentet är en tom sträng eller null (se även *isnull*).
- *isnotempty* returnerar sant om argumentet inte är en tom sträng eller en null (se även *isnotnull*). alias: *notempty*.

### <a name="syntax"></a>Syntax

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Exempel

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl (tolk)

Delar en URL i dess delar (protokoll, värd, port osv.) och returnerar ett ordlisteobjekt som innehåller delarna som strängar.

### <a name="syntax"></a>Syntax

```
parseurl(urlstring)
```

### <a name="examples"></a>Exempel

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Resultatet kommer att bli:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>Ersätta

Ersätter alla regex-matchningar med en annan sträng. 

### <a name="syntax"></a>Syntax

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argument

- `regex`- Det reguljära uttrycket att matcha med. Den kan innehålla insamlingsgrupper i '('parentes')'.
- `rewrite`- Den ersättande regex för alla matcher som gjorts genom att matcha regex. Använd \0 för att referera till hela matchningen, \1 för den första insamlingsgruppen, \2, och så vidare för efterföljande insamlingsgrupper.
- `input_text`- Indatasträngen att söka i.

### <a name="returns"></a>Returnerar
Texten efter att ha ersatt alla matchningar av regex med utvärderingar av omskrivning. Matchningar överlappar inte varandra.

### <a name="examples"></a>Exempel

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Kan ha följande resultat:

Aktivitet                                        |Ersatt
------------------------------------------------|----------------------------------------------------------
4663 - Ett försök gjordes att komma åt ett objekt  |Aktivitets-ID 4663: Ett försök gjordes att komma åt ett objekt.


## <a name="split"></a>split

Delar en viss sträng enligt en angiven avgränsare och returnerar en matris med de resulterande delsträngarna.

### <a name="syntax"></a>Syntax
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argument:

- `source`- Strängen som ska delas enligt den angivna avgränsaren.
- `delimiter`- Avgränsaren som ska användas för att dela upp källsträngen.
- `requestedIndex`- Ett valfritt nollbaserat index. Om det anges kommer den returnerade strängmatrisen endast att innehålla det objektet (om det finns).


### <a name="examples"></a>Exempel

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat (strcat)

Sammanfoga strängargument (stöder 1-16 argument).

### <a name="syntax"></a>Syntax
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Exempel
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen (olika)

Returnerar längden på en sträng.

### <a name="syntax"></a>Syntax
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Exempel
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>Delsträng

Extraherar en delsträng från en viss källsträng, med början vid det angivna indexet. Du kan också ange längden på den begärda delsträngen.

### <a name="syntax"></a>Syntax
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argument:

- `source`- Källsträngen som delsträngen kommer att tas från.
- `startingIndex`- Den nollbaserade startteckenpositionen för den begärda delsträngen.
- `length`- En valfri parameter som kan användas för att ange den begärda längden på den returnerade delsträngen.

### <a name="examples"></a>Exempel
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Konverterar en viss sträng till alla gemener eller versaler.

### <a name="syntax"></a>Syntax
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Exempel
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Nästa steg
Fortsätt med de avancerade självstudierna:
* [Aggregeringsfunktioner](aggregations.md)
* [Avancerade aggregeringar](advanced-aggregations.md)
* [Grafer och diagram](charts.md)
* [Arbeta med JSON och datastrukturer](json-data-structures.md)
* [Avancerad frågeskrivning](advanced-query-writing.md)
* [Kopplingar - korsanalys](joins.md)
