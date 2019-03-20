---
title: Arbeta med strängar i Azure Monitor loggfrågor | Microsoft Docs
description: Beskriver hur du redigerar, jämföra, söka i och utföra en mängd andra åtgärder i strängar i Azure Monitor log-frågor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 4b2763629a3036551cb3d362e609c72737436f4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012230"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Arbeta med strängar i Azure Monitor log-frågor


> [!NOTE]
> Bör du genomföra [Kom igång med Azure Monitor Log Analytics](get-started-portal.md) och [komma igång med Azure Monitor loggfrågor](get-started-queries.md) innan den här kursen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Den här artikeln beskriver hur du redigerar, jämföra, söka i och utföra en mängd andra åtgärder i strängar.

Varje tecken i en sträng har ett indextal enligt dess plats. Det första tecknet är vid index 0, nästa tecken är 1 och så att ett. Olika strängfunktioner använda indextal som visas i följande avsnitt. Många av i följande exempel används den **skriva ut** kommandot för att demonstrera manipulering av sträng utan att använda en särskild datakälla.


## <a name="strings-and-escaping-them"></a>Strängar och undantagstecken dem.
Strängvärden omslutna av antingen med enkla eller dubbla citattecken. Omvänt snedstreck (\) används för att escape-tecken tecknet efter den, till exempel \t för fliken \n för ny rad, och \" citattecken själva.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

Att förhindra att ”\\” agerar som ett escape-tecken, Lägg till ”\@” som ett prefix till strängen:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Strängjämförelser

