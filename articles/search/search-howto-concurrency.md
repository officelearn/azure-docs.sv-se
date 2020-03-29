---
title: Så här hanterar du samtidiga skrivningar till resurser
titleSuffix: Azure Cognitive Search
description: Använd optimistisk samtidighet för att undvika kollisioner i luften på uppdateringar eller borttagningar till Azure Cognitive Search-index, indexerare och datakällor.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edfb2fe5cc37a00335ca7b5be851a88825b03eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792220"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Hantera samtidighet i Azure Cognitive Search

När du hanterar Azure Cognitive Search-resurser som index och datakällor är det viktigt att uppdatera resurser på ett säkert sätt, särskilt om resurser används samtidigt av olika komponenter i ditt program. När två klienter samtidigt uppdaterar en resurs utan samordning är konkurrensvillkor möjliga. För att förhindra detta erbjuder Azure Cognitive Search en *optimistisk samtidighetsmodell*. Det finns inga lås på en resurs. I stället finns det en ETag för varje resurs som identifierar resursversionen så att du kan skapa begäranden som undviker oavsiktliga överskrivningar.

> [!Tip]
> Konceptuell kod i en [exempel C#-lösning](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) förklarar hur samtidighetskontroll fungerar i Azure Cognitive Search. Koden skapar villkor som anropar samtidighetskontroll. Läsa [koden fragmentet nedan](#samplecode) är förmodligen tillräckligt för de flesta utvecklare, men om du vill köra det, redigera appsettings.json att lägga till tjänstens namn och en admin api-nyckel. Med en tjänst-URL `http://myservice.search.windows.net`till `myservice`är tjänstnamnet .

## <a name="how-it-works"></a>Hur det fungerar

Optimistisk samtidighet implementeras genom åtkomstvillkorskontroller i API-anrop som skriver till index, indexerare, datakällor och synonymMap-resurser.

Alla resurser har en [*entitetstagg (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) som tillhandahåller information om objektversion. Genom att kontrollera ETag först kan du undvika samtidiga uppdateringar i ett typiskt arbetsflöde (hämta, ändra lokalt, uppdatera) genom att se till att resursens ETag matchar din lokala kopia.

+ REST API använder en [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) på begäran huvudet.
+ .NET SDK ställer in ETag via ett accessCondition-objekt och ställer in [If-Match | Om-Matcha-ingen-huvud](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) på resursen. Alla objekt som ärver från [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) har ett accessCondition-objekt.

Varje gång du uppdaterar en resurs ändras dess ETag automatiskt. När du implementerar samtidighetshantering är allt du gör att en förutsättning för uppdateringsbegäran som kräver att fjärrresursen har samma ETag som kopian av resursen som du har ändrat på klienten. Om en samtidig process redan har ändrat fjärrresursen matchar ETag inte förutsättningen och begäran misslyckas med HTTP 412. Om du använder .NET SDK visas detta `CloudException` som `IsAccessConditionFailed()` en plats där tilläggsmetoden returnerar true.

> [!Note]
> Det finns bara en mekanism för samtidighet. Det används alltid oavsett vilket API som används för resursuppdateringar.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Använd ärenden och exempelkod

Följande kod visar åtkomstVillkorskontroller för nyckeluppdateringsåtgärder:

+ Misslyckas med en uppdatering om resursen inte längre finns
+ Misslyckas med en uppdatering om resursversionen ändras

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Exempelkod från [DotNetETagsExplainer-programmet](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Cognitive Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Cognitive Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Designmönster

Ett designmönster för att implementera optimistisk samtidighet bör innehålla en loop som försöker återställa kontrollen av åtkomstvillkoret, ett test för åtkomstvillkoret och eventuellt hämtar en uppdaterad resurs innan du försöker tillämpa ändringarna igen.

Det här kodavsnittet illustrerar tillägget av en synonymMapp till ett index som redan finns. Den här koden är från [synonym C# exempel för Azure Cognitive Search](search-synonyms-tutorial-sdk.md).

Kodavsnittet får "hotell"-indexet, kontrollerar objektversionen på en uppdateringsåtgärd, genererar ett undantag om villkoret misslyckas och försöker sedan åtgärden (upp till tre gånger), med början i indexhämtning från servern för att få den senaste versionen.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>Nästa steg

Granska [synonymerna C# exempel](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) för mer sammanhang om hur man säkert uppdaterar ett befintligt index.

Prova att ändra något av följande exempel så att de innehåller ETags- eller AccessCondition-objekt.

+ [REST API-exempel på GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [.NET SDK-exempel på GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Den här lösningen innehåller projektet "DotNetEtagsExplainer" som innehåller koden som presenteras i den här artikeln.

## <a name="see-also"></a>Se även

[Vanliga HTTP-begäranden och svarshuvuden](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP-statuskoder](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[Indexåtgärder (REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
