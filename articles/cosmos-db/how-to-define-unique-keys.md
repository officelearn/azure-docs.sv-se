---
title: Definiera unika nycklar för en Azure Cosmos-behållare
description: Lär dig hur du definierar unika nycklar för en Azure Cosmos-behållare
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/3/2019
ms.author: thweiss
ms.openlocfilehash: 1e8a88b640d588fbdb96089f11672b09590ee5d9
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597731"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definiera unika nycklar för en Azure Cosmos-behållare

Den här artikeln beskrivs olika sätt att definiera [unika nycklar](unique-keys.md) när du skapar en Azure Cosmos-behållare. Det går för närvarande att utföra åtgärden med hjälp av Azure portal eller via en av SDK: erna.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. [Skapa ett nytt Azure Cosmos-konto](create-sql-api-dotnet.md#create-account) eller välj en befintlig.

1. Öppna den **Datautforskaren** rutan och välj den behållare som du vill arbeta med.

1. Klicka på **ny behållare**.

1. I den **Lägg till behållare** dialogrutan klickar du på **+ Lägg till unik nyckel** att lägga till en unik nyckelpost.

1. Ange sökväg för unika nyckelvillkor

1. Om det behövs kan du lägga till fler unika viktiga poster genom att klicka på **+ Lägg till unik nyckel**

![Skärmbild av unika nyckelvillkor posten på Azure portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Använd .NET SDK V2

När du skapar en ny behållare med den [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), ett `UniqueKeyPolicy` objekt som kan användas för att definiera unika nyckelvillkor.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-java-sdk"></a>Använda Java SDK

När du skapar en ny behållare med den [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), ett `UniqueKeyPolicy` objekt som kan användas för att definiera unika nyckelvillkor.

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

## <a name="use-the-nodejs-sdk"></a>Använda Node.js-SDK

När du skapar en ny behållare med den [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos), ett `UniqueKeyPolicy` objekt som kan användas för att definiera unika nyckelvillkor.

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

När du skapar en ny behållare med den [Python SDK](https://pypi.org/project/azure-cosmos/), unika nyckelvillkor kan anges som en del av ordlista som skickas som parameter.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            { 'paths': ['/firstName', '/lastName', '/emailAddress'] },
            { 'paths': ['/address/zipCode'] }
        ]
    }
})
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [partitionering](partition-data.md)
- Utforska [så fungerar indexering](index-overview.md)