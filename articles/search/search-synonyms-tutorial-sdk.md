---
title: C# Exempel på synonymer – Azure Search
description: I det C# här exemplet lär du dig hur du lägger till synonym funktionen i ett index i Azure Search. En synonymkarta är en lista med motsvarande termer. Fält med stöd för synonymer expanderar frågor till att innehålla det användarangivna uttrycket och alla relaterade synonymer.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ad71a6ab5090e601ef075617edf08c421abebdb0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647759"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>Exempel: Lägga till synonymer för Azure Search i C#

Med synonymer kan du utöka en fråga genom att matcha mot termer som anses betyda samma sak som den angivna söktermen. Du kanske vill att söktermen ”bil” även ska matcha dokument som innehåller termen ”fordon”. 

I Azure Search definieras synonymer i en *synonymmappning* enligt *mappningsregler* som associerar ekvivalenta termer. I det här exemplet beskrivs viktiga steg för att lägga till och använda synonymer med ett befintligt index. Lär dig att:

> [!div class="checklist"]
> * Skapa en synonym mappning med klassen [SynonymMap](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) . 
> * Ange egenskapen [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) för fält som ska ha stöd för frågans expansion via synonymer.

Du kan fråga ett synonymt-aktiverat fält som vanligt. Det krävs ingen ytterligare frågesyntax som krävs för att komma åt synonymer.

Du kan skapa flera synonymmappningar, publicera dem som en resurs på tjänstnivå tillgänglig för alla index och sedan referera till den mappning som ska användas på fältnivå. När en fråga körs kommer Azure Search då att söka i den synonymmappning som anges för fälten som används i frågan förutom att söka i indexet.

> [!NOTE]
> Synonymer kan skapas program mässigt, men inte i portalen. Om du skulle ha nytta av funktionen Synonymer på Azure Portal vill vi gärna att du skickar din feedback via [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien behöver du följande:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Search-tjänsten](search-create-service-portal.md)
* [.NET-biblioteket Microsoft.Azure.Search](https://aka.ms/search-sdk)
* [How to use Azure Search from a .NET Application](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) (Så använder du Azure Search från .NET-program)

## <a name="overview"></a>Översikt

Med före-och-efter-frågor kan du se hur användbara synonymer kan vara. I det här exemplet använder du ett exempel program som kör frågor och returnerar resultat i ett exempel index. Exempelprogrammet skapar ett litet index med namnet ”hotels” som innehåller två dokument. Programmet kör sökfrågor med termer och fraser som inte förekommer i indexet, aktiverar synonymfunktionen och kör sedan samma sökningar igen. I koden nedan kan du se flödet som en helhet.

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
Stegen där exempelindexet skapas och fylls i beskrivs i [How to use Azure Search from a .NET Application](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) (Så använder du Azure Search från .NET-program).

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
   En synonymmappning måste följa det öppna standardformatet `solr`. Formatet förklaras i [Synonyms in Azure Search](search-synonyms.md) (Synonymer i Azure Search) i avsnittet `Apache Solr synonym format`.

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

Du får en helt ny sökupplevelse när du lägger till synonymer. I det här exemplet kunde inte de ursprungliga frågorna returnera meningsfulla resultat trots att dokumenten i vårt index var relevanta. Genom att aktivera synonymer kan vi expandera ett index med termer som används ofta utan att behöva ändra underliggande data i indexet.

## <a name="sample-application-source-code"></a>Källkod för exempelprogrammet
Du hittar hela källkoden för exempelprogrammet i den här genomgången på [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa efter ett exempel är genom att ta bort resurs gruppen som innehåller den Azure Search tjänsten. Du kan ta bort resursgruppen nu så att allt innehåll i den tas bort permanent. I portalen ser du resursgruppens namn på översiktssidan för Azure Search-tjänsten.

## <a name="next-steps"></a>Nästa steg

I det här exemplet demonstreras funktionen synonymer i C# kod för att skapa och publicera mappnings regler och sedan anropa synonym kartan på en fråga. Mer information finns i referensdokumentationen för [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) och [REST-API:t](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Använda synonymer i Azure Search](search-synonyms.md)
