---
title: 'Exempel: Fasor på flera nivåer'
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar fasstrukturer för taxonomier på flera nivåer, vilket skapar en kapslad navigeringsstruktur som du kan inkludera på programsidor.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792946"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Exempel: Fasor på flera nivåer i Azure Cognitive Search

Azure Cognitive Search-scheman stöder inte uttryckligen taxonomikategorier på flera nivåer, men du kan approximera dem genom att ändra innehåll före indexering och sedan tillämpa viss särskild hantering på resultaten. 

## <a name="start-with-the-data"></a>Börja med data

Exemplet i den här artikeln bygger på ett tidigare exempel, [Modell adventureworks inventory-databasen,](search-example-adventureworks-modeling.md)för att demonstrera fasning på flera nivåer i Azure Cognitive Search.

AdventureWorks har en enkel två-nivå taxonomi med en förälder-barn relation. För taxonomidjup med fast längd i den här strukturen kan en enkel SQL-kopplingsfråga användas för att gruppera taxonomin:

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

## <a name="indexing-to-a-collection-field"></a>Indexera till ett samlingsfält

I indexet som innehåller den här strukturen skapar du ett **insamlingsfält(Edm.String)** i Azure Cognitive Search-schemat för att lagra dessa data och se till att fältattributen innehåller sökbara, filterbara, ansiktstablering och hämtningsbara.

När du nu indexerar innehåll som refererar till en viss taxonomikategori skickar du nu taxonomin som en matris som innehåller text från varje nivå i taxonomin. För en entitet `ProductCategoryId = 5 (Mountain Bikes)`med skickar du till exempel fältet som`[ "Bikes", "Bikes|Mountain Bikes"]`

Observera att föräldrakategorin "Cyklar" ingår i barnkategorivärdet "Mountainbikes". Varje underkategori ska bädda in hela sökvägen i förhållande till rotelementet. Pipe-teckenavgränsaren är godtycklig, men den måste vara konsekvent och bör inte visas i källtexten. Avgränsningstecknet används i programkoden för att rekonstruera taxonominträdet från fasettresultat.

## <a name="construct-the-query"></a>Konstruera frågan

När du utfärdar frågor, inkludera följande aspektspecifikation (där taxonomi är ditt facetable taxonomifält):`facet = taxonomy,count:50,sort:value`

Räknevärdet måste vara tillräckligt högt för att returnera alla möjliga taxonomivärden. AdventureWorks-data innehåller 41 olika taxonomivärden, så `count:50` det räcker.

  ![Fasterat filter](./media/search-example-adventureworks/facet-filter.png "Fasterat filter")

## <a name="build-the-structure-in-client-code"></a>Skapa strukturen i klientkod

Rekonstruera taxonominträdet i klientprogramkoden genom att dela upp varje nominellt värde på pipe-tecknet.

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

**Kategoriobjektet** kan nu användas för att återge ett hopfällbart taxonomiträd med korrekta antal:

  ![fasat filter på flera nivåer](./media/search-example-adventureworks/multi-level-facet.png "fasat filter på flera nivåer")

 
Varje länk i trädet ska använda det relaterade filtret. Ett exempel:

+ **taxonomi/alla** `(x:x eq 'Accessories')` returnerar alla dokument i grenen Tillbehör
+ **taxonomi/eventuella** `(x:x eq 'Accessories|Bike Racks')` returnerar endast dokumenten med en underkategori av cykelställ under tillbehörsgrenen.

Den här tekniken skalas för att täcka mer komplexa scenarier som djupare taxonomiträd och `Bike Components|Forks` `Camping Equipment|Forks`duplicerade underkategorier som förekommer under olika överordnade kategorier (till exempel och ).

> [!TIP]
> Frågehastigheten påverkas av antalet aspekter som returneras. Om du vill stödja mycket stora taxonomiuppsättningar bör du överväga att lägga till ett **edm.String-fält** med facetable för att hålla taxonominvärdet på den översta nivån för varje dokument. Använd sedan samma teknik ovan, men utför bara insamlingsast-aspektfrågan (filtrerad i rottaxonomifältet) när användaren expanderar en nod på den översta nivån. Eller, om 100% återkallande inte krävs, helt enkelt minska aspekten räkna till ett rimligt antal, och se till att aspektposterna sorteras efter räkna.

## <a name="see-also"></a>Se även

[Exempel: Modellera AdventureWorks Inventory-databasen för Azure Cognitive Search](search-example-adventureworks-modeling.md)