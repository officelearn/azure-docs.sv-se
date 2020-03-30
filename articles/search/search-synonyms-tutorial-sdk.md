---
title: Synonymer C# exempel
titleSuffix: Azure Cognitive Search
description: I det här C#-exemplet lär du dig hur du lägger till synonymfunktionen i ett index i Azure Cognitive Search. En synonymkarta är en lista med motsvarande termer. Fält med stöd för synonymer expanderar frågor till att innehålla det användarangivna uttrycket och alla relaterade synonymer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8cc085fd27004928babd7df305a4452d1b068f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794243"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Exempel: Lägga till synonymer för Azure Cognitive Search i C #

Med synonymer kan du utöka en fråga genom att matcha mot termer som anses betyda samma sak som den angivna söktermen. Du kanske vill att söktermen ”bil” även ska matcha dokument som innehåller termen ”fordon”. 

I Azure Cognitive Search definieras synonymer i en *synonymkarta*, genom *mappningsregler* som associerar motsvarande termer. Det här exemplet omfattar viktiga steg för att lägga till och använda synonymer med ett befintligt index. Lär dig att:

> [!div class="checklist"]
> * Skapa en synonymkarta med klassen [SynonymMap.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) 
> * Ange egenskapen [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) i fält som ska stödja frågeexpansion via synonymer.

Du kan fråga efter ett synonymaktiverat fält som vanligt. Det finns ingen ytterligare frågesyntax som krävs för att komma åt synonymer.

Du kan skapa flera synonymmappningar, publicera dem som en resurs på tjänstnivå tillgänglig för alla index och sedan referera till den mappning som ska användas på fältnivå. Vid frågetid, förutom att söka i ett index, gör Azure Cognitive Search en sökning i en synonymkarta, om en anges i fält som används i frågan.

> [!NOTE]
> Synonymer kan skapas programmässigt, men inte i portalen. Om du skulle ha nytta av funktionen Synonymer på Azure Portal vill vi gärna att du skickar din feedback via [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Krav

I den här självstudien behöver du följande:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search-tjänst](search-create-service-portal.md)
* [.NET-biblioteket Microsoft.Azure.Search](https://aka.ms/search-sdk)
* [Så här använder du Azure Cognitive Search från ett .NET-program](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Översikt

Med före-och-efter-frågor kan du se hur användbara synonymer kan vara. I det här exemplet använder du ett exempelprogram som kör frågor och returnerar resultat på ett exempelindex. Exempelprogrammet skapar ett litet index med namnet ”hotels” som innehåller två dokument. Programmet kör sökfrågor med termer och fraser som inte förekommer i indexet, aktiverar synonymfunktionen och kör sedan samma sökningar igen. I koden nedan kan du se flödet som en helhet.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Stegen för att skapa och fylla i exempelindexet förklaras i [Så här använder du Azure Cognitive Search från ett .NET-program](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>”Före”-frågor

Kör sökningar med termerna ”five star”, ”internet” och ”economy AND hotel” i `RunQueriesWithNonExistentTermsInIndex`.
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Inget av de två indexerade dokumenten innehåller de här söktermerna, så från den första `RunQueriesWithNonExistentTermsInIndex` får vi följande resultat.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Aktivera synonymer

Att aktivera synonymer är en tvåstegsprocess. Först definierar vi och laddar upp synonymregler och sedan konfigurerar vi de fält som ska använda dem. Processen beskrivs i `UploadSynonyms` och `EnableSynonymsInHotelsIndex`.

1. Lägg till en synonymmappning i söktjänsten. I `UploadSynonyms` definierar vi fyra regler i synonymmappningen desc-synonymmap och laddar upp mappningen till tjänsten.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   En synonymmappning måste följa det öppna standardformatet `solr`. Formatet förklaras i [Synonymer i Azure](search-synonyms.md) `Apache Solr synonym format`Cognitive Search under avsnittet .

2. Konfigurera sökbara fält så att synonymmappningen används i indexdefinitionen. I `EnableSynonymsInHotelsIndex` aktiverar vi synonymer för de två fälten `category` och `tags` genom att ange namnet på synonymmappningen för egenskapen `synonymMaps`.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   När du lägger till en synonymmappning behöver du inte skapa indexet på nytt. Du kan lägga till en synonymmappning för tjänsten och sedan göra tillägg till befintliga fältdefinitioner i dina index så att den nya synonymmappningen används. Indexet tillgänglighet påverkas inte om du lägger till nya attribut. Samma sak gäller om du inaktiverar synonymer för ett fält. Du kan helt enkelt ange en tom lista för egenskapen `synonymMaps`.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>”Efter”-frågor

När synonymmappningen har laddats upp och indexet har uppdaterats så att mappningen används ger det andra anropet av `RunQueriesWithNonExistentTermsInIndex` följande resultat:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
Den första frågan hittar dokument via regeln `five star=>luxury`. Den andra frågan utökar sökningen med `internet,wifi` och den tredje med både `hotel, motel` och `economy,inexpensive=>budget`, och därför hittas de matchande dokumenten.

Du får en helt ny sökupplevelse när du lägger till synonymer. I det här exemplet kunde de ursprungliga frågorna inte returnera meningsfulla resultat trots att dokumenten i vårt index var relevanta. Genom att aktivera synonymer kan vi expandera ett index med termer som används ofta utan att behöva ändra underliggande data i indexet.

## <a name="sample-application-source-code"></a>Källkod för exempelprogrammet
Du hittar hela källkoden för exempelprogrammet i den här genomgången på [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa efter ett exempel är att ta bort resursgruppen som innehåller Azure Cognitive Search-tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. I portalen finns resursgruppsnamnet på sidan Översikt i Azure Cognitive Search-tjänsten.

## <a name="next-steps"></a>Nästa steg

Det här exemplet visade synonymfunktionen i C#-kod för att skapa och bokföra mappningsregler och anropa sedan synonymkartan i en fråga. Mer information finns i referensdokumentationen för [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) och [REST-API:t](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Så här använder du synonymer i Azure Cognitive Search](search-synonyms.md)
