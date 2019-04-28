---
title: Hur komplexa datatyper – Azure Search
description: Kapslade eller hierarkisk datastrukturer kan utformas i ett Azure Search-index med hjälp av Flat raduppsättning och datatyp för samlingar.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.custom: seodec2018
ms.openlocfilehash: 973623d6c4cb57518af2012bccf67c969146d23c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076216"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Hur komplexa datatyper i Azure Search
Externa datauppsättningar som används för att fylla ett Azure Search-index ibland omfattar hierarkisk eller kapslad underordnade strukturer som inte bryter snyggt i en tabular raduppsättning. Exempel på sådana strukturer kan inkludera flera platser och telefonnummer för en enda kund, flera färger och storlekar för en enda SKU flera författare till en enda bok, och så vidare. Modellering villkor, du kan se dessa strukturer kallas *komplexa datatyper*, *sammansatt datatyper*, *sammansatta datatyper*, eller *sammanställd datatyper*, för att nämna några.

Komplexa datatyper stöds inte internt i Azure Search, men en beprövade lösning innehåller en tvåstegsprocessen för att förenkla strukturen och sedan använda en **samling** datatyp till att fylla inre strukturen. Efter den teknik som beskrivs i den här artikeln kan innehållet som ska genomsökas aspektbaserad, filtreras och sorteras.

## <a name="example-of-a-complex-data-structure"></a>Exempel på en komplex datastruktur
Data i fråga finns vanligen som en uppsättning JSON eller XML-dokument eller objekt i ett NoSQL-lager, till exempel Azure Cosmos DB. Strukturellt, utmaningen har att göra med att ha flera underordnade objekt som behöver för att söka efter och filtreras.  Vidta följande JSON-dokument som innehåller en uppsättning kontakter som ett exempel som en startpunkt för att illustrera lösningen:

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

Fält med namnet ”id”, ”name” och ”företagets” kan enkelt mappas 1: 1 som fält i ett Azure Search-index, ”platser” fältet innehåller en matris med platser, med både en uppsättning med plats-ID: N, samt beskrivningar av platsen. Tanke på att Azure Search inte har en datatyp som stöder detta, måste vi ett annat sätt att modellera detta i Azure Search. 

> [!NOTE]
> Den här metoden beskrivs också av Kirk Evans i ett blogginlägg [indexera Azure Cosmos DB med Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), som visar en teknik som kallas ”förenkla data”, där du skulle ha ett fält med namnet `locationsID` och `locationsDescription` som är både [samlingar](https://msdn.microsoft.com/library/azure/dn798938.aspx) (eller en matris med strängar).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Del 1: Förenkla matrisen i enskilda fält
Skapa ett Azure Search-index som kan hantera den här datauppsättningen kan skapa enskilda fält för den kapslade underordnade: `locationsID` och `locationsDescription` med datatypen [samlingar](https://msdn.microsoft.com/library/azure/dn798938.aspx) (eller en matris med strängar). I de här fälten skulle indexeras värdena ”1” och ”2” till den `locationsID` datafältet för John Smith och värdena ”3” och ”4” i den `locationsID` för Jen Campbell.  

Dina data i Azure Search skulle se ut så här: 

![exempeldata, 2 rader](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Del 2: Lägg till en samling fält i indexdefinitionen
I indexschemat, fältdefinitioner se ut som i det här exemplet.

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

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validera sökbeteenden och eventuellt utökar indexet
Anta att du har skapat indexet och läsa in data och nu kan du testa lösningen för att verifiera search frågekörning mot datauppsättningen. Varje **samling** fältet ska vara **sökbara**, **filtrerbara** och **fasettbar**. Du ska kunna köra frågor som:

* Hitta alla personer som arbetar på Adventureworks huvudkontoret.
* Få en uppräkning av antalet personer som arbetar i en Home Office.  
* Visa vilka andra kontoren de fungerar tillsammans med antalet personer på varje plats för de personer som arbetar på en Home Office.  

När den här tekniken infaller ifrån varandra är när du behöver göra en sökning som kombinerar både plats-id samt Platsbeskrivning. Exempel:

* Hitta alla personer som de har ett hemkontor och har en plats-ID på 4.  

Om du kommer ihåg det ursprungliga innehållet såg ut så här:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Men nu när vi har delat upp data i separata fält, vi har inget sätt att veta om hemkontor Jen Campbell relaterar till `locationsID 3` eller `locationsID 4`.  

För att hantera det här fallet definierar du ett annat fält i indexet som kombinerar alla data till en enda samling.  I vårt exempel vi ringer upp det här fältet `locationsCombined` och vi kommer att separera innehåll med en `||` men du kan välja valfri avgränsare som du tycker skulle vara en unik uppsättning tecken för ditt innehåll. Exempel: 

![exempeldata, 2 rader med avgränsare](./media/search-howto-complex-data-types/sample-data-2.png)

Med det här `locationsCombined` fält, vi kan nu hantera ännu fler frågor, till exempel:

* Visa antalet personer som arbetar på en ”hemkontor' med platsen för Id för” 4 ”.  
* Sök efter personer som arbetar på en Home Office med platsen Id ”4”. 

## <a name="limitations"></a>Begränsningar
Den här tekniken är användbar för ett antal scenarier, men den är inte tillämplig i alla fall.  Exempel:

1. Om du inte har en statisk uppsättning fält i din komplex datatyp och gick det inte att mappa alla möjliga typer till ett fält. 
2. Uppdatering av kapslade objekt kräver lite extraarbete att avgöra exakt vad som behöver uppdateras i Azure Search-index

## <a name="sample-code"></a>Exempelkod
Du kan se ett exempel på hur du indexera en komplex uppsättning för JSON-data till Azure Search och utför ett antal frågor i den här datauppsättningen på den här [GitHub-lagringsplatsen](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Nästa steg
[Rösta på inbyggt stöd för komplexa datatyper](https://feedback.azure.com/forums/263029-azure-search) på Azure Search UserVoice sidan och ange eventuella ytterligare information som du vill bli kontaktad att tänka på avseende funktionen implementering. Du kan även kontakta mig direkt på Twitter på @liamca.

