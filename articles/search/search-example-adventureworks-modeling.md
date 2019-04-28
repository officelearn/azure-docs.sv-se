---
title: 'Exempel: Utforma AdventureWorks inventering-databas – Azure Search'
description: Lär dig hur du modeller med relationsdata, omvandla det till en Flat datamängd, för indexering och Fullständig textsökning i Azure Search.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291916"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Exempel: Utforma AdventureWorks inventering databasen för Azure Search

Modellering strukturerade databasen innehåll till en effektiv search-index är sällan en enkel övning. Schemaläggning och ändringshantering tagits ur bruk, det finns den stora utmaningen med avnormalisera källraderna från deras tillstånd för anslutna till en tabell till search-vänlig entiteter. Den här artikeln använder exempeldata AdventureWorks, som du tillgängliga online, för att markera vanliga upplevelser i övergången från databasen för att söka. 

## <a name="about-adventureworks"></a>Om AdventureWorks

Om du har en SQL Server-instans kan kanske du är bekant med exempeldatabasen AdventureWorks. Bland de tabeller som ingår i den här databasen är fem tabeller som exponerar produktinformation.

+ **ProductModel**: namn
+ **Produkten**: namn, färg, kostnad, storlek, vikt, bild, kategori (varje rad ansluter till en specifik ProductModel)
+ **ProductDescription**: beskrivning
+ **ProductModelProductDescription**: nationella inställningar (varje rad kopplar en ProductModel till en specifik ProductDescription för ett specifikt språk)
+ **ProductCategory**: namn, överordnad kategori

Kombinera alla dessa data till en Flat raduppsättning som kan hämtas till ett search-index är den aktuella uppgiften. 

## <a name="considering-our-options"></a>Funderar på att våra alternativ

Naïve metod är att indexera alla rader från Product-tabellen (ansluten där det behövs) sedan Product-tabellen har informationen som mest. Denna metod skulle dock exponera sökindex som upplevd dubbletter i en resultatuppsättning. Till exempel är väg 650 modellen tillgänglig i två färger och sex storlekar. En fråga för ”vägen cyklar” skulle sedan domineras av tolv instanser av samma modell, differentierade bara av storlek och färg. De andra sex väg-specifika modellerna skulle alla vara delegerats till nether världen av search: sidan två.

  ![Lista över produkter](./media/search-example-adventureworks/products-list.png "produktlista")
 
Observera att vägen 650 modellen har tolv alternativ. Entitet i en-till-många rader visas bäst som flervärdesfält eller plap aggregated värdet fält i sökindexet.

Hur du löser det här problemet är inte lika enkelt som att flytta målindex till tabellen ProductModel. Detta skulle Ignorera viktiga data i Product-tabellen som bör fortfarande visas i sökresultaten.

## <a name="use-a-collection-data-type"></a>Använd datatypen samling

Den ”rätta metoden” är att använda en sökschema funktion som inte har en direkt parallell i databasmodellen: **Collection(Edm.String)**. En samlingsdatatyp används när du har en lista med enskilda strängar, snarare än en lång sträng (enkelt). Om du har taggar eller nyckelord, använder du en samlingsdatatyp för det här fältet.

Genom att definiera flera värden index-fält av **Collection(Edm.String)** för ”färg”, ”storlek” och ”bild” extra information som är sparade för fasettering och filtrering utan förorenande index med dubbla poster. På samma sätt gäller mängdfunktioner för de numeriska fält för produkten kan indexera **minListPrice** i stället för varje enskild produkt **listPrice**.

Med ett index med dessa strukturer kan visas en sökning efter ”mountain cyklar” diskreta cykel modeller och behålla viktiga metadata som färg, storlek och lägst pris. Följande skärmbild innehåller en bild.

  ![Mountain cykel search exempel](./media/search-example-adventureworks/mountain-bikes-visual.png "Mountain cykel search-exempel")

## <a name="use-script-for-data-manipulation"></a>Använd skript för datamanipulering

Tyvärr uppnås inte den här typen av modellering enkelt via fristående SQL-uttryck. Använd istället ett enkelt NodeJS-skript för att läsa in data och mappa den till search-vänlig JSON entiteter.

Sista databassökning mappningen ser ut så här:

+ model (Edm.String: searchable, filterable, retrievable) from "ProductModel.Name"
+ description_en (Edm.String: sökbar) från ”ProductDescription” för modellen där kultur = ”en”
+ färg (Collection(Edm.String): sökbar, filtrerbar, fasettbar, hämtningsbara): unika värden från ”Product.Color” för modellen
+ storlek (Collection(Edm.String): sökbar, filtrerbar, fasettbar, hämtningsbara): unika värden från ”Product.Size” för modellen
+ bild (Collection(Edm.String): hämtningsbar): unika värden från ”Product.ThumbnailPhoto” för modellen
+ minStandardCost (Edm.Double: filtrerbar, fasettbar, sorterbar, hämtningsbara): sammanställd minst alla ”Product.StandardCost” för modellen
+ minListPrice (Edm.Double: filtrerbar, fasettbar, sorterbar, hämtningsbara): sammanställd minst alla ”Product.ListPrice” för modellen
+ minWeight (Edm.Double: filtrerbar, fasettbar, sorterbar, hämtningsbara): sammanställd minst alla ”Product.Weight” för modellen
+ produkter (Collection(Edm.String): sökbar, filtrerbar, hämtningsbara): unika värden från ”Product.Name” för modellen

När du ansluter till tabellen ProductModel med produkt och ProductDescription, Använd [lodash](https://lodash.com/) (eller Linq i C#) att snabbt omvandla resultatuppsättningen:

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

Här är slutligen SQL-frågan ska returnera inledande postuppsättning. Jag använde den [mssql](https://www.npmjs.com/package/mssql) npm-modulen för att läsa in data till min NodeJS-app.

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
> [Exempel: Med flera nivåer aspekten taxonomier i Azure Search](search-example-adventureworks-multilevel-faceting.md)


