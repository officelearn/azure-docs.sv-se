---
title: Grammatik-format finns i Knowledge utforskning Service API | Microsoft Docs
description: Lär dig mer om grammatikformatet i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 27202379b8c36696a380049336229cac040b0108
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351882"
---
# <a name="grammar-format"></a>Grammar Format
Grammatiken är en XML-fil som definierar vilka viktat av naturligt språk frågor som tjänsten kan tolka samt hur dessa frågor med naturligt språk översätts till semantisk fråga uttryck.  Grammatik syntax är baserad på [SRGS](http://www.w3.org/TR/speech-grammar/), en W3C-standard för speech recognition grammatik med tillägg för att stödja index dataintegrering och semantiska funktioner.

Nedan beskrivs var och en av de syntaktiska element som kan användas i en grammatik.  Se [det här exemplet](#example) för en fullständig grammatik som visar hur du använder de här elementen i kontexten.

### <a name="grammar-element"></a>grammatik Element 
Den `grammar` elementet är det översta elementet i XML-specifikationen grammatik.  Det obligatoriska `root` attributet anger namnet på rot-regel som definierar startpunkten för grammatik.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Importera Element
Den `import` elementet import schemadefinition från en extern fil att aktivera attributet referenser. Elementet måste vara underordnad den översta `grammar` element och finnas före alla `attrref` element. Det obligatoriska `schema` attributet anger namnet på en schemafil som finns i samma katalog som grammatik XML-fil. Det obligatoriska `name` element anger schemat alias som efterföljande `attrref` element använder när du refererar till attribut som definierats i det här schemat.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>regeln Element
Den `rule` elementet definierar en grammatikregel, en strukturell enhet som anger en uppsättning frågeuttryck som systemet kan tolka.  Elementet måste vara underordnad den översta `grammar` element.  Det obligatoriska `id` attributet anger namnet på regeln, som refereras till från `grammar` eller `ruleref` element.

En `rule` elementet definierar en uppsättning juridiska expansion.  Text-token matcha direkt mot indatafrågan.  `item` element ange upprepas och ändra tolkning sannolikhet.  `one-of` element ange alternativa val.  `ruleref` element kan skapa mer komplexa expansion från enklare de.  `attrref` element kan matchningar mot attributvärden från indexet.  `tag` element ange semantiken för tolkning och kan ändra sannolikheten tolkning.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>exempel Element
Det valfria `example` element anger exempel fraser som kan godkännas av den innehållande `rule` definition.  Detta kan användas för dokumentation och/eller automatiserad testning.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>element Element
Den `item` element grupper en sekvens med grammatik konstruktioner.  Den kan användas till repetitioner av expansion sekvens eller för att ange alternativa tillsammans med den `one-of` element.

När en `item` elementet är inte en underordnad till en `one-of` element, det kan ange upprepning av slutna sekvensen genom att tilldela den `repeat` attributet count-värdet.  Antal värdet ”*n*” (där *n* är ett heltal) anger att sekvensen måste äga rum exakt *n* gånger.  Antal värdet ”*m*-*n*” gör att aktivitetssekvensen ska visas mellan *m* och *n* gånger portintervallet.  Antal värdet ”*m*-” anger att teckensekvensen måste finnas minst *m* gånger.  Det valfria `repeat-logprob` attributet kan användas för att ändra tolkning sannolikheten för varje ytterligare upprepning utöver minst.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

När `item` element visas som underordnade till en `one-of` elementet definierar uppsättningen expanderades alternativ.  I den här användning, det valfria `logprob` attributet anger den relativa logg sannolikheten mellan de olika alternativen.  Få en sannolikhet *p* mellan 0 och 1 sannolikheten för motsvarande loggen kan beräknas som loggen (*p*), där log() är den funktionen den naturliga logaritmen.  Om inget anges `logprob` standardvärdet är 0 som inte ändrar sannolikheten tolkning.  Observera att loggen sannolikheten är alltid ett negativt värde eller 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>en-för-Element
Den `one-of` element anger alternativa expansion mellan en underordnad `item` element.  Endast `item` elementen kan finnas i en `one-of` element.  Relativa sannolikhet mellan olika alternativ kan anges den `logprob` attribut i varje underordnad `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref Element
Den `ruleref` element anger giltiga expansion via referenser till en annan `rule` element.  Med `ruleref` element, mer komplexa uttryck kan byggas från enklare regler.  Det obligatoriska `uri` attributet anger namnet på den refererade `rule` med syntaxen ”#*rulename*”.  Om du vill samla in semantiska utdata från den refererade regeln, Använd det valfria `name` attribut för att ange namnet på en variabel som semantiska utdata har tilldelats.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref Element
Den `attrref` element refererar till ett index-attributet tillåter matchning mot attributvärden observeras i indexet.  Det obligatoriska `uri` attributet anger index schemanamn och attributnamn med syntaxen ”*schemaName*#*%{attrname/*”.  Det måste finnas en föregående `import` element som importerar schema med namnet *schemaName*.  Attributets namn är namnet på ett attribut som angetts i motsvarande schemat.

Utöver att matcha användaren i `attrref` elementet returnerar också ett strukturerade frågeobjekt som utdata som väljer delmängd av objekt i indexet matchar indatavärdet.  Använd det valfria `name` attribut för att ange namnet på variabeln där objektet frågeresultatet ska sparas.  Frågeobjektet kan sammanställas med andra frågeobjekt för att bilda mer komplexa uttryck.  Se [semantisk tolkning](SemanticInterpretation.md) mer information.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Frågan slutförs 
För att stödja frågan slutföranden vid tolkning partiella användarfrågor, måste varje refererade attribut innehålla ”starts_with” som en åtgärd i schemadefinitionen.  Får en fråga prefix användaren `attrref` matcha alla värden i indexet som Slutför prefixet, och ge varje fullständiga värde som en separat tolkning av grammatik.  

Exempel:
* Matchar `<attrref uri="academic#Keyword" name="keyword"/>` mot frågan prefixet ”dat” genererar en tolkning för rapporter om ”databas”, en tolkning för rapporter om ”datautvinning”, osv.
* Matchar `<attrref uri="academic#Year" name="year"/>` mot frågan prefixet ”200” genererar en tolkning för papper i ”2000”, en tolkning för rapporter i ”2001”, osv.

#### <a name="matching-operations"></a>Matchande åtgärder
Förutom exakt matchning Välj attributet typer också stöd för prefix och olikhet matchar via det valfria `op` attribut.  Om inga objekt i indexet har ett värde som matchar, grammatik sökvägen blockeras och tjänsten kommer inte att generera någon tolkningar passerar över den här sökvägen för grammatik.   Den `op` attributet standardvärden ”eq”.

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

I följande tabell visas de stöds `op` värden för varje typ av attribut.  Deras användning kräver motsvarande indexåtgärden som ska ingå i attributet schemadefinition.

| Attributtyp | Op värde | Beskrivning | Indexåtgärden
|----|----|----|----|
| Sträng | EQ | Exakt matchning av sträng | lika med |
| Sträng | starts_with | Prefix för matchning av sträng | starts_with |
| Int32, Int64, Double | EQ |  Numeriska identisk motsvarighet | lika med |
| Int32, Int64, Double | lt, le, gt, ge | Numeriska olikhet matchning (<, < =, >, > =) | is_between |
| Int32, Int64, Double | starts_with | Prefixmatchning av värdet i decimalformat | starts_with |

Exempel:
* `<attrref uri="academic#Year" op="lt" name="year"/>` matchar den inmatade strängen ”2000” och returnerar alla rapporter som publiceras innan år 2000, exklusivt.
* `<attrref uri="academic#Year" op="lt" name="year"/>` matchar inte den inmatade strängen ”20” eftersom det finns inga artiklar i indexet publiceras innan år 20.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` matchar den inmatade strängen ”dat” och returnerar i en enkel tolkning handlingar om ”databas”, ”datautvinning”, osv.  Detta är en sällsynt användningsfall.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` matchar den inmatade strängen ”20” och returnerar i en enkel tolkning handlingar som publicerats i 200 299, 2000 2999 osv.  Detta är en sällsynt användningsfall.

### <a name="tag-element"></a>tagga Element
Den `tag` elementet anger hur en sökväg till grammatik ska tolkas.  Den innehåller en sekvens med semikolon avslutas instruktioner.  Ett uttryck kan vara en tilldelning av ett litteralvärde eller en variabel till en annan variabel.  Det kan också tilldela en variabel utdata för en funktion med 0 eller fler parametrar.  Varje funktionsparameter kan anges med ett litteralvärde eller en variabel.  Om funktionen inte returnerar några utdata utelämnas tilldelningen.  Variabeln scope är lokala för som innehåller regeln.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Varje `rule` i grammatik har en fördefinierad variabel med namnet ”out” som representerar semantiska utdata från regeln.  Värdet beräknas genom att utvärdera var och en av semantiska instruktionerna i sökvägen till den `rule` matcha användaren fråga indata.  Värdet som tilldelas variabeln ”out” i slutet av utvärderingen är semantiska utdata från regeln.  Semantiska utdata tolka en användarfråga mot grammatiken är semantiska utdata från rot-regel.

Vissa rapporter kan ändra sannolikheten för en tolkning sökväg genom att introducera en additiva loggen sannolikhet förskjutning.  Vissa rapporter kan avvisa tolkning helt och hållet om villkoren inte uppfylls har angetts.

En lista över semantiska funktioner som stöds, se [semantiska funktioner](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Tolkning sannolikhet
Sannolikheten för en tolkning sökväg via grammatiken är kumulativa loggen sannolikheten att alla de `<item>` element och semantiska funktioner påträffades på vägen.  Det beskriver relativa sannolikheten för matchning av en viss inkommande sekvens.

Få en sannolikhet *p* mellan 0 och 1 sannolikheten för motsvarande loggen kan beräknas som loggen (*p*), där log() är funktionen den naturliga logaritmen.  Med hjälp av loggen sannolikhet kan ackumuleras gemensamma sannolikheten för en tolkning sökväg via enkel addition.  Det gör också flyttals underskott som är gemensamma för sådana gemensamma sannolikhetsberäkningar.  Observera att avsiktligt sannolikheten loggen är alltid ett negativt värde eller 0, där större värden anger högre sannolikhet.

<a name="example"></a>
## <a name="example"></a>Exempel
Följande är ett exempel XML från domänen academic publikationer som visar de olika elementen i en grammatik:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