Operator       |Beskrivning                         |Skiftlägeskänsligt|Exempel (ger `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Lika med                              |Ja           |`"aBc" == "aBc"`
`!=`           |Inte lika med                          |Ja           |`"abc" != "ABC"`
`=~`           |Lika med                              |Nej            |`"abc" =~ "ABC"`
`!~`           |Inte lika med                          |Nej            |`"aBc" !~ "xyz"`
`has`          |Right hand sida är en hela term i vänstra hand sida |Nej|`"North America" has "america"`
`!has`         |Right hand sida är inte en fullständig term i vänstra hand sida       |Nej            |`"North America" !has "amer"` 
`has_cs`       |Right hand sida är en hela term i vänstra hand sida |Ja|`"North America" has_cs "America"`
`!has_cs`      |Right hand sida är inte en fullständig term i vänstra hand sida       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Right hand sida är en term som prefix i vänstra hand sida         |Nej            |`"North America" hasprefix "ame"`
`!hasprefix`   |Right hand sida inte är en term prefixet i vänstra hand sida     |Nej            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Right hand sida är en term som prefix i vänstra hand sida         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Right hand sida inte är en term prefixet i vänstra hand sida     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Right hand sida är en term suffix i vänstra hand sida         |Nej            |`"North America" hassuffix "ica"`
`!hassuffix`   |Right hand sida inte är en term suffix i vänstra hand sida     |Nej            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Right hand sida är en term suffix i vänstra hand sida         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Right hand sida inte är en term suffix i vänstra hand sida     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Right hand sida som utförs som en efterföljande av vänstra hand sida  |Nej            |`"FabriKam" contains "BRik"`
`!contains`    |Right hand sida inträffar inte i vänstra hand sida           |Nej            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Right hand sida som utförs som en efterföljande av vänstra hand sida  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Right hand sida inträffar inte i vänstra hand sida           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Right hand sida är en inledande efterföljande av vänstra hand sida|Nej            |`"Fabrikam" startswith "fab"`
`!startswith`  |Right hand sida är inte en inledande efterföljande av vänstra hand sida|Nej        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Right hand sida är en inledande efterföljande av vänstra hand sida|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Right hand sida är inte en inledande efterföljande av vänstra hand sida|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Right hand sida är en avslutande efterföljande av vänstra hand sida|Nej             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Right hand sida är inte en avslutande efterföljande av vänstra hand sida|Nej         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Right hand sida är en avslutande efterföljande av vänstra hand sida|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Right hand sida är inte en avslutande efterföljande av vänstra hand sida|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|vänstra hand sida innehåller en matchning för Right hand sida        |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Som motsvarar ett av elementen       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Inte lika med ett element   |Ja           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Antal förekomster av en understräng i en sträng. Kan matcha vanlig strängar eller använda regex. Vanlig strängen matchar kan överlappa medan regex matchar inte stöds.

### <a name="syntax"></a>Syntax
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argument:
- `text` -Indatasträngen 
- `search` -Vanlig sträng eller ett reguljärt uttryck som matchar i texten.
- `kind` - _Normal_ | _regex_ (standard: normal).

### <a name="returns"></a>Returnerar

Antal gånger som strängen kan matchas i behållaren. Vanlig strängen matchar kan överlappa medan regex matchar inte.

### <a name="examples"></a>Exempel

#### <a name="plain-string-matches"></a>Vanlig strängen matchar

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Matchar Regex

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Extrahera

Hämtar en matchning för ett reguljärt uttryck från en given sträng. Du kan också också konverterar den extraherade understräng den angivna typen.

### <a name="syntax"></a>Syntax

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argument

- `regex` – Ett reguljärt uttryck.
- `captureGroup` -En positiv heltalskonstant som anger gruppen avbildning för att extrahera. 0 för hela matchar, 1 för värdet av först '(' parenteser')' i reguljära uttryck, 2 eller högre för efterföljande parentes.
- `text` – En sträng att söka.
- `typeLiteral` – En valfri typ literal (till exempel typeof(long)). Om den extrahera delsträngen omvandlas till den här typen.

### <a name="returns"></a>Returnerar
Delsträngen som matchas mot den angivna capture grupp captureGroup, du kan också konverteras till typeLiteral.
Om det finns ingen matchning eller typ konverze misslyckas kan returnera null.

### <a name="examples"></a>Exempel

I följande exempel extraherar den sista oktetten av *ComputerIP* från en post för pulsslag:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

I följande exempel extraherar den sista oktetten, konverterar den till en *verkliga* skriver (nummer) och beräknar det nästa IP-värdet
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

I exemplet nedan strängen *Trace* söks igenom efter en definition av ”varaktighet”. Matchningen kastas *verkliga* och multiplicerat med en tidkonstant (1 s) *som kastar varaktighet till typen timespan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty isnotempty, notempty

- *IsEmpty* returnerar true om argumentet är en tom sträng eller null (Se även *isnull*).
- *isnotempty* returnerar true om argumentet är inte en tom sträng eller null (Se även *isnotnull*). alias: *notempty*.

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


## <a name="parseurl"></a>parseurl

Delar upp en URL i dess delar (protokoll, värd, port, osv.) och returnerar ett katalogobjekt som innehåller delarna som strängar.

### <a name="syntax"></a>Syntax

```
parseurl(urlstring)
```

### <a name="examples"></a>Exempel

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Resultatet blir:
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


## <a name="replace"></a>Ersätt

Ersätter alla regex-matchningar med en annan sträng. 

### <a name="syntax"></a>Syntax

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argument

- `regex` -Reguljärt uttryck för matchning av. Det kan innehålla capture grupper i '('parentes')'.
- `rewrite` -Ersättning regex för någon matchning av matchande regex. Använda \0 för att referera till hela matchning, \1 för den första avbildning-gruppen, \2, och så vidare för efterföljande capture grupper.
- `input_text` -Den inkommande strängen att söka i.

### <a name="returns"></a>Returnerar
Texten som ska användas när du har ersatt alla matchningar av regex med utvärderingar av omarbetning. Matchar inte överlappar varandra.

### <a name="examples"></a>Exempel

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Kan ha följande resultat:

Aktivitet                                        |ersatt
------------------------------------------------|----------------------------------------------------------
4663 – ett försök gjordes att komma åt ett objekt  |Aktivitets-ID 4663: Ett försök gjordes att komma åt ett objekt.


## <a name="split"></a>split

Delar upp en given sträng enligt en angiven avgränsare och returnerar en matris med de resulterande delsträngar.

### <a name="syntax"></a>Syntax
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argument:

- `source` -Strängen som ska delas upp baserat på angiven avgränsare.
- `delimiter` -Avgränsaren som ska användas för att kunna dela Källsträngen.
- `requestedIndex` – En valfri nollbaserade index. Om den returnerade Strängmatrisen ska innehålla endast objekt (om det finns).


### <a name="examples"></a>Exempel

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Sammanfogar strängargument (har stöd för 1 – 16 argument).

### <a name="syntax"></a>Syntax
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Exempel
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Returnerar längden på en sträng.

### <a name="syntax"></a>Syntax
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Exempel
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>delsträngen

Extraherar en understräng från en viss källa-sträng, med början vid det angivna indexet. Du kan också kan begärda delsträngens längd anges.

### <a name="syntax"></a>Syntax
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argument:

- `source` -Källsträngen som delsträngen hämtas från.
- `startingIndex` -Nollbaserade från teckenpositionen för den begärda delsträngen.
- `length` – En valfri parameter som kan användas för att ange den begärda längden på den returnerade delsträngen.

### <a name="examples"></a>Exempel
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower toupper

Konverterar en given sträng till alla lägre eller versal.

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
Fortsätt med avancerade Självstudier:
* [Aggregeringsfunktioner](aggregations.md)
* [Avancerade aggregeringar](advanced-aggregations.md)
* [-Diagram](charts.md)
* [Arbeta med JSON och datastrukturer](json-data-structures.md)
* [Avancerad fråga skrivning](advanced-query-writing.md)
* [Kopplingar - mellan analys](joins.md)
