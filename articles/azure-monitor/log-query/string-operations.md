---
title: Arbeta med strängar i Azure Monitor logg frågor | Microsoft Docs
description: Beskriver hur du redigerar, jämför, söker i och utför en rad andra åtgärder på strängar i Azure Monitor logg frågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75397474"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Arbeta med strängar i Azure Monitor logg frågor


> [!NOTE]
> Du bör slutföra [Kom igång med Azure Monitor Log Analytics](get-started-portal.md) och [komma igång med Azure Monitor logg frågor](get-started-queries.md) innan du slutför den här kursen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

I den här artikeln beskrivs hur du redigerar, jämför, söker i och utför en rad andra åtgärder på strängar.

Varje tecken i en sträng har ett index nummer, baserat på dess plats. Det första symbolen är vid index 0, nästa steg är 1 och så vidare. Olika sträng funktioner använder index nummer som du ser i följande avsnitt. I många av följande exempel används kommandot **Print** för att demonstrera sträng manipulation utan att använda en speciell data källa.


## <a name="strings-and-escaping-them"></a>Strängar och undantags tecken
Sträng värden radbryts med antingen enkla eller dubbla citat tecken. Omvänt\\snedstreck () används för att undanta tecken till tecknet som följer, till exempel \t för tab, \n för ny rad \" och citat tecken.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Om du vill\\förhindra att "" fungerar som ett escape-tecken\@lägger du till "" som ett prefix till strängen:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Sträng jämförelser

