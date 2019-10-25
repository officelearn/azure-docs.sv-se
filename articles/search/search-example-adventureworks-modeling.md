---
title: 'Exempel: utforma AdventureWorks Inventory Database'
titleSuffix: Azure Cognitive Search
description: Lär dig att modellera Relations data, omvandla dem till en utplattad data uppsättning för indexering och fullständig texts ökning i Azure Kognitiv sökning.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793000"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Exempel: utforma AdventureWorks Inventory Database för Azure Kognitiv sökning

Azure Kognitiv sökning accepterar en utplattad rad uppsättning som indata till [pipeline för indexering (data inmatning)](search-what-is-an-index.md). Om dina källdata härstammar från en SQL Server Relations databas, visar den här artikeln en metod för att skapa en utplattad rad uppsättning före indexering, med hjälp av AdventureWorks-exempel databasen som exempel.

## <a name="about-adventureworks"></a>Om AdventureWorks

Om du har en SQL Server-instans kan du bekanta dig med [AdventureWorks-exempel databasen](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Bland tabellerna som ingår i databasen finns fem tabeller som visar produkt information.

+ **ProductModel**: namn
+ **Produkt**: namn, färg, kostnad, storlek, vikt, bild, kategori (varje rad kopplas till en viss ProductModel)
+ **ProductDescription**: Beskrivning
+ **ProductModelProductDescription**: locale (varje rad kopplar en ProductModel till en specifik ProductDescription för ett speciellt språk)
+ **ProductCategory**: namn, överordnad kategori

Syftet med det här exemplet är att kombinera alla dessa data till en utplattad rad uppsättning som kan matas in i ett Sök index. 

## <a name="considering-our-options"></a>Överväg våra alternativ

Naïve-metoden skulle vara att indexera alla rader från produkt tabellen (där det är lämpligt) sedan produkt tabellen har den mest detaljerade informationen. Den metoden skulle dock innebära att Sök indexet uppfattar dubbletter i en resultat uppsättning. Till exempel är Road-650-modellen tillgänglig i två färger och sex storlekar. En fråga för "Road Bikes" kommer sedan att domineratss av tolv instanser av samma modell, differentierad enbart efter storlek och färg. De andra sex vägbaserade modellerna skulle alltid relegated till Nether-världen för Sök: sida två.

  ![Produkt lista](./media/search-example-adventureworks/products-list.png "Produkt lista")
 
Observera att Road-650-modellen har tolv alternativ. En-till-många-enhets rader visas bäst som flervärdesfält eller föraggregerade-värde fält i Sök indexet.

Att lösa det här problemet är inte lika enkelt som att flytta mål indexet till ProductModel-tabellen. Detta skulle ignorera viktiga data i produkt tabellen som fortfarande ska visas i Sök resultaten.

## <a name="use-a-collection-data-type"></a>Använd en samlings data typ

"Rätt metod" är att använda en Sök schema funktion som inte har en direkt parallell i databas modellen: **Collection (EDM. String)** . Den här konstruktionen definieras i Azure Kognitiv sökning index-schemat. En samlings data typ används när du behöver representera en lista med enskilda strängar, i stället för en mycket lång (enkel) sträng. Om du har taggar eller nyckelord använder du en samlings data typ för det här fältet.

Genom att definiera flera värde index fält för **samling (EDM. String)** för "Color", "size" och "image", bevaras hjälp informationen för fasettering och filtrering utan att det förorenar indexet med dubbla poster. På samma sätt använder du mängd funktioner för de numeriska produkt fälten och indexerar **minListPrice** i stället för varje enskild produkt **listPrice**.

Med tanke på ett index med dessa strukturer skulle en sökning efter "Mountain Bikes" Visa modeller för diskreta cyklar, samtidigt som viktiga metadata som färg, storlek och lägsta pris betjänas. Följande skärm bild visar en illustration.

  ![Exempel på Mountain Bike search](./media/search-example-adventureworks/mountain-bikes-visual.png "Exempel på Mountain Bike search")

## <a name="use-script-for-data-manipulation"></a>Använd skript för data behandling

Den här typen av modellering kan tyvärr inte enkelt uppnås genom SQL-uttryck. Använd i stället ett enkelt NodeJS-skript för att läsa in data och sedan mappa dem till sökvänliga JSON-entiteter.

Den slutgiltiga databasen – Sök mappningen ser ut så här:

+ modell (EDM. String: sökbar, Filterable, hämtnings bar) från "ProductModel.Name"
+ description_en (EDM. String: sökbar) från "ProductDescription" för modellen där Culture = "en"
+ färg (samling (EDM. String): sökbar, Filterable, aspekt bar, hämtnings bar): unika värden från "Product. Color" för modellen
+ storlek (samling (EDM. String): sökbar, Filterable, aspekt bar, hämtnings bar): unika värden från "Product. size" för modellen
+ bild (samling (EDM. String): hämtnings bar): unika värden från "Product. ThumbnailPhoto" för modellen
+ minStandardCost (EDM. Double: Filterable, aspekt bar, sorterbar, hämtnings bar): sammanlagt minst alla "Product. StandardCost" för modellen
+ minListPrice (EDM. Double: Filterable, aspekt bar, sorterbar, hämtnings bar): sammanlagt minst alla "Product. ListPrice" för modellen
+ minWeight (EDM. Double: Filterable, aspekt bar, sorterbar, hämtnings bar): sammanlagt minimum för all produkt. vikt för modellen
+ produkter (samling (EDM. String): sökbart, Filterable, hämtnings bar): unika värden från "Product.Name" för modellen

När du har gått med i ProductModel-tabellen med produkten och ProductDescription, använder du [lodash](https://lodash.com/) ( C#eller LINQ in) för att snabbt transformera resultat uppsättningen:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Den resulterande JSON-filen ser ut så här:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Slutligen är det SQL-frågan för att returnera den första post uppsättningen. Jag använde modulen [MSSQL](https://www.npmjs.com/package/mssql) NPM för att läsa in data i min NodeJS-app.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Exempel: Face-taxonomi på flera nivåer i Azure Kognitiv sökning](search-example-adventureworks-multilevel-faceting.md)