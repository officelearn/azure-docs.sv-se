---
title: Hantera indexeringsprinciper i Azure Cosmos DB
description: Lär dig hur du hanterar indexeringsprinciper, inkluderar eller utesluter en egenskap från indexering, hur du definierar indexering med olika Azure Cosmos DB SDK:er
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 58a1ee13afa76b152723cb71d4037f9c31cc8d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252082"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Hantera indexeringsprinciper i Azure Cosmos DB

I Azure Cosmos DB indexeras data efter [indexeringsprinciper](index-policy.md) som har definierats för varje behållare. Standardprincipen för indexering för nyligen skapade containrar framtvingar intervallindex för strängar eller nummer. Du kan åsidosätta den här principen med din egen anpassade indexeringsprincip.

## <a name="indexing-policy-examples"></a>Exempel på indexeringsprincip

Här är några exempel på indexeringsprinciper som visas i [deras JSON-format](index-policy.md#include-exclude-paths), vilket är hur de exponeras på Azure-portalen. Samma parametrar kan ställas in via Azure CLI eller valfri SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Opt-out-princip för att selektivt utesluta vissa egenskapssökvägar

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Den här indexeringsprincipen motsvarar den nedanför ```dataType```som ```precision``` manuellt anger ```kind```, och deras standardvärden. Dessa egenskaper är inte längre nödvändiga för att uttryckligen ange och du kan utelämna dem från indexeringsprincipen helt (som visas i exemplet ovan).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Opt-in-policy för att selektivt inkludera vissa egenskapssökvägar

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Den här indexeringsprincipen motsvarar den nedanför ```dataType```som ```precision``` manuellt anger ```kind```, och deras standardvärden. Dessa egenskaper är inte längre nödvändiga för att uttryckligen ange och du kan utelämna dem från indexeringsprincipen helt (som visas i exemplet ovan).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> Det rekommenderas vanligtvis att använda en **opt-out** indexeringsprincip för att låta Azure Cosmos DB proaktivt indexera alla nya egenskaper som kan läggas till din modell.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Endast använda ett rumsligt index på en viss egenskapssökväg

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Exempel på sammansatt indexeringsprincip

Förutom att inkludera eller utesluta banor för enskilda egenskaper kan du också ange ett sammansatt index. Om du vill utföra en fråga `ORDER BY` som har en sats för flera egenskaper krävs ett [sammansatt index](index-policy.md#composite-indexes) för dessa egenskaper. Dessutom har sammansatta index en prestandafördel för frågor som har ett filter och har en ORDER BY-sats på olika egenskaper.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Sammansatt index definierat för (namn asc, age desc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Ovanstående sammansatta index för namn och ålder krävs för Query #1 och Query #2:

Fråga #1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Fråga #2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Det här sammansatta indexet kommer att gynna Query #3 och Query #4 och optimera filtren:

Fråga #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Fråga #4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Sammansatt index definierat för (namn ASC, ålder ASC) och (namn ASC, ålder DESC):

Du kan definiera flera olika sammansatta index inom samma indexeringsprincip.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Sammansatt index definierat för (namn ASC, ålder ASC):

Det är valfritt att ange ordern. Om inget anges stiger ordningen uppåt.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Exklusive alla egenskapssökvägar men hålla indexeringen aktiv

Den här principen kan användas i situationer där [TTL-funktionen (Time-to-Live)](time-to-live.md) är aktiv men inget sekundärt index krävs (för att använda Azure Cosmos DB som ett rent nyckelvärdesarkiv).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Ingen indexering

Den här principen inaktiverar indexeringen. Om `indexingMode` är `none`inställt på kan du inte ange en TTL på behållaren.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Uppdatera indexeringsprincipen

I Azure Cosmos DB kan indexeringsprincipen uppdateras med någon av nedanstående metoder:

- från Azure-portalen
- använda Azure CLI
- använda PowerShell
- använda en av SDK:erna

En [indexeringsprincipuppdatering](index-policy.md#modifying-the-indexing-policy) utlöser en indexomvandling. Förloppet för den här omvandlingen kan också spåras från SDK:erna.

> [!NOTE]
> När du uppdaterar indexeringsprincipen kommer skrivningar till Azure Cosmos DB att vara oavbrutna. Under omindexering kan frågor returnera partiella resultat när indexet uppdateras.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Azure Cosmos-behållare lagrar sin indexeringsprincip som ett JSON-dokument som azure-portalen låter dig redigera direkt.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Skapa ett nytt Azure Cosmos DB-konto eller välj ett befintligt konto.

1. Öppna fönstret **Data explorer** och markera den behållare som du vill arbeta med.

1. Klicka på **Skala & inställningar**.

1. Ändra JSON-dokumentet för indexeringsprincipen (se exempel [nedan)](#indexing-policy-examples)

1. Klicka på **Spara** när du är klar.

![Hantera indexering med hjälp av Azure-portalen](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Om du vill skapa en behållare med en anpassad indexeringsprincip finns [i Skapa en behållare med en anpassad indexprincip med CLI](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>Använd PowerShell

Om du vill skapa en behållare med en anpassad indexeringsprincip finns [i Skapa en behållare med en anpassad indexprincip med Powershell](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk-v2"></a>Använda .NET SDK V2

Objektet `DocumentCollection` från [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) `IndexingPolicy` visar en egenskap `IndexingMode` som gör `IncludedPaths` att `ExcludedPaths`du kan ändra och lägga till eller ta bort och .

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Om du vill spåra indexomvandlingen skickar du ett `RequestOptions` objekt som anger egenskapen `PopulateQuotaInfo` till `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>Använda .NET SDK V3

Objektet `ContainerProperties` från [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (se [den här snabbstarten](create-sql-api-dotnet.md) om dess användning) visar en `IndexingPolicy` egenskap som gör att du kan ändra `IndexingMode` och lägga till eller ta bort `IncludedPaths` och `ExcludedPaths`.

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Om du vill spåra indexomvandlingen skickar du ett `RequestOptions` objekt som anger egenskapen `PopulateQuotaInfo` till `true`och hämtar sedan värdet från svarshuvudet. `x-ms-documentdb-collection-index-transformation-progress`

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

När du definierar en anpassad indexeringsprincip när du skapar en ny behållare kan du skriva den här definitionen på ett koncist och effektivt sätt när du skapar en ny behållare:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Använda Java SDK

Objektet `DocumentCollection` från [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (se [denna Snabbstart](create-sql-api-java.md) om `getIndexingPolicy()` `setIndexingPolicy()` dess användning) exponerar och metoder. Med `IndexingPolicy` objektet som de manipulerar kan du ändra indexeringsläget och lägga till eller ta bort inkluderade och uteslutna banor.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Om du vill spåra indexomvandlingsförloppet på en behållare skickar du ett `RequestOptions` objekt `x-ms-documentdb-collection-index-transformation-progress` som begär att kvotinformationen ska fyllas i och hämtar sedan värdet från svarshuvudet.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Använda Node.js SDK

Gränssnittet `ContainerDefinition` från [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (se [den här snabbstarten](create-sql-api-nodejs.md) när det gäller dess användning) visar en `indexingPolicy` egenskap som låter dig ändra `indexingMode` och lägga till eller ta bort `includedPaths` och `excludedPaths`.

Hämta informationen i behållaren

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Ställa in indexeringsläget på konsekvent

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Lägg till inkluderad sökväg inklusive ett rumsligt index

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Lägga till utesluten bana

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Uppdatera behållaren med ändringar

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Om du vill spåra indexomvandlingsstatusen `populateQuotaInfo` för `true`en behållare skickar du `x-ms-documentdb-collection-index-transformation-progress` ett `RequestOptions` objekt som anger egenskapen till och hämtar sedan värdet från svarshuvudet.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk-v3"></a>Använda Python SDK V3

När du använder [Python SDK V3](https://pypi.org/project/azure-cosmos/) (se [den här snabbstarten](create-sql-api-python.md) när det gäller dess användning) hanteras behållarkonfigurationen som en ordlista. Från den här ordlistan är det möjligt att komma åt indexeringsprincipen och alla dess attribut.

Hämta informationen i behållaren

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Ställa in indexeringsläget på konsekvent

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definiera en indexeringsprincip med en inkluderad bana och ett rumsligt index

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definiera en indexeringsprincip med en utesluten bana

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Lägga till ett sammansatt index

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Uppdatera behållaren med ändringar

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="use-the-python-sdk-v4"></a>Använda Python SDK V4

När du använder [Python SDK V4](https://pypi.org/project/azure-cosmos/)hanteras behållarkonfigurationen som en ordlista. Från den här ordlistan är det möjligt att komma åt indexeringsprincipen och alla dess attribut.

Hämta informationen i behållaren

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Ställa in indexeringsläget på konsekvent

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Definiera en indexeringsprincip med en inkluderad bana och ett rumsligt index

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definiera en indexeringsprincip med en utesluten bana

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Lägga till ett sammansatt index

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

Uppdatera behållaren med ändringar

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Översikt över indexering](index-overview.md)
- [Indexeringsprincip](index-policy.md)
