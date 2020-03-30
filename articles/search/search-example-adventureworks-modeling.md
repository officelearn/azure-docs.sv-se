---
title: 'Exempel: Modellera AdventureWorks Inventory-databasen'
titleSuffix: Azure Cognitive Search
description: Lär dig hur du modellerar relationsdata, omvandlar dem till en förenklad datauppsättning, för indexering och fulltextsökning i Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793000"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Exempel: Modellera AdventureWorks Inventory-databasen för Azure Cognitive Search

Azure Cognitive Search accepterar en förenklad raduppsättning som indata till [indexeringspipelinen (datainmatning).](search-what-is-an-index.md) Om källdata kommer från en SQL Server-relationsdatabas demonstrerar den här artikeln en metod för att skapa en förenklad raduppsättning före indexering, med hjälp av Exempeldatabasen AdventureWorks som exempel.

## <a name="about-adventureworks"></a>Om AdventureWorks

Om du har en SQL Server-instans kan du känna till [exempeldatabasen AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Bland de tabeller som ingår i den här databasen finns fem tabeller som exponerar produktinformation.

+ **ProductModel**: namn
+ **Produkt:** namn, färg, kostnad, storlek, vikt, bild, kategori (varje rad går till en viss ProductModel)
+ **ProductDescription**: beskrivning
+ **ProductModelProductDescription**: språk (varje rad ansluter en ProductModel till en specifik ProductDescription för ett visst språk)
+ **ProductCategory**: namn, överordnad kategori

Att kombinera alla dessa data till en tillplattad raduppsättning som kan intas i ett sökindex är målet för det här exemplet. 

## <a name="considering-our-options"></a>Med tanke på våra alternativ

Den naiva metoden skulle vara att indexera alla rader från tabellen Produkt (sammanfogad i förekommande fall) eftersom tabellen Produkt har den mest specifika informationen. Detta tillvägagångssätt skulle dock utsätta sökindex för upplevda dubbletter i en resultatuppsättning. Road-650-modellen finns till exempel i två färger och sex storlekar. En fråga om "landsvägscyklar" skulle då domineras av tolv instanser av samma modell, differentierade endast efter storlek och färg. De andra sex vägspecifika modellerna skulle alla förpassas till den nedre sökvärlden: sidan två.

  ![Lista över produkter](./media/search-example-adventureworks/products-list.png "Lista över produkter")
 
Observera att Road-650-modellen har tolv alternativ. 1:1:a entitetsrader representeras bäst som fält med flera värden eller föraggregerade värdefält i sökindexet.

Att lösa det här problemet är inte så enkelt som att flytta målindexet till tabellen ProductModel. Om du gör det ignoreras viktiga data i tabellen Produkt som fortfarande ska representeras i sökresultaten.

## <a name="use-a-collection-data-type"></a>Använda en datatyp för insamling

Den "rätta metoden" är att använda en sökschema funktion som inte har en direkt parallell i databasmodellen: **Collection(Edm.String)**. Den här konstruktionen definieras i Indexschemat för Azure Cognitive Search. En insamlingsdatatyp används när du behöver representera en lista över enskilda strängar i stället för en mycket lång (enkel) sträng. Om du har taggar eller nyckelord använder du en insamlingsdatatyp för det här fältet.

Genom att definiera indexfält med flera värden i **Collection(Edm.String)** för "färg", "storlek" och "bild" behålls den underordnade informationen för att skapa och filtrera utan att index förorenas med dubblettposter. På samma sätt, tillämpa mängdfunktioner på numeriska produktfält, indexering **minListPrice** i stället för varje enskild **produktlistaPrice**.

Med tanke på ett index med dessa strukturer, en sökning efter "mountainbikes" skulle visa diskreta cykelmodeller, samtidigt som viktiga metadata som färg, storlek och lägsta pris. Följande skärmbild ger en illustration.

  ![Exempel på sökning på mountainbike](./media/search-example-adventureworks/mountain-bikes-visual.png "Exempel på sökning på mountainbike")

## <a name="use-script-for-data-manipulation"></a>Använda skript för datamanipulering

Tyvärr kan denna typ av modellering inte lätt uppnås genom SQL-satser ensam. Använd i stället ett enkelt NodeJS-skript för att läsa in data och sedan mappa det till sökvänliga JSON-entiteter.

Den slutliga mappningen för databassökning ser ut så här:

+ modell (Edm.String: sökbar, filterbar, hämtningsbar) från "ProductModel.Name"
+ description_en (Edm.String: sökbar) från "ProductDescription" för modellen där culture='en'
+ färg (Collection(Edm.String): sökbar, filterbar, ansiktstabell, hämtningsbar): unika värden från "Product.Color" för modellen
+ storlek (Collection(Edm.String): sökbar, filterbar, ansiktstablering, hämtningsbar): unika värden från "Product.Size" för modellen
+ bild (Collection(Edm.String): hämtningsbar): unika värden från "Product.ThumbnailPhoto" för modellen
+ minStandardCost (Edm.Double: filterbar, facetable, sorterbar, hämtningsbar): sammanlagd minimum av alla "Product.StandardCost" för modellen
+ minListPrice (Edm.Double: filterbar, facetable, sorterbar, hämtningsbar): sammanlagd minimum av alla "Product.ListPrice" för modellen
+ minVikt (Edm.Double: filterbar, facetable, sorterbar, hämtningsbar): sammanlagd minimum av alla "Product.Weight" för modellen
+ produkter (Collection(Edm.String): sökbara, filterbara, hämtningsbara): unika värden från "Product.Name" för modellen

När du har anslutit tabellen ProductModel med Product och ProductDescription använder du [lodash](https://lodash.com/) (eller Linq i C#) för att snabbt omvandla resultatuppsättningen:

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

Den resulterande JSON ser ut så här:

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

Slutligen, här är SQL-frågan för att returnera den första postmängden. Jag använde [mssql](https://www.npmjs.com/package/mssql) npm-modulen för att läsa in data i min NodeJS-app.

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
> [Exempel: Faktjonomier på flera nivåer i Azure Cognitive Search](search-example-adventureworks-multilevel-faceting.md)