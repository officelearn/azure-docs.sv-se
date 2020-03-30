---
title: Skapa eller ta bort en blob-behållare med .NET - Azure Storage
description: Lär dig hur du skapar eller tar bort en blob-behållare i ditt Azure Storage-konto med hjälp av .NET-klientbiblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135946"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Skapa eller ta bort en behållare i Azure Storage med .NET

Blobbar i Azure Storage är ordnade i behållare. Innan du kan ladda upp en blob måste du först skapa en behållare. Den här artikeln visar hur du skapar och tar bort behållare med [Azure Storage-klientbiblioteket för .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="name-a-container"></a>Namnge en behållare

Ett behållarnamn måste vara ett giltigt DNS-namn, eftersom det utgör en del av den unika URI som används för att adressera behållaren eller dess blobbar. Följ dessa regler när du namnger en behållare:

- Behållarnamn kan vara mellan 3 och 63 tecken långa.
- Behållarnamnen måste börja med en bokstav eller ett tal och kan bara innehålla gemener, siffror och strecktecknet (-).
- Två eller flera på varandra följande strecktecken är inte tillåtna i behållarnamn.

URI för en behållare är i det här formatet:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Skapa en container

Om du vill skapa en behållare anropar du någon av följande metoder:

- [Skapa](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [SkapaAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Metoderna **Create** och **CreateAsync** genererar ett undantag om det redan finns en behållare med samma namn.

**Metoderna CreateIfNotExists** och **CreateIfNotExistsAsync** returnerar ett booleskt värde som anger om behållaren skapades. Om det redan finns en behållare med samma namn returnerar dessa metoder **False** för att ange att en ny behållare inte har skapats.

Behållare skapas omedelbart under lagringskontot. Det går inte att kapsla en behållare under en annan.

I följande exempel skapas en behållare asynkront:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Skapa rotbehållaren

En rotbehållare fungerar som standardbehållare för ditt lagringskonto. Varje lagringskonto kan ha en rotbehållare som måste namnges *$root.*. Du måste uttryckligen skapa eller ta bort rotbehållaren.

Du kan referera till en blob som lagras i rotbehållaren utan att inkludera rotbehållarens namn. Med rotbehållaren kan du referera till en blob på den översta nivån i lagringskontohierarkin. Du kan till exempel referera till en blob som finns i rotbehållaren på följande sätt:

`https://myaccount.blob.core.windows.net/default.html`

I följande exempel skapas rotbehållaren synkront:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Ta bort en container

Om du vill ta bort en behållare i .NET använder du någon av följande metoder:

- [Ta bort](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Metoderna **Delete** och **DeleteAsync** genererar ett undantag om behållaren inte finns.

**Metoderna DeleteIfExists** och **DeleteIfExistsAsync** returnerar ett booleskt värde som anger om behållaren har tagits bort. Om den angivna behållaren inte finns returnerar dessa metoder **False** för att ange att behållaren inte har tagits bort.

När du har tagit bort en behållare kan du inte skapa en behållare med samma namn i minst 30 sekunder och eventuellt längre. Medan behållaren tas bort misslyckas ett försök att skapa en behållare med samma namn med HTTP-felkod 409 (Konflikt). Alla andra åtgärder på behållaren eller blobbar som den innehåller misslyckas med HTTP-felkod 404 (Hittades inte) medan behållaren tas bort.

I följande exempel tas den angivna behållaren bort och undantaget hanteras om behållaren inte finns:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

I följande exempel visas hur du tar bort alla behållare som börjar med ett angivet prefix. Exemplet bryter lånet om det finns ett befintligt lån på behållaren.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Se även

- [Åtgärden Skapa behållare](/rest/api/storageservices/create-container)
- [Åtgärd för att ta bort container](/rest/api/storageservices/delete-container)
