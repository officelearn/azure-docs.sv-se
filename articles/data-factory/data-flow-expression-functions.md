---
title: Azure Data Factory mappning datafunktioner Flow uttryck
description: Azure Data Factory mappning datafunktioner Flow uttryck
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 9a3d596d79aec2305dfc3ec61bd587da57f4a397
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56409024"
---
# <a name="mapping-data-flow-expression-functions"></a>Mappa datafunktioner Flow uttryck

Data Factory mappning Data flödar har ett Uttrycksspråk som kan användas för att konfigurera Datatransformationer.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Kontrollerar om värdet är NULL * ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
Returnerar ett nullvärde. Använd funktionen syntax(null()) om det finns en kolumn med namnet ”null”. Alla åtgärder som använder leder till ett null-värde * ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Baserat på ett villkor gäller ett värde eller den andra. ”Annan” anses vara NULL om det inte anges. Båda värdena måste vara kompatibel (numeriska, sträng...) * ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Baserat på alternerande villkor gäller för ett värde eller den andra. Om antalet indata är jämna, är den andra NULL för senaste villkoret * ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Jämförelse är lika med operatorn Ignorera skiftläge. Samma som <> = operator * ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Sammanfogar en variabel antal strängar tillsammans. Samma som den + operator med strängar * ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Sammanfogar en variabel antal strängar tillsammans med en avgränsare. Den första parametern är avgränsare * ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) -> ``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Tar bort en sträng med inledande och avslutande tecken. Om andra parameter är Ospecificerad rensar blanksteg. Annars den tar bort alla tecken som anges i den andra parametern * ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Vänster tar bort en ledande teckensträng. Om andra parameter är Ospecificerad rensar blanksteg. Annars den tar bort alla tecken som anges i den andra parametern * ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Höger tar bort en ledande teckensträng. Om andra parameter är Ospecificerad rensar blanksteg. Annars den tar bort alla tecken som anges i den andra parametern * ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extraherar en delsträng av en viss längd från en plats. Positionen är 1-baserad. Om du utelämnar längden som standard till slutet av strängen * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Lowercases en sträng * ``lower('GunChus') -> 'gunchus'``
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Uppercases en sträng * ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Returnerar längden på strängen * ``length('kiddo') -> 5``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Höger pads strängen som den angivna utfyllnad tills den är av en viss längd. Om strängen är lika med eller större än längden är en icke-alternativ * ``rpad('great', 10, '-') -> 'great-----'`` 
* ``rpad('great', 4, '-') -> 'great'`` 
* ``rpad('great', 8, '<>') -> 'great<><'`` 
*********************************
<code>lpad</code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Vänster pads strängen som den angivna utfyllnad tills den är av en viss längd. Om strängen är lika med eller större än längden är en icke-alternativ * ``lpad('great', 10, '-') -> '-----great'`` 
* ``lpad('great', 4, '-') -> 'great'`` 
* '' lpad (bra, 8, ' <>') -> ' <><great'``
*********************************
<code>vänstra</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extraherar en understräng start vid index 1 med ett antal tecken. Samma som DELSTRÄNGEN (str, 1, n) * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extraherar en understräng med antal tecken från höger. Samma som DELSTRÄNGEN (str, LENGTH(str) - n, n) * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Kontrollerar om strängen börjar med den angivna strängen * ``startsWith('great', 'gr') -> true``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Kontrollerar om strängen slutar med den angivna strängen * ``endsWith('great', 'eat') -> true``
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Söker efter positionen (1-baserad) av delsträngen i en sträng från en särskild plats. Om positionen utelämnas betraktas den som från början av strängen. 0 är returneras om inget hittas * ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Söker efter positionen (1-baserad) av delsträngen i en sträng. 0 är returneras om inget hittas * ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Ersätt en uppsättning tecken i en annan uppsättning tecken i strängen. Tecken har 1 till 1 ersättning * ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Kastar en sträng * ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Konverterar den första bokstaven i varje ord till versaler. Ord har identifierats som avgränsade med blanksteg * ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Ersätt alla förekomster av en understräng med en annan delsträngen i den angivna strängen * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Ersätt alla förekomster av ett regex-mönster med en annan delsträngen i den angivna strängen Använd '<regex>'(back quote) att matcha en sträng utan undantagstecken * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahera en matchande delsträng för en viss regex-mönster. Den sista parametern identifierar gruppen matchning och standard till 1 om det utelämnas. Använd ”<regex>'(back quote) att matcha en sträng utan undantagstecken * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Kontrollerar om strängen matchar angivna regex-mönster. Använd ”<regex>'(back quote) att matcha en sträng utan undantagstecken * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Mönstret är en sträng som matchas bokstavligt, med undantag till symbolerna: _ matchar valfritt tecken i inkommande (liknar. i posix reguljära uttryck) % matchar noll eller flera tecken i inkommande (liknar. * i posix reguljära uttryck).
Escape-tecken ”. Om ett escape-tecken före en särskild symbol eller annat escape-tecken, matchas bokstavligt följande tecken. Det går inte att undvika ett annat tecken.
* ``like('icecream', 'ice%') -> true``
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Kontrollerar om strängen matchar angivna regex-mönster * ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Kontrollerar om ett objekt i matrisen * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Konverterar en primitiv datatyp till en sträng. Ett format som kan anges för tal och datum. Om inget anges som standard hämtas. Java Decimalformatet används för tal. Standardformatet för datum är åååå-MM-dd * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Delar upp en sträng som baseras på en avgränsare och returnerar en matris med strängar * ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Delar upp en sträng som baseras på en avgränsare baserat på regex och returnerar en matris med strängar * ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Hämtar soundex-koden för strängen * ``soundex('genius') -> 'G520'``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Hämtar levenshtein avståndet mellan två strängar * ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extraherar en delmängd av en matris från en plats. Positionen är 1-baserad. Om du utelämnar längden som standard till slutet av strängen * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
Alltid returnerar värdet true. Använd funktionen syntax(true()) om det finns ett kolumnnamn som heter ”true” * ``isDiscounted == true()``
* ``isDiscounted() == true``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
Returnerar alltid ett falskt värde. Använd funktionen syntax(false()) om det finns ett kolumnnamn som heter ”FALSKT” * ``isDiscounted == false()``
* ``isDiscounted() == false``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Konverterar ett värde av ('t ”,” true ”,” y ”,” Ja ”, '1') till true och ('f', 'false', ' n”, ”Nej” ”0”) till FALSKT och NULL för ett annat värde * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Lägger till ett par av strängar eller siffror. Lägger till ett datum i ett antal dagar. Lägger till en matris med liknande typ till en annan. Samma som den + operator * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Subtraherar tal. Subtrahera från ett datum antal dagar. Samma som-operator * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multiplicerar två tal. Samma som den * operator * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dividerar två tal. Samma som den / operator * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Modulus av par med tal. Samma som %-operatorn * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Positivt Modulus av par med tal.
* ``pmod(-20, 8) -> 4``
*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Positivt Modulus av par med tal.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logiska operatorn och. Samma som & & * ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logisk OR-operator. Samma som || * ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logisk XOR operator. Samma som ^ operator * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logisk negation operator * ``not(true) -> false``
* ``not(premium)``
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Matchar typ av kolumnen. Kan endast användas i mönstret expressions.number matchar kort, heltal, long, double, float eller decimal, integrerad matchar kort sagt: heltal, långa, bråkdelar matchningar dubbla, float, decimal och datetime matchningar datum eller tidsstämpel typ * ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
Konverterar alla numeriska eller en sträng till ett kort värde. En valfri Java-Decimalformatet kan användas för konverteringen. Trunkerar alla heltal, long, float, dubbla * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
Konverterar alla numeriska eller en sträng till ett heltalsvärde. En valfri Java-Decimalformatet kan användas för konverteringen. Trunkerar alla long, float, double * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
Konverterar alla numeriska eller en sträng till ett långt värde. En valfri Java-Decimalformatet kan användas för konverteringen. Trunkerar alla float, double * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
Konverterar alla numeriska eller en sträng till ett flyttalsvärde. En valfri Java-Decimalformatet kan användas för konverteringen. Trunkerar alla double-värde * ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
Konverterar alla numeriska eller en sträng till ett double-värde. En valfri Java-Decimalformatet kan användas för konverteringen.
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Konverterar alla numeriska eller en sträng till ett decimalvärde. Om precision och scale inte anges standard som till (10.2). En valfri Java-Decimalformatet kan användas för konverteringen.
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jämförelse är lika med operatorn. Samma som == operator * ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'abc'=='abc' -> true``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jämförelse är inte lika med operatorn. Samma som! =-operator * ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Större jämförelseoperatorn. Samma som > operator * ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jämförelse mindre operator. Samma som < operator * ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jämförelse som är större än eller lika med-operator. Samma som > =-operator * ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jämförelse av mindre än eller lika med operatorn. Samma som < =-operator * ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Returnerar det största värdet i listan över värden som indata. Returnerar null om alla indata är null * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Jämförelse av mindre än eller lika med operatorn. Samma som < =-operator * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Upphöjer ett tal upphöjt till en annan * ``power(10, 2) -> 100``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar kvadratroten ur ett tal * ``sqrt(9) -> 3``
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräkna kuben roten för ett tal * ``cbrt(8) -> 2.0``
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Eliminerar ett tal. Aktiverar positiva tal till negativt och vice versa * ``negate(13) -> -13``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar en cosinus-värdet * ``cos(10) -> -0.83907152907``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar ett consine inverterade värde * ``acos(1) -> 0.0``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar en hyperboliskt cosinus för ett värde * ``cosh(0) -> 1.0``
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar ett värde för sinus * ``sin(2) -> 0.90929742682``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar ett värde för inverterade sinus * ``asin(0) -> 0.0``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar ett värde för hyperboliska sinus * ``sinh(0) -> 0.0``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar en tangensvärdet * ``tan(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar en omvänd tangensvärdet * ``atan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar en hyperbolisk tangensvärdet * ``tanh(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Returnerar vinkeln i radianer mellan positiva x-axeln i ett plan och den plats som anges av koordinaterna * ``atan2(0, 0) -> 0.0``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Beräkna Fakulteten av ett tal * ``factorial(5) -> 120``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Returnerar det största heltalet som är inte större än antalet * ``floor(-0.1) -> -1``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Returnerar det minsta heltalet som är inte mindre än antalet * ``ceil(-0.1) -> 0``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Konverterar radianer till grader * ``degrees(3.141592653589793) -> 180``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Beräknar log-värde. Ett valfritt base kan vara, annars Eulers flera om används * ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Beräknar log-värde baserat på bas 10 * ``log10(100) -> 2``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Avrundar ett tal baserat på en valfri skala och ett valfritt avrundning läge. Om detta utelämnas används skalan som standard till 0.  Om detta utelämnas används läget som standard till ROUND_HALF_UP(5). Värden för avrundning är 1 - ROUND_UP 2 - ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Hämtar det aktuella datumet när jobbet börjar köras. Du kan skicka en valfri tidszon i form av 'GMT', 'PST', 'UTC', ' America/Caymanöarna'. Den lokala tidszonen används som standard.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Hämtar den aktuella tidsstämpeln när jobbet börjar köras med lokala tidszon * ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Konverterar en sträng till ett datum som anges ett valfritt datumformat. Om du utelämnar datumformatet accepteras kombinationer av följande. [åååå, yyyy-[M] M, yyyy-[M] [d] d M, åååå [M] M-[d] d, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *] * ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Konverterar en sträng till ett datum som anges ett valfritt Tidsstämpelformat. Om detta utelämnas används tidsstämpeln i standard mönstret åååå-[M] M-[d] d: mm: ss [. f...] används * ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Konverterar tidsstämpel till UTC. Du kan skicka en valfri tidszon i form av 'GMT', 'PST', 'UTC', ' America/Caymanöarna'. Som standard till den aktuella tidszonen * ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Hämtar aktuellt tidsstämpeln som UTC. Du kan skicka en valfri tidszon i form av 'GMT', 'PST', 'UTC', ' America/Caymanöarna'. Som standard till den aktuella tidszonen * ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hämtar månadsvärdet för ett datum eller en tidsstämpel * ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hämtar värdet för året för ett datum * ``year(toDate('2012-8-8')) -> 2012``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Hämtar värdet för timmen för en tidsstämpel. Du kan skicka ett valfritt tidszonen i form av 'GMT', 'PST', 'UTC', ' America/Caymanöarna'. Den lokala tidszonen används som standard.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Hämtar en tidsstämpel minuten. Du kan skicka en valfri tidszon i form av 'GMT', 'PST', 'UTC', ' America/Caymanöarna'. Den lokala tidszonen används som standard.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Hämtar det andra värdet för ett datum. Du kan skicka en valfri tidszon i form av 'GMT', 'PST', 'UTC', ' America/Caymanöarna'. Den lokala tidszonen används som standard.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hämtar dagen i månaden angivet date * ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hämtar veckodag angivet date. 1 – söndag, 2 – måndag..., 7 – lördag * ``dayOfWeek(toDate('2018-06-08')) -> 7``
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hämtar dagen på året angivet date * ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Hämtar vecka på året angivet date * ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Hämtar det sista datumet i månaden angivet date * ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Hämtar antalet månader mellan två datesYou kan skicka en valfri tidszon i form av 'GMT', 'PST', 'UTC', ' America/Caymanöarna'. Den lokala tidszonen används som standard.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Lägg till månader till ett datum eller en tidsstämpel * ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Lägger till dagar till ett datum eller en tidsstämpel. Samma som den + operator för datum * ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtrahera månader från ett datum. Samma som-operator för datum * ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtrahera månader från ett datum eller en tidsstämpel * ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
Returnerar nästa unika sekvensen. Det har på varandra följande endast inom en partition och inleds med partitionId * ``nextSequence() -> 12313112``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Beräknar MD5-sammanfattning av uppsättning kolumn med varierande primitiva datatyper och returnerar en hexadecimal 32 tecken lång sträng. Den kan användas för att beräkna ett fingeravtryck för en rad * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Beräknar SHA-1-sammanfattning av uppsättning kolumn med varierande primitiva datatyper och returnerar en hexadecimal 40 tecken lång sträng. Den kan användas för att beräkna ett fingeravtryck för en rad * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Beräknar SHA-2-sammanfattning av uppsättning kolumn med varierande primitiva datatyper som får en bit-längd kan bara vara av värden 0(256), 224, 256, 384, 512. Den kan användas för att beräkna ett fingeravtryck för en rad * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Beräknar CRC32-hash för uppsättning kolumn med varierande primitiva datatyper som får en bit-längd som bara kan vara av värden 0(256) 224, 256, 384, 512. Den kan användas för att beräkna ett fingeravtryck för en rad * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontrollerar om raden har markerats för insert. Transformationer som tar mer än en Indataströmmen kan skicka (1-baserade) indexet på dataströmmen. Standardvärdet för stream-index är en * ``isInsert() -> true``
* ``isInsert(1) -> false``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontrollerar om raden har markerats för uppdatering. Transformationer som tar mer än en Indataströmmen kan skicka (1-baserade) indexet på dataströmmen. Standardvärdet för stream-index är en * ``isUpdate() -> true``
* ``isUpdate(1) -> false``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontrollerar om raden har markerats för borttagning. Transformationer som tar mer än en Indataströmmen kan skicka (1-baserade) indexet på dataströmmen. Standardvärdet för stream-index är en * ``isDelete() -> true``
* ``isDelete(1) -> false``
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontrollerar om raden matchas på sökning. Transformationer som tar mer än en Indataströmmen kan skicka (1-baserade) indexet på dataströmmen. Standardvärdet för stream-indexet är 1 * ``isMatch() -> true``
* ``isMatch(1) -> false``
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontrollerar om raden har markerats som fel. Transformationer som tar mer än en Indataströmmen kan skicka (1-baserade) indexet på dataströmmen. Standardvärdet för stream-index är en * ``isError() -> true``
* ``isError(1) -> false``
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontrollerar om raden har markerats ignoreras. Transformationer som tar mer än en Indataströmmen kan skicka (1-baserade) indexet på dataströmmen. Standardvärdet för stream-index är en * ``isIgnore() -> true``
* ``isIgnore(1) -> false``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Hämtar summan av en numerisk kolumn * ``sum(col) -> value``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Baserat på kriterier hämtar summan av en numerisk kolumn. Villkoret kan baseras på valfri kolumn * ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales) ``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Hämtar summan av distinkta värden för en numerisk kolumn * ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Baserat på kriterier hämtar summan av en numerisk kolumn. Villkoret kan baseras på valfri kolumn * ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales) ``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Hämtar det sammanlagda antalet värden. Om den eller de valfria kolumnerna anges, ignoreras NULL-värden i antalet * ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Baserat på ett villkor hämtar sammanlagda antal värden. Om den valfria kolumnen anges, ignoreras NULL-värden i antalet * ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Hämtar det sammanlagda antalet distinkta värdena för en uppsättning kolumner * ``countDistinct(custId, custName) -> 60``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Hämtar medelvärdet av värdena för en kolumn * ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Baserat på ett villkor hämtar medelvärdet av värdena för en kolumn * ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Hämtar medelvärdet av värdena för en kolumn. Samma som genomsn. * ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Baserat på ett villkor hämtar medelvärdet av värdena för en kolumn. Samma som avgIf * ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Hämtar det lägsta värdet på en kolumn * ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Baserat på ett villkor, hämtar det lägsta värdet på en kolumn * ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Hämtar det maximala värdet för en kolumn * ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Baserat på ett villkor, hämtar det maximala värdet för en kolumn * ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hämtar standardavvikelsen för en kolumn * ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar standardavvikelsen för en kolumn * ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hämtar populationens standardavvikelse för en kolumn * ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar populationens standardavvikelse för en kolumn * ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hämtar exemplet standardavvikelsen för en kolumn * ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar exemplet standardavvikelsen för en kolumn * ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hämtar variansen för en kolumn * ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar variansen för en kolumn * ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hämtar populationens varians för en kolumn * ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar populationens varians för en kolumn * ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hämtar oprioriterad variansen för en kolumn * ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar oprioriterad variansen för en kolumn * ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Hämtar population kovariansen mellan två kolumner * ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar population kovariansen för två kolumner * ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Hämtar exemplet kovariansen för två kolumner * ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar exemplet kovariansen för två kolumner * ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hämtar en datamängds en kolumn * ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar fördelning av en kolumn * ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Hämtar snedställningar för en kolumn * ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Baserat på ett villkor, hämtar snedställningar för en kolumn * ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Hämtar det första värdet i en kolumngrupp. Om den andra parametern IgnoreraNull-värden utelämnas antas den vara falskt * ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Hämtar det sista värdet för en kolumngrupp. Om du utelämnar den andra parametern IgnoreraNull-värden förutsätts FALSKT * ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Hämtar värdet för de första n raderna med parametern utvärderas före den aktuella raden. Den andra parametern är antalet rader tillbaka. Standardvärdet är en. Om det inte så många rader som ett null-värde som returneras om ett standardvärde har angetts * ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Hämtar värdet för de första parametern utvärderas n raderna efter den aktuella raden. Den andra parametern är antalet rader som ska hoppas. Standardvärdet är en. Om det inte så många rader som ett null-värde som returneras om ett standardvärde har angetts * ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
Funktionen CumeDist beräknar positionen för ett värde i förhållande till alla värden i partitionen. Resultatet är antalet rader som föregående eller lika med den aktuella raden i sorteringen av partitionen dividerat med det totala antalet rader i fönstret partitionen. Koppla värden i den ordning som ska utvärderas till samma plats.
* ``cumeDist() -> 1``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Funktionen NTile dividerar rader för varje partition för fönster i `n` buckets som sträcker sig från 1 till högst `n`. Bucket värden varierar med högst 1. Om antalet rader i partitionen inte jämnt delbart i hur många buckets, är värdena resten distribuerade en för varje bucket, från och med den första bucketen. Funktionen NTile är särskilt användbart för beräkning av tertiles, Kvartiler, deciles och andra vanliga sammanfattande statistik. Funktionen beräknar två variabler under initieringen: Storleken på en vanlig bucket och hur många buckets som har en extra rad har lagts till (när raderna jämnt inte passar i hur många buckets); Båda variablerna baseras på storleken på den aktuella partitionen. Under beräkningen håller funktionen reda på aktuellt radnummer och det aktuella bucket-antalet radnumret där en bucket ändras (bucketThreshold). När de aktuella raden nummer når bucket tröskelvärdet, ökar bucket-värde med ett och tröskelvärdet ökas med bucket-storleken (plus en extra om den aktuella bucketen fylls ut).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Beräknar rangordningen för ett värde i en grupp av värden. Resultatet är ett plus antalet rader föregående eller lika med den aktuella raden i sorteringen av partitionen. Värdena kommer att generera luckor i sekvensen. Ranking fungerar även när data inte sorteras och söker efter ändring i värden * ``rank(salesQtr, salesAmt) -> 1``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Beräknar rangordningen för ett värde i en grupp av värden. Resultatet är ett plus antalet rader föregående eller lika med den aktuella raden i sorteringen av partitionen. Värdena kommer inte att generera luckor i sekvensen. Kompakt rangordning fungerar även om data inte sorteras och söker efter ändring i värden * ``denseRank(salesQtr, salesAmt) -> 1``
*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
Tilldelar en sekventiell rad numrering för rader i ett fönster som startar med 1 * ``rowNumber() -> 1``
