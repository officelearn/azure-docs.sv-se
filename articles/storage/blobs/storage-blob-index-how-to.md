---
title: Använd BLOB-index för att hantera och hitta data i Azure Blob Storage
description: Se exempel på hur du använder BLOB-taggar för att kategorisera, hantera och fråga för att identifiera BLOB-objekt.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: hux
ms.openlocfilehash: 6e3ce99211da35105fd9e118a850110dfd48ece1
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84986280"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Använd BLOB-Taggar (för hands version) för att hantera och hitta data i Azure Blob Storage

BLOB index-Taggar kategoriserar data i ditt lagrings konto med hjälp av attribut för nyckel värdes taggar. Taggarna indexeras automatiskt och exponeras som ett fråge bara flerdimensionellt index för att enkelt hitta data. Den här artikeln visar hur du ställer in, hämtar och hittar data med hjälp av BLOB-taggar.

Mer information om BLOB-indexet finns i [Hantera och hitta data på Azure Blob Storage med BLOB index (för hands version)](storage-manage-find-blobs.md).

> [!NOTE]
> BLOB-indexet finns i en offentlig för hands version och är tillgängligt i regionerna **Kanada**, **östra**, Australien, **centrala** och **Frankrike, södra** . Mer information om den här funktionen tillsammans med kända problem och begränsningar finns i [Hantera och hitta data på Azure Blob Storage med BLOB index (för hands version)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Krav
# <a name="portal"></a>[Portal](#tab/azure-portal)
- Prenumeration registrerad och godkänd för åtkomst till för hands versionen av BLOB-index
- Åtkomst till [Azure Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Eftersom BLOB-indexet finns i en offentlig för hands version, släpps .NET Storage-paketet i NuGet-flödet för för hands versionen. Det här biblioteket kan ändras mellan nu och när det blir officiellt. 

1. I Visual Studio lägger du till URL: en `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` till dina NuGet-paket källor. 

   Mer information finns i [paket källor](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. Leta upp paketet **Azure. Storage. blobs** i NuGet Package Manager och installera version **12.5.0-dev. 20200422.2** i projektet. Du kan också köra kommandot```Install-Package Azure.Storage.Blobs -Version 12.5.0-dev.20200422.2```

   Mer information finns i [hitta och installera ett paket](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Lägg till följande using-uttryck högst upp i din kod fil.
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>Ladda upp en ny BLOB med index-Taggar
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto 

2. Navigera till alternativet **behållare** under **BLOB service**, välj din behållare

3. Välj knappen **överför** för att öppna bladet överför och bläddra i det lokala fil systemet för att hitta en fil som ska laddas upp som en Block-Blob.

4. Expandera den **avancerade** List rutan och gå till avsnittet **blobb index Taggar**

5. Ange de index taggar för nyckel/värde-blob som du vill använda för dina data

6. Välj knappen **överför** för att överföra blobben

![Ladda upp data med BLOB-Taggar](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
I följande exempel visas hur du skapar en tilläggs-BLOB med taggar som anges när du skapar.
```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Hämta, ange och uppdatera BLOB-index Taggar
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto 

2. Navigera till alternativet **behållare** under **BLOB service**, välj din behållare

3. Välj önskad BLOB från listan över blobbar i den valda behållaren

4. På fliken Översikt över BLOB visas din blobs egenskaper inklusive eventuella **BLOB-Taggar**

5. Du kan hämta, ange, ändra eller ta bort alla nyckel-/värde index-taggar för din BLOB

6. Välj knappen **Spara** för att bekräfta eventuella uppdateringar av din BLOB

![Hämta, ange, uppdatera och ta bort BLOB index-taggar på objekt](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrera och hitta data med BLOB-Taggar

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Portal tillämpar filtret BLOB index taggar automatiskt `@container` parametern för att ange den valda behållarens omfång. Om du vill filtrera och hitta taggade data över hela ditt lagrings konto kan du använda våra REST API, SDK: er eller verktyg.

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto. 

2. Navigera till alternativet **behållare** under **BLOB service**, välj din behållare

3. Välj **filter knappen BLOB index Taggar** för att filtrera i den valda behållaren

4. Ange en tagg-och tagg värde för BLOB index

5. Välj **filter knappen BLOB index Taggar** för att lägga till ytterligare tag filter (upp till 10)

![Filtrera och hitta taggade objekt med BLOB-index Taggar](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Livs cykel hantering med tagg filter för BLOB index

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto. 

2. Navigera till **livs cykel hanterings** alternativet under **BLOB service**

3. Välj *Lägg till regel* och fyll sedan i formulär fälten för åtgärds uppsättning

4. Välj filter uppsättning för att lägga till valfritt filter för prefix matchning och blob-index matcha ![ Lägg till BLOB index tag filter för livs cykel hantering](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Välj **Granska + Lägg** till för att granska regel inställningarna ![ livs cykel hanterings regel med filter för BLOB index Taggar](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. Välj **Lägg till** för att tillämpa den nya regeln i livs cykel hanterings principen

# <a name="net"></a>[.NET](#tab/net)
Policys för [livs cykel hantering](storage-lifecycle-management-concepts.md) tillämpas för varje lagrings konto på kontroll Plans nivån. För .NET installerar du [Microsoft Azure Management Storage Library version 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) eller högre för att dra nytta av matchnings filtret för BLOB-index i en hanterings regel för livs cykel.

---

## <a name="next-steps"></a>Nästa steg

Läs mer om BLOB-index. Se [Hantera och hitta data på Azure Blob Storage med BLOB index (för hands version)](storage-manage-find-blobs.md )

Läs mer om livs cykel hantering. Se [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
