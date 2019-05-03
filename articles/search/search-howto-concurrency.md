---
title: Hantera samtidiga skrivningar till resurser – Azure Search
description: Använd Optimistisk samtidighet för att undvika mitten air kollisioner på uppdateringar eller borttagningar till Azure Search-index, indexerare och datakällor.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7e569fa30727f2df7411eee5fa6d48f9b9454460
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025328"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Hantera samtidighet i Azure Search

När du hanterar Azure Search-resurser, till exempel index och datakällor, är det viktigt att uppdatera resurser på ett säkert sätt, särskilt om resurser samtidigt av olika komponenter i ditt program. När två klienter uppdaterar samtidigt en resurs utan samordning, är konkurrenstillstånd möjliga. Om du vill förhindra detta kan Azure Search erbjuder en *optimistisk samtidighetsmodellen*. Det finns inga lås på en resurs. Det finns i stället en ETag för varje resurs som identifierar resursen version så att du kan skapa förfrågningar som undviker oavsiktligt skriver över.

> [!Tip]
> Konceptuell koden i en [exempel C# lösning](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) förklarar hur du samtidighetskontroll i Azure Search. Koden skapar villkor som anropar samtidighetskontroll. Läsa den [kodfragmentet nedan](#samplecode) är förmodligen tillräckliga för de flesta utvecklare, men om du vill köra den, redigera appsettings.json för att lägga till tjänstens namn och en api-administratörsnyckel. Tjänsten URL: en `http://myservice.search.windows.net`, namnet på tjänsten är `myservice`.

## <a name="how-it-works"></a>Hur det fungerar

Optimistisk samtidighet implementeras via åtkomst villkoret kontrollerar i API-anrop som skriver till index, indexerare, datakällor och synonymMap resurser.

Alla resurser har en [ *entitetstagg (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) som innehåller versionsinformation för objektet. Genom att kontrollera ETag först, kan du undvika att samtidiga uppdateringar i ett vanligt arbetsflöde (get, ändra lokalt, uppdatera) genom att säkerställa resursens ETag matchar den lokala kopian.

+ REST-API använder en [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) på rubriken.
+ .NET SDK anger ETag via ett accessCondition-objekt och ange den [If-Match | If-Match-ingen rubrik](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) på resursen. Alla objekt som ärver från [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) har ett accessCondition-objekt.

Varje gång du uppdaterar en resurs ändras dess ETag automatiskt. När du implementerar hantering av samtidighet är allt du gör skapar ett Förhandsvillkoret på begäran om uppdatering som kräver fjärresursen ha samma ETag som kopia av den resurs som du har ändrat på klienten. Om en samtidig process har redan ändrats fjärresursen, ETag matchar inte villkor och begäran misslyckas med HTTP 412. Om du använder .NET SDK detta manifest som en `CloudException` där den `IsAccessConditionFailed()` tilläggsmetod returnerar true.

> [!Note]
> Det finns bara en mekanism för samtidighet. Den används alltid oavsett vilka API används för att uppdatera resurserna.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Användningsfall och exempelkod

Följande kod visar accessCondition söker efter nyckeln uppdateringsåtgärder:

+ Inte en uppdatering om resursen finns inte längre
+ Inte en uppdatering om resursversionen ändras

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Exempelkoden från [DotNetETagsExplainer program](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
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
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

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

## <a name="design-pattern"></a>designmönster

Design arbetsprofilen för att implementera Optimistisk samtidighet bör innehålla en loop som försöker åtkomst villkoret kontrollera ett test för åtkomst-villkor och du kan också hämtar en uppdaterad resurs innan du försöker igen tillämpa ändringarna.

Det här kodstycket visar att lägga till en synonymMap till ett index som redan finns. Den här koden är från den [synonymen C# exempel för Azure Search](search-synonyms-tutorial-sdk.md).

Kodfragmentet hämtar indexet ”hotels”, kontrollerar versionen på en uppdateringsåtgärd, genereras ett undantag om villkoret misslyckas, och sedan försöker igen (upp till tre gånger) från och med index hämtning från servern för att hämta den senaste versionen.

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

Granska den [synonymer C# exempel](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) för mer kontext om hur du uppdaterar ett befintligt index på ett säkert sätt.

Försök att ändra något av följande exempel ska innehålla ETags eller AccessCondition objekt.

+ [REST API-exemplet på GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [.NET SDK-exemplet på GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Den här lösningen inkluderar ”DotNetEtagsExplainer”-projektet som innehåller koden som visas i den här artikeln.

## <a name="see-also"></a>Se också

[Vanliga HTTP-begäranden och svar rubriker](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP-statuskoder](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[Index-åtgärder (REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
