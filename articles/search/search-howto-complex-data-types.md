---
title: Hur du modellera komplexa datatyper i Azure Search | Microsoft Docs
description: Kapslade eller hierarkisk datastrukturer kan utformas i ett Azure Search-index med Flat raduppsättning och samlingar datatyp.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 81298bedd43a89ea948753dffc5f80248f5429ca
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Så här modellen komplexa datatyper i Azure Search
Externa datauppsättningar som används för att fylla ett Azure Search index ibland inkluderar hierarkisk eller kapslade underordnade strukturer som inte nedbrytning av snyggt i en tabellvy raduppsättning. Exempel på sådana strukturer kan innehålla flera platser och telefonnummer för en kund, flera färger och storlekar för en enskild SKU flera författare av en enda bok och så vidare. Modeling villkor du kan se dessa strukturer kallas *komplexa datatyper*, *sammansatta datatyper*, *sammansatta datatyper*, eller *aggregera datatyper*, t.ex.

Komplexa datatyper stöds inte internt i Azure Search, men en beprövad lösning innehåller två steg för att förenkla strukturen och sedan använda en **samling** datatyp till att fylla inre strukturen. Efter den teknik som beskrivs i den här artikeln kan innehållet som ska genomsökas fasetterad, filtreras och sorteras.

## <a name="example-of-a-complex-data-structure"></a>Exempel på en komplex struktur
Data i fråga finns vanligen som en uppsättning JSON eller XML-dokument eller objekt i en NoSQL-arkivet, t.ex Azure Cosmos DB. Utmaningen beror strukturellt, från att ha flera underordnade objekt som ska sökas igenom och filtreras.  Vidta följande JSON-dokument som innehåller en uppsättning kontakter som ett exempel som en startpunkt för att illustrera lösningen:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Medan fält med namnet ”id” 'name' och 'företag' kan enkelt mappas 1 som fält i ett Azure Search-index, fältet 'platser' innehåller en matris med-platser med både en uppsättning plats-ID: N, samt beskrivningar av platsen. Med hänsyn till att Azure Search inte har en datatyp som stöder detta, behöver vi ett annat sätt att modellera detta i Azure Search. 

> [!NOTE]
> Den här tekniken beskrivs också av Kirk Evans i ett blogginlägg [indexering Azure Cosmos DB med Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), vilket visar att en teknik som kallas ”förenkla data”, där du vill ha ett fält med namnet `locationsID` och `locationsDescription` som är både [samlingar](https://msdn.microsoft.com/library/azure/dn798938.aspx) (eller en matris med strängar).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Del 1: Förenkla matrisen till enskilda fält
Skapa ett Azure Search-index som hanterar den här datauppsättningen kan skapa enskilda fält för kapslade underordnade: `locationsID` och `locationsDescription` med datatypen [samlingar](https://msdn.microsoft.com/library/azure/dn798938.aspx) (eller en matris med strängar). Index skulle värdet '1' och '2' fälten i den `locationsID` fältet för John Smith och värdena ”3” & ”4” i den `locationsID` för Jen Campbell.  

Dina data i Azure Search skulle se ut så här: 

![exempeldata, 2 rader](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Del 2: Lägga till en samling fält i indexdefinitionen
I index-schemat fältdefinitioner kan se ut ungefär så här.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validera Sök beteenden och eventuellt utökar indexet
Under förutsättning att du har skapat indexet och läsa in data, kan du nu Testa lösningen för att verifiera Sök Frågekörningen mot dataset. Varje **samling** fältet måste innehålla **sökbara**, **filtrera** och **facetable**. Du ska kunna köra frågor som:

* Hitta alla personer som arbetar på Adventureworks huvudkontor.
* Hämta en uppräkning av antalet personer som arbetar i en Home Office.  
* Visa vilka andra kontoren de fungerar tillsammans med ett antal människor på varje plats för de personer som arbetar på en Home Office.  

När den här tekniken infaller isär är när du behöver göra en sökning som kombinerar både plats-id samt Platsbeskrivning. Exempel:

* Hitta alla personer som de har ett hemkontor och har en plats-ID på 4.  

Om du kommer ihåg det ursprungliga innehållet såg ut så här:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Nu när vi har avgränsat data i separata fält, vi har dock inget sätt att om du vet om Home Office för Jen Campbell avser `locationsID 3` eller `locationsID 4`.  

Ange ett annat fält i indexet som kombinerar alla data till en enda samling för att hantera det här fallet.  I vårt exempel kommer vi kallar det här fältet `locationsCombined` och vi kommer att separeras innehåll med en `||` men du kan välja alla avgränsare som du anser skulle vara en unik uppsättning tecken för ditt innehåll. Exempel: 

![exempeldata, 2 rader med avgränsare](./media/search-howto-complex-data-types/sample-data-2.png)

Med den här `locationsCombined` fältet vi kan nu hantera ännu fler frågor som:

* Visa antalet personer som arbetar på en 'hemkontor' med plats-Id för '4'.  
* Sök efter personer som arbetar på en Home Office med plats-Id '4'. 

## <a name="limitations"></a>Begränsningar
Den här metoden är användbar för ett antal scenarier, men den kan inte användas i samtliga fall.  Exempel:

1. Om du inte har en statisk uppsättning fält i en komplex datatyp och det gick inte att mappa alla möjliga typer till ett enda fält. 
2. Uppdatering av kapslade objekt kräver lite extraarbete veta exakt vad behöver uppdateras i Azure Search index

## <a name="sample-code"></a>Exempelkod
Du kan se ett exempel på hur du indexera en komplex JSON-datauppsättning i Azure Search och genomföra ett antal frågor via den här datauppsättningen på den här [GitHub-repo-](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Nästa steg
[Rösta på inbyggt stöd för komplexa datatyper](https://feedback.azure.com/forums/263029-azure-search) på Azure Search UserVoice sidan och ange några ytterligare indata som du vill att vi ska tänka på avseende funktionen implementering. Du kan också nå ut till mig direkt på Twitter på @liamca.