Operator       |Beskrivning                         |Skift läges känslig|Exempel (avkastning `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Är lika med                              |Ja           |`"aBc" == "aBc"`
`!=`           |Inte lika med                          |Ja           |`"abc" != "ABC"`
`=~`           |Är lika med                              |Inga            |`"abc" =~ "ABC"`
`!~`           |Inte lika med                          |Inga            |`"aBc" !~ "xyz"`
`has`          |Höger sida är en hel term i vänster sida |Inga|`"North America" has "america"`
`!has`         |Höger sida är inte en fullständig term i vänster sida       |Inga            |`"North America" !has "amer"` 
`has_cs`       |Höger sida är en hel term i vänster sida |Ja|`"North America" has_cs "America"`
`!has_cs`      |Höger sida är inte en fullständig term i vänster sida       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Höger sida är ett term prefix i vänster sida         |Inga            |`"North America" hasprefix "ame"`
`!hasprefix`   |Höger sida är inte ett term prefix i vänster sida     |Inga            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Höger sida är ett term prefix i vänster sida         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Höger sida är inte ett term prefix i vänster sida     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Höger sida är ett term suffix i vänster sida         |Inga            |`"North America" hassuffix "ica"`
`!hassuffix`   |Höger sida är inte ett term suffix i vänster sida     |Inga            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Höger sida är ett term suffix i vänster sida         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Höger sida är inte ett term suffix i vänster sida     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Höger sida visas som en underordnad sida till vänster  |Inga            |`"FabriKam" contains "BRik"`
`!contains`    |Höger sida visas inte i vänster sida           |Inga            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Höger sida visas som en underordnad sida till vänster  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Höger sida visas inte i vänster sida           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Höger sida är en inledande delsekvens av vänster sida|Inga            |`"Fabrikam" startswith "fab"`
`!startswith`  |Höger sida är inte en inledande underordnad del i vänster sida|Inga        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Höger sida är en inledande delsekvens av vänster sida|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Höger sida är inte en inledande underordnad del i vänster sida|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Höger sida är en avslutande delsekvens av den vänstra sidan|Inga             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Den högra sidan är inte en avslutande delsekvens av den vänstra sidan|Inga         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Höger sida är en avslutande delsekvens av den vänstra sidan|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Den högra sidan är inte en avslutande delsekvens av den vänstra sidan|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|den vänstra hand sidan innehåller en matchning för höger sida        |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Lika med ett av elementen       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Är inte lika med något av elementen   |Ja           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Räknar förekomster av en del sträng i en sträng. Kan matcha vanliga strängar eller använda regex. Matchningar med oformaterade strängar kan överlappa även om regex matchar inte.

### <a name="syntax"></a>Syntax
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Ogiltiga
- `text`– Indatasträngen 
- `search`– En vanlig sträng eller ett reguljärt uttryck som matchar text i text.
- `kind` - _normalt_ | _regex_ (standard: normal).

### <a name="returns"></a>Returnerar

Antalet gånger som Sök strängen kan matchas i behållaren. Matchningar med oformaterade strängar kan överlappa även om regex matchar inte.

### <a name="examples"></a>Exempel

#### <a name="plain-string-matches"></a>Vanliga sträng matchningar

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex matchningar

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>utvinning

Hämtar en matchning för ett reguljärt uttryck från en specifik sträng. Du kan också konvertera den extraherade under strängen till den angivna typen.

### <a name="syntax"></a>Syntax

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argument

- `regex`– Ett reguljärt uttryck.
- `captureGroup`– En positiv heltals konstant som anger vilken infångnings grupp som ska extraheras. 0 för hela matchningen 1 för det värde som matchas av den första ("parentesen") "i det reguljära uttrycket, 2 eller mer för efterföljande parenteser.
- `text`– En sträng att söka i.
- `typeLiteral`– En valfri typ literal (till exempel typeof (Long)). Om den extraherade del strängen har angetts konverteras den till den här typen.

### <a name="returns"></a>Returnerar
Del strängen matchad mot den angivna insamlings gruppen captureGroup, eventuellt konverteras till typeLiteral.
Om det inte finns någon matchning, eller om typ konverteringen Miss lyckas, returnerar du null.

### <a name="examples"></a>Exempel

I följande exempel extraheras den sista oktetten *ComputerIP* från en pulsslags post:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

I följande exempel extraherar du den sista oktetten, skickar den till en *riktig* typ (nummer) och beräknar nästa IP-värde
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

I exemplet nedan genomsöks sträng *spårningen* efter en definition av "varaktighet". Matchningen omvandlas till *verkligt* värde och multipliceras med en tidskonstant (1) *som kastar varaktigheten för att skriva TimeSpan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, nofrestar

- *IsEmpty* returnerar true om argumentet är en tom sträng eller null (se även *IsNull*).
- *isnotempty* returnerar true om argumentet inte är en tom sträng eller ett null-värde (se även *isnotnull*). alias: *nofrestar*.

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

Delar upp en URL i dess delar (protokoll, värd, Port osv.) och returnerar ett Dictionary-objekt som innehåller delarna som strängar.

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


## <a name="replace"></a>bytt

Ersätter alla regex-matchningar med en annan sträng. 

### <a name="syntax"></a>Syntax

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argument

- `regex`– Det reguljära uttrycket som ska matchas av. Den kan innehålla infångnings grupper i ' (' parenteser ') '.
- `rewrite`– Ersättnings regex för all matchning som görs genom matchande regex. Använd \ 0 om du vill referera till hela matchningen, \ 1 för den första infångnings gruppen, \ 2, och så vidare för efterföljande infångnings grupper.
- `input_text`– Den inmatade sträng som ska genomsökas.

### <a name="returns"></a>Returnerar
Texten efter att alla matchningar av regex har ersatts med utvärdering av omskrivning. Matchningar överlappar inte.

### <a name="examples"></a>Exempel

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Kan ha följande resultat:

Aktivitet                                        |ersätter
------------------------------------------------|----------------------------------------------------------
4663 – ett försök gjordes att komma åt ett objekt  |Aktivitets-ID 4663: ett försök gjordes att komma åt ett objekt.


## <a name="split"></a>split

Delar upp en given sträng enligt en angiven avgränsare och returnerar en matris med de resulterande del strängarna.

### <a name="syntax"></a>Syntax
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Ogiltiga

- `source`– Strängen som ska delas upp enligt angiven avgränsare.
- `delimiter`– Avgränsaren som ska användas för att dela käll strängen.
- `requestedIndex`– Ett valfritt noll-baserat index. Om den returnerade sträng mat ris filen bara innehåller objektet (om det finns).


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

Sammanfogar sträng argument (stöder 1-16 argument).

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


## <a name="substring"></a>under sträng

Extraherar en under sträng från en given käll sträng med början vid det angivna indexet. Du kan också ange längden på den begärda del strängen.

### <a name="syntax"></a>Syntax
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Ogiltiga

- `source`– Käll strängen som under strängen kommer att hämtas från.
- `startingIndex`– Den nollbaserade start tecken positionen för den begärda del strängen.
- `length`– En valfri parameter som kan användas för att ange den begärda längden för den returnerade del strängen.

### <a name="examples"></a>Exempel
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Konverterar en specifik sträng till gemener eller versaler.

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
* [Arbeta med JSON och data strukturer](json-data-structures.md)
* [Avancerad frågeskrivning](advanced-query-writing.md)
* [Kopplingar – kors analys](joins.md)
