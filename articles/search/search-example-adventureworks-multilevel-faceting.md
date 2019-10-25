---
title: 'Exempel: på flera nivåer'
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar aspekt strukturer för taxonomier på flera nivåer, skapa en kapslad navigerings struktur som du kan ta med på program sidor.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792946"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Exempel: på flera nivåer i Azure Kognitiv sökning

Azure Kognitiv sökning-scheman stöder inte explicita kategoriserings kategorier med flera nivåer, men du kan uppskatta dem genom att ändra innehåll före indexeringen och sedan använda viss särskild hantering av resultaten. 

## <a name="start-with-the-data"></a>Börja med data

Exemplet i den här artikeln bygger på ett tidigare exempel, [modell The AdventureWorks Inventory Database](search-example-adventureworks-modeling.md), för att demonstrera flera nivåer i Azure kognitiv sökning.

AdventureWorks har en enkel taxonomi med två nivåer med en överordnad-underordnad relation. För en enkel SQL-koppling i den här strukturen kan du använda en enkel SQL-kopplings fråga för att gruppera taxonomin:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Frågeresultat](./media/search-example-adventureworks/prod-query-results.png "Frågeresultat")

## <a name="indexing-to-a-collection-field"></a>Indexera till ett samlings fält

I indexet som innehåller den här strukturen skapar du ett **samlings fält (EDM. String)** i Azure kognitiv sökning-schemat för att lagra dessa data, vilket säkerställer att Field-attributen är sökbara, filter bara, aspekt bara och kan hämtas.

När du har indexerat innehåll som refererar till en viss taxonomi kategori skickar du taxonomin som en matris med text från varje nivå i taxonomin. För en entitet med `ProductCategoryId = 5 (Mountain Bikes)`skickar du till exempel fältet som `[ "Bikes", "Bikes|Mountain Bikes"]`

Observera att den överordnade kategorin "cyklar" ingår i den underordnade kategorins värde "Mountain Bikes". Varje under kategori ska bädda in hela sökvägen i förhållande till rot elementet. Pipe Character-avgränsaren är godtycklig, men måste vara konsekvent och ska inte visas i käll texten. Avgränsnings tecknet används i program koden för att återskapa taxonomi trädet från fasett-resultat.

## <a name="construct-the-query"></a>Konstruera frågan

Vid utfärdande av frågor inkluderar du följande aspekt specifikation (där taxonomi är ditt fasettable-fält för taxonomi): `facet = taxonomy,count:50,sort:value`

Count-värdet måste vara tillräckligt högt för att returnera alla möjliga taxonomi värden. AdventureWorks-data innehåller 41 distinkta värden för taxonomi, så `count:50` räcker.

  ![Fasett-filter](./media/search-example-adventureworks/facet-filter.png "Fasett-filter")

## <a name="build-the-structure-in-client-code"></a>Bygg strukturen i klient koden

I klient program koden konstruerar du om taxonomi trädet genom att dela varje fasett-värde på pipe-tecknet.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

Objektet **Kategorier** kan nu användas för att återge ett komprimerbart träd med korrekta antal:

  ![fasettt filter med flera nivåer](./media/search-example-adventureworks/multi-level-facet.png "fasettt filter med flera nivåer")

 
Varje länk i trädet bör använda det relaterade filtret. Exempel:

+ **taxonomi/alla** `(x:x eq 'Accessories')` returnerar alla dokument i tillbehörs grenen
+ **taxonomi/** `(x:x eq 'Accessories|Bike Racks')` returnerar bara dokumenten med en under kategori av cykel rack under tillbehörs grenen.

Den här tekniken kommer att skalas för att avse mer komplexa scenarier som djupare taxonomi träd och duplicerade under kategorier som sker under olika överordnade kategorier (till exempel `Bike Components|Forks` och `Camping Equipment|Forks`).

> [!TIP]
> Frågans hastighet påverkas av antalet ansikte som returneras. Om du vill ha stöd för mycket stora taxonomi uppsättningar kan du lägga till ett aspekt bara **EDM. String** -fält för att lagra det översta taxonomi-värdet för varje dokument. Använd sedan samma metod ovan, men utför bara insamlings aspekt frågan (filtrerat i fältet rot taxonomi) när användaren utökar en nod på den översta nivån. Eller, om 100% återkallande inte krävs, minska antalet fasetter till ett rimligt tal och se till att aspekt posterna sorteras efter antal.

## <a name="see-also"></a>Se också

[Exempel: utforma AdventureWorks Inventory Database för Azure Kognitiv sökning](search-example-adventureworks-modeling.md)