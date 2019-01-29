---
title: 'Exempel: Fasetter på flera – Azure Search'
description: Lär dig hur du skapar fasettering strukturer för flera nivåer taxonomier, skapa en kapslad navigeringsstruktur som du kan inkludera på sidor i webbprogram.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 7fa17528931be40109d81edac0f15a6a6822ec01
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194831"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Exempel: Fasetter på flera nivåer i Azure Search

Azure Search-scheman stöder inte uttryckligen med flera nivåer taxonomikategorier, men du kan göra en uppskattning av dem genom att ändra innehållet innan indexering och sedan tillämpa vissa särskild hantering på resultaten. 

## <a name="start-with-the-data"></a>Börja med data

I exemplet i den här artikeln bygger vidare på föregående exempel [modellera AdventureWorks inventering databasen](search-example-adventureworks-modeling.md), för att demonstrera flernivåfasett i Azure Search.

AdventureWorks har en enkel taxonomi två nivåer med en överordnad-underordnad-relation. För fast längd taxonomi djup på den här strukturen, kan en enkel anslutning till SQL-fråga användas för att gruppera taxonomin:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Frågeresultat](./media/search-example-adventureworks/prod-query-results.png "frågeresultat")

## <a name="indexing-to-a-collection-field"></a>Indexering i en samling fält

I indexet som innehåller den här strukturen, skapa en **Collection(Edm.String)** i Azure Search-schemat för att lagra den här informationen och kontrollerar att fältattribut inkluderar sökbar, filtrerbar, fasettbar hämtningsbart strängfält.

Nu, vid indexering av innehåll som refererar till en viss taxonomi kategori, skicka taxonomin som en matris som innehåller text från varje nivå i taxonomin. Till exempel för en entitet med `ProductCategoryId = 5 (Mountain Bikes)`, skicka fältet som `[ "Bikes", "Bikes|Mountain Bikes"]`

Observera inkludering av överordnad kategori ”cyklar” i underordnade kategori värdet ”Mountain cyklar”. Varje underkategori ska bädda in hela sökvägen i förhållande till rotelementet. Teckenavskiljaren pipe är valfri, men det måste stämma överens och ska inte visas i texten källa. Avgränsningstecken används i programkod för att rekonstruera trädet taxonomi aspekten resultaten.

## <a name="construct-the-query"></a>Konstruera frågan

Vid utfärdandet av frågor, är följande aspekten specifikation (där taxonomi är din fasettbar taxonomifält): `facet = taxonomy,count:50,sort:value`

Värdet för antal måste vara tillräckligt högt för att returnera alla möjliga taxonomi värden. AdventureWorks data innehåller värden som 41 distinkta taxonomi, så `count:50` räcker.

  ![Aspektbaserad filter](./media/search-example-adventureworks/facet-filter.png "Aspektbaserad filter")

## <a name="build-the-structure-in-client-code"></a>Skapa strukturen i klientkod

Rekonstruera trädet taxonomifält i programkoden klienten genom att dela upp varje aspektvärdet på det pipe-tecknet.

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

Den **kategorier** objekt kan nu användas för att återge ett komprimerbart taxonomi träd med korrekt antal:

  ![flera nivåer aspektbaserad filter](./media/search-example-adventureworks/multi-level-facet.png "flera nivåer aspektbaserad filter")

 
Varje länk i trädet bör filtret relaterade. Exempel:

+ **taxonomi/any** `(x:x eq 'Accessories')` returnerar alla dokument i grenen tillbehör
+ **taxonomi/any** `(x:x eq 'Accessories|Bike Racks')` returnerar endast dokument med en underkategori av cykel rack under grenen tillbehör.

Den här tekniken skalas så att den täcker mer komplicerade scenarier som djupare taxonomi träd och duplicerade underkategorier som uppstår under olika överordnade kategorier (till exempel `Bike Components|Forks` och `Camping Equipment|Forks`).

> [!TIP]
> Fråga hastighet påverkas av antalet fasetterna som returneras. Överväg att lägga till en fasettbar för att hantera mycket stora taxonomi uppsättningar **Edm.String** fält för översta taxonomi värdet för varje dokument. Tillämpa samma metod som ovan, men endast utföra samling fasettera frågan (filtrerade på taxonomifält rot) när du expanderar en översta noden. Eller om 100% återkallande inte krävs bara minska antalet aspekten till ett rimligt antal och se till att aspekten posterna sorteras efter antal.

## <a name="see-also"></a>Se också

[Exempel: Utforma AdventureWorks inventering databasen för Azure Search](search-example-adventureworks-modeling.md)