---
title: Så här hanterar du samtidiga skriver till resurser i Azure Search
description: Optimistisk samtidighet Undvik att använda mitt luften kollisioner på uppdateringar eller borttagningar Azure Search index, indexerare,-datakällor.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: f5fa495c1266c847cabc0eb4e35b85132550bc3c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Så här hanterar du samtidighet i Azure Search

När du hanterar Azure Search-resurser, till exempel index och datakällor, är det viktigt att uppdatera resurser på ett säkert sätt, särskilt om resurser används samtidigt av olika komponenter i ditt program. När två klienter samtidigt uppdaterar resurser utan samordning, är konkurrenstillstånd möjliga. Om du vill förhindra detta Azure Search erbjuder en *optimistisk*. Det finns inga lås på en resurs. Det finns i stället en ETag för varje resurs som identifierar resursversionen så att du kan skapa förfrågningar som undviker oavsiktliga skrivs över.

> [!Tip]
> Konceptuell kod i en [exempel C#-lösningen](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) förklarar hur samtidighetskontroll fungerar i Azure Search. Koden skapar villkor som anropar samtidighetskontroll. Läsa den [kodfragmentet nedan](#samplecode) är förmodligen tillräcklig för de flesta utvecklare, men om du vill köra, redigera appsettings.json för att lägga till tjänstens namn och en admin api-nyckel. Tjänsten URL: en `http://myservice.search.windows.net`, tjänstnamnet är `myservice`.

## <a name="how-it-works"></a>Hur det fungerar

Optimistisk samtidighet implementeras via åtkomst villkoret söker i API-anrop skrivning till index, indexerare, datakällor och synonymMap resurser. 

Alla resurser som har en [ *entitetstaggen (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) som innehåller versionsinformation för objektet. Du kan undvika samtidiga uppdateringar i ett normalt arbetsflöde genom att kontrollera ETag först (get, ändra lokalt, uppdatera) genom att kontrollera resursens ETag matchar den lokala kopian. 

+ REST-API: N används en [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) på begärandehuvudet.
+ .NET SDK anger ETag via ett accessCondition objekt, ange den [If-Match | Huvudet If-Match-ingen](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) på resursen. Alla objekt som ärver från [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) har ett accessCondition-objekt.

Varje gång du uppdaterar en resurs ändras dess ETag automatiskt. När du implementerar samtidighet management allt du gör är att publicera ett villkor på begäran om uppdatering som kräver fjärresursen ska ha samma ETag som en kopia av den resurs som du har ändrat på klienten. Om en samtidig process har redan ändrats fjärresursen, ETag matchar inte villkor och begäran att misslyckas med HTTP 412. Om du använder .NET SDK, visar detta som en `CloudException` där den `IsAccessConditionFailed()` metod returnerar true.

> [!Note]
> Det finns endast en mekanism för samtidighet. Den används alltid oavsett vilket API används för att uppdatera resurserna. 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Användningsfall och exempelkod

Följande kod visar accessCondition söker efter uppdateringsåtgärder:

+ Inte en uppdatering om resursen finns inte längre
+ En uppdatering att misslyckas om resursversionen ändras

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Exempel på kod från [DotNetETagsExplainer program](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

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

## <a name="design-pattern"></a>Designmönstret

Ett designmönster för att implementera på Optimistisk samtidighet ska innehålla en loop som försöker villkoret åtkomst kontrollera, ett test för villkoret åtkomst och hämtar eventuellt ett uppdaterade resursen innan du försöker att åter tillämpa ändringarna. 

Det här kodstycket illustrerar för att lägga till en synonymMap till ett index som redan finns. Den här koden är från den [synonymen (förhandsgranskning) C#-självstudiekurs för Azure Search](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk). 

Sammandraget hämtar ”hotell” index, kontrollerar objektversionen på en uppdateringsåtgärd, genererar ett undantag om villkoret misslyckas, och sedan försöker igen (upp till tre gånger), från och med index hämtning från servern för att hämta den senaste versionen.

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

Granska de [synonymer C#-exempel](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) för mer kontext om hur du uppdaterar ett befintligt index på ett säkert sätt.

Försök att ändra någon av följande prov ska innehålla ETags eller AccessCondition objekt.

+ [REST API-exemplet på Github](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [.NET SDK-exempel på Github](https://github.com/Azure-Samples/search-dotnet-getting-started). Den här lösningen innehåller ”DotNetEtagsExplainer”-projektet som innehåller koden som visas i den här artikeln.

## <a name="see-also"></a>Se också

  [Vanliga HTTP-begäran och svar rubriker](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)    
  [Statuskoder för HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) [Index-åtgärder (REST-API)](https://docs.microsoft.com/\rest/api/searchservice/index-operations)