---
title: Självstudie i förhandsgranskning av synonymer i Azure Search | Microsoft Docs
description: Lägg till funktionen för förhandsgranskning av synonymer i ett index i Azure Search.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.topic: tutorial
ms.date: 03/31/2017
ms.author: heidist
ms.openlocfilehash: 5614b0253b43938b5079515899590c7c560d232c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="synonym-preview-c-tutorial-for-azure-search"></a>Förhandsgranska synonymer – en självstudie i C# för Azure Search

Med synonymer kan du utöka en fråga genom att matcha mot termer som anses betyda samma sak som den angivna söktermen. Du kanske vill att söktermen ”bil” även ska matcha dokument som innehåller termen ”fordon”.

I Azure Search definieras synonymer i en *synonymmappning* enligt *mappningsregler* som associerar ekvivalenta termer. Du kan skapa flera synonymmappningar, publicera dem som en resurs på tjänstnivå tillgänglig för alla index och sedan referera till den mappning som ska användas på fältnivå. När en fråga körs kommer Azure Search då att söka i den synonymmappning som anges för fälten som används i frågan förutom att söka i indexet.

> [!NOTE]
> Synonymfunktionen finns för närvarande bara i en förhandsversion och ingår bara i de senaste API- och SDK-förhandsversionerna (api-version=2016-09-01-Preview, SDK version 4.x-preview). Funktionen stöds för närvarande inte på Azure Portal. För förhandsversioner av API:er gäller inget SLA och de här funktionerna kan ändras, så du bör inte använda dem i produktionsmiljöer.

## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien behöver du följande:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Search-tjänsten](search-create-service-portal.md)
* [Förhandsversionen av .NET-biblioteket Microsoft.Azure.Search](https://aka.ms/search-sdk-preview)
* [How to use Azure Search from a .NET Application](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) (Så använder du Azure Search från .NET-program)

## <a name="overview"></a>Översikt

Med före-och-efter-frågor kan du se hur användbara synonymer kan vara. I den här självstudien använder vi ett exempelprogram som kör frågor och returnerar resultat för ett exempelindex. Exempelprogrammet skapar ett litet index med namnet ”hotels” som innehåller två dokument. Programmet kör sökfrågor med termer och fraser som inte förekommer i indexet, aktiverar synonymfunktionen och kör sedan samma sökningar igen. I koden nedan kan du se flödet som en helhet.

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

I `RunQueriesWithNonExistentTermsInIndex` kör vi sökningar med termerna ”five star”, ”internet” och ”economy AND hotel”.
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

Du får en helt ny sökupplevelse när du lägger till synonymer. I den här självstudien fick vi inga meningsfulla resultat från de ursprungliga frågorna trots att dokumenten i indexet var relevanta. Genom att aktivera synonymer kan vi expandera ett index med termer som används ofta utan att behöva ändra underliggande data i indexet.

## <a name="sample-application-source-code"></a>Källkod för exempelprogrammet
Du hittar hela källkoden för exempelprogrammet i den här genomgången på [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="next-steps"></a>Nästa steg

* Läs [How to use synonyms in Azure Search](search-synonyms.md) (Använda synonymer i Azure Search)
* Läs om [synonymer i dokumentationen till REST API](https://aka.ms/rgm6rq)
* Bläddra i referensinformationen till [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) och [REST API](https://docs.microsoft.com/rest/api/searchservice/).
