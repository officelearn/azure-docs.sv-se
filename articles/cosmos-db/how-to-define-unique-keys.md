---
title: Definiera unika nycklar för en Azure Cosmos-behållare
description: Lär dig hur du definierar unika nycklar för en Azure Cosmos-behållare med Azure-portalen, PowerShell, .Net, Java och flera andra SDK:er.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: fa62495a7b51c9a06a91102299378c15e811eae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872119"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definiera unika nycklar för en Azure Cosmos-behållare

Den här artikeln innehåller olika sätt att definiera [unika nycklar](unique-keys.md) när du skapar en Azure Cosmos-behållare. Det är för närvarande möjligt att utföra den här åtgärden antingen med hjälp av Azure-portalen eller via en av SDK:erna.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account) eller välj ett befintligt konto.

1. Öppna fönstret **Data explorer** och markera den behållare som du vill arbeta med.

1. Klicka på **Ny behållare**.

1. I dialogrutan **Lägg till behållare** klickar du på + Lägg till unik **nyckel** för att lägga till en unik nyckelpost.

1. Ange sökvägen/sökvägarna för den unika nyckelbegränsningen

1. Om det behövs kan du lägga till fler unika nyckelposter genom att klicka på **+ Lägg till unik tangent**

    ![Skärmbild av unik nyckelbegränsningspost på Azure-portalen](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>Använda Powershell

Om du vill skapa en behållare med unika nycklar finns [i Skapa en Azure Cosmos-behållare med unik nyckel och TTL](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk-v2"></a>Använda .NET SDK V2

När du skapar en ny behållare med [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)kan ett `UniqueKeyPolicy` objekt användas för att definiera unika tangentbegränsningar.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>Använda .NET SDK V3

När du skapar en ny behållare med [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)använder du SDK:s flytande API för att deklarera unika nycklar på ett koncist och läsbart sätt.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Använda Java SDK

När du skapar en ny behållare med `UniqueKeyPolicy` [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)kan ett objekt användas för att definiera unika nyckelbegränsningar.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Använda Node.js SDK

När du skapar en ny behållare med [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)kan ett `UniqueKeyPolicy` objekt användas för att definiera unika tangentbegränsningar.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Använda Python SDK

När du skapar en ny behållare med [Python SDK](https://pypi.org/project/azure-cosmos/)kan unika nyckelbegränsningar anges som en del av ordlistan som skickas som parameter.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [partitionering](partition-data.md)
- Utforska [hur indexering fungerar](index-overview.md)
