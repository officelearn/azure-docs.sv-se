---
title: Grammatikformat - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Läs mer om grammatikformat i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 72dec3effe5e350818c01cf65c9140e778effd17
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860295"
---
# <a name="grammar-format"></a>Grammatikformat

Grammatik är en XML-fil som definierar vilka viktad frågor med naturligt språk som tjänsten kan tolka samt hur dessa frågor med naturligt språk översätts till semantiska frågeuttryck.  Grammatik-syntax är baserad på [SRGS](http://www.w3.org/TR/speech-grammar/), en W3C-standard för tal erkännande grammatik, med tillägg för integrering av index och semantisk funktioner.

Nedan beskrivs var och en av de syntaktiska element som kan användas i en grammatik.  Se [det här exemplet](#example) för en fullständig grammatik som visar hur du använder de här elementen i kontexten.

### <a name="grammar-element"></a>grammatik Element

Den `grammar` elementet har det översta elementet i XML-specifikationen grammatik.  De nödvändiga `root` attributet anger namnet på rot-regel som definierar grammatik startpunkt.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Importera Element

Den `import` elementet importerar en schemadefinitionen från en extern fil att aktivera attributet referenser. Elementet måste vara en underordnad till den översta `grammar` element och visas innan någon `attrref` element. De nödvändiga `schema` attributet anger namnet på en schemafil som finns i samma katalog som XML-filen grammatik. De nödvändiga `name` elementet anger schemat alias som efterföljande `attrref` element använder när du refererar till attribut som definierats i det här schemat.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>regeln Element

Den `rule` elementet definierar en strukturella enhet som anger en uppsättning med frågeuttryck som systemet kan tolka en grammatik-regel.  Elementet måste vara en underordnad till den översta `grammar` element.  De nödvändiga `id` attributet anger namnet på regeln, som refereras till från `grammar` eller `ruleref` element.

En `rule` elementet definierar en uppsättning juridiska expanderar.  Text-token matcha direkt mot indatafrågan.  `item` element ange upprepningar och alter tolkning sannolikhet.  `one-of` indikationer på alternativa val.  `ruleref` element kan konstruktion av mer komplexa expanderar från enklare som.  `attrref` element kan matchningar mot attributvärden från indexet.  `tag` element ange semantiken för tolkningen och kan ändra tolkning sannolikheten.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>exempel Element

Den valfria `example` elementet anger exempel fraser som kan godkännas av den som innehåller `rule` definition.  Detta kan användas för dokumentation och/eller automatiska tester.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>element Element

Den `item` elementet grupperar en sekvens med grammatik konstruktioner.  Den kan användas till repetitioner av expansion sekvensen eller för att ange alternativa tillsammans med den `one-of` element.

När en `item` elementet är inte en underordnad till en `one-of` element, den kan ange upprepning av slutna sekvensen genom att tilldela den `repeat` attributet till ett värde för antal.  Antal värdet ”*n*” (där *n* är ett heltal) anger att sekvensen måste äga rum exakt *n* gånger.  Antal värdet ”*m*-*n*” gör att aktivitetssekvensen ska visas mellan *m* och *n* gånger portintervallet.  Antal värdet ”*m*-” anger att teckensekvensen måste finnas minst *m* gånger.  Den valfria `repeat-logprob` attributet kan användas för att ändra tolkning sannolikheten för varje ytterligare upprepning utöver minimum.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

När `item` element visas som underordnade till en `one-of` element de definiera uppsättningen alternativ för utökning.  I den här användningen, det valfria `logprob` attributet anger den relativa log sannolikheten mellan olika alternativ.  Får en sannolikhet *p* mellan 0 och 1 motsvarande log sannolikheten kan beräknas som log (*p*), där log() är funktionen naturliga loggen.  Om inte anges `logprob` standardvärdet är 0 som inte ändrar tolkning sannolikheten.  Observera att loggen sannolikheten alltid är ett negativt värde eller 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>one-of Element

Den `one-of` elementet anger alternativa expanderar mellan en underordnad `item` element.  Endast `item` element kan förekomma inuti en `one-of` element.  Relativa sannolikhet mellan olika alternativ kan anges den `logprob` attribut i varje underordnad `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref Element

Den `ruleref` elementet anger giltiga expanderar via referenser till en annan `rule` element.  Med `ruleref` element, mer komplexa uttryck kan byggas från enklare regler.  De nödvändiga `uri` attributet anger namnet på den refererade `rule` med syntaxen ”#*rulename*”.  Om du vill samla in semantiska utdata från den refererade regeln, Använd det valfria `name` attribut för att ange namnet på en variabel som semantiska utdata är tilldelad.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref Element

Den `attrref` element refererar till ett indexattribut som tillåter matchning mot attributvärden observerats i indexet.  De nödvändiga `uri` attributet anger index schemanamn och attributnamnet med syntaxen ”*%{SchemaName/*#*%{attrname/*”.  Det måste finnas en föregående `import` element som importerar schemat med namnet *%{SchemaName/*.  Attributets namn är namnet på ett attribut som angetts i motsvarande schemat.

Utöver att matcha indata från användaren, den `attrref` elementet returnerar också en strukturerade frågeobjektet som utdata som väljer deluppsättning objekt i indexet matchar indatavärdet.  Använd det valfria `name` attribut för att ange namnet på variabeln där objektet frågeresultatet ska sparas.  Frågeobjektet kan sammanställas med andra frågeobjekt för att skapa mer komplexa uttryck.  Se [semantisk tolkning](SemanticInterpretation.md) mer information.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Frågan slutförs

För att stödja frågeifyllning när tolkas partiella användarfrågor, måste varje refererade attributet innehålla ”starts_with” som en åtgärd i schemadefinitionen.  Får en fråga prefix användaren `attrref` matcha alla värden i indexet som Slutför prefixet, och ge varje fullständig värde som en separat tolkning av grammatik.  

Exempel:
* Matchar `<attrref uri="academic#Keyword" name="keyword"/>` mot frågan prefixet ”dat” genererar en tolkning Paper-faktablad om ”databas”, en tolkning Paper-faktablad om ”data-utvinning” osv.
* Matchar `<attrref uri="academic#Year" name="year"/>` mot frågan prefixet ”200” genererar en tolkning papper i ”2000”, en tolkning papper ”2001”, osv.

#### <a name="matching-operations"></a>Matchande åtgärder

Förutom exakt matchning väljer du attributet typer också stöd för prefix och ojämlikhet matchar via den valfria `op` attribut.  Om inga objekt i indexet har ett värde som matchar, grammatik sökvägen blockeras och tjänsten kommer inte att generera någon tolkningar passerar över den här grammatik-sökvägen.   Den `op` attributet standardvärden ”eq”.

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

I följande tabell visas de stöds `op` värden för varje attributtyp.  Deras användning kräver index motsvarande åtgärd som ska ingå i attributet schemadefinitionen.

| Attributtyp | Op värde | Beskrivning | Indexåtgärden
|----|----|----|----|
| String | eq | Exakt matchning av sträng | lika med |
| String | starts_with | Prefix för strängmatchning | starts_with |
| Int32, Int64, Double-värde | eq |  Numeriska likhetsmatchning | lika med |
| Int32, Int64, Double-värde | lt, le, gt, ge | Numeriska ojämlikhet matchning (<, < =, >, > =) | is_between |
| Int32, Int64, Double-värde | starts_with | Prefixmatchning värde i decimalform | starts_with |

Exempel:
* `<attrref uri="academic#Year" op="lt" name="year"/>` matchar strängen ”2000” och returnerar alla rapporter som publiceras innan år 2000, exklusivt.
* `<attrref uri="academic#Year" op="lt" name="year"/>` matchar inte den inmatade strängen ”20” eftersom det finns inga dokument i indexet publiceras innan år 20.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` matchar strängen ”dat” och returnerar i en enkel tolkning Paper-faktablad om ”databas”, ”data-utvinning” osv.  Det här är ett ovanligt användningsfall.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` matchningar Indatasträngen ”20” och returnerar i en enkel tolkning handlingar som publicerats i 200 299, 2000-2999, osv.  Det här är ett ovanligt användningsfall.

### <a name="tag-element"></a>tagga Element

Den `tag` elementet anger hur en väg genom grammatik ska tolkas.  Den innehåller en sekvens med semikolon-terminerade instruktioner.  En instruktion kan vara en tilldelning av en literal eller en variabel till en annan variabel.  Det kan också tilldela en variabel utdata för en funktion med 0 eller fler parametrar.  Varje funktionsparameter kan anges med hjälp av en literal eller en variabel.  Om funktionen inte returnerar några utdata utelämnas tilldelningen.  Variabeln scope är lokala för som innehåller regeln.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Varje `rule` i grammatik har en fördefinierad variabel med namnet ”ut”, som representerar den semantiska utdatan från regeln.  Värdet beräknas genom att utvärdera var och en av semantiska uttrycken i sökvägen via det `rule` matcha användaren fråga indata.  Värdet som tilldelas till variabeln ”ut” i slutet av utvärderingen är semantiska utdata från regeln.  Semantisk utdata från tolka en användarfråga mot grammatik är semantiska utdata för rot-regeln.

Vissa instruktioner får ändra sannolikheten för en tolkning sökväg genom att introducera en additiva log sannolikheten förskjutning.  Vissa rapporter kan avvisa tolkningen funktionen helt och hållet om anges villkor inte är uppfyllda.

En lista över semantiska funktioner som stöds finns i [semantiska funktioner](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Tolkning sannolikhet

Sannolikheten för en tolkning väg genom grammatik är kumulativa log sannolikheten att alla de `<item>` element och semantisk functions påträffade längs vägen.  Det beskriver relativa sannolikheten för att matcha en viss sekvens för indata.

Får en sannolikhet *p* mellan 0 och 1 motsvarande log sannolikheten kan beräknas som log (*p*), där log() är funktionen naturliga loggen.  Om du använder log sannolikhet kan systemet att lagra den sammanslagna sannolikheten för en tolkning väg genom att lägga.  Det gör också med underskott som är gemensamma för sammanslagen sannolikhet beräkningarna.  Observera att avsiktligt log sannolikheten är alltid ett negativt värde eller 0, där större värden anger högre sannolikhet.

## <a name="example"></a>Exempel

Följande är ett exempel XML från domänen akademiska publikationer som visar de olika elementen i en grammatik:

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
