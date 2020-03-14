---
title: Indexera geospatiala data med Azure Cosmos DB
description: Indexera spatialdata med Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137911"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexera geospatiala data med Azure Cosmos DB

Vi utformade Azure Cosmos DB databas motorn så att den verkligen är schema-oberoende och ger första klass stöd för JSON. Den Write-optimerade databas motorn i Azure Cosmos DB internt förstår spatialdata som representeras i standarden för polyjson.

I en kortfattat så Jenkins projiceras geometrin från Geodetic-koordinater till ett 2D-plan och delas sedan progressivt i cellerna med hjälp av en **quadtree**. Dessa celler mappas till 1D baserat på cellens position inom en **Fyllnings kurva för Hilbert utrymme**, som bevarar platsens plats. När plats data indexeras går de igenom en process som kallas **mosaik**, det vill säga att alla celler som korsar en plats identifieras och lagras som nycklar i Azure Cosmos DB indexet. När en fråga körs argument som punkter och polygoner är också fasetterade för att extrahera de relevanta cellintervall ID och sedan används för att hämta data från indexet.

Om du anger en indexerings princip som innehåller rums index för/* (alla sökvägar), indexeras alla data som finns i behållaren för effektiva rums frågor.

> [!NOTE]
> Azure Cosmos DB stöder indexering av punkter, lin Est rings, polygoner och multipolygoner
>
>

## <a name="modifying-geospatial-data-type"></a>Ändra Geospatial datatyp

I din behållare anger `geospatialConfig` hur geospatiala data ska indexeras. Du bör ange ett `geospatialConfig` per container: geografi eller geometri. Om inget värde `geospatialConfig` anges används data typen geografi som standard. När du ändrar `geospatialConfig`kommer alla befintliga geospatiala data i behållaren att indexeras om.

> [!NOTE]
> Azure Cosmos DB stöder för närvarande endast ändringar i geospatialConfig i .NET SDK i version 3,6 och senare.
>

Här är ett exempel på hur du ändrar den geospatiala data typen till `geometry` genom att ange egenskapen `geospatialConfig` och lägga till en **boundingBox**:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Exempel på geografi data indexering

Följande JSON-kodfragment visar en indexerings princip där spatial indexering har Aktiver ATS för **geografi** data typen. Den är giltig för spatialdata med data typen geografi och kommer att indexera alla typer av JSON-punkter, polygoner, multipolygoner eller lin Est ring som finns i dokument för spatial fråga. Om du ändrar indexerings principen med hjälp av Azure Portal kan du ange följande JSON för indexerings principen för att aktivera rums indexering i din behållare:

**Behållar indexerings princip JSON med geografisk rums indexering**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Om platsen GeoJSON värde inom dokumentet är skadad eller ogiltig, få sedan inte indexera för spatial frågor. Du kan validera plats värden med hjälp av ST_ISVALID och ST_ISVALIDDETAILED.

Du kan också [ändra indexerings principen](how-to-manage-indexing-policy.md) med hjälp av Azure CLI, POWERSHELL eller SDK.

## <a name="geometry-data-indexing-examples"></a>Exempel på geometri data indexering

Med data typen **Geometry** , som liknar data typen geografi, måste du ange relevanta sökvägar och typer att indexera. Dessutom måste du också ange en `boundingBox` i indexerings principen för att ange det önskade områden som ska indexeras för den angivna sökvägen. Varje Geospatial sökväg kräver en egen`boundingBox`.

Avgränsnings rutan består av följande egenskaper:

- **xMin**: den minsta indexerade x-koordinaten
- **yMin**: den minsta indexerade y-koordinaten
- **Xmax**: den maximalt indexerade x-koordinaten
- **yMax**: den maximalt indexerade y-koordinaten

En avgränsnings ruta krävs eftersom geometriska data upptar ett plan som kan vara oändligt. Rums index kräver dock ett begränsat utrymme. För **geografi** data typen är jordens kant linjen och du behöver inte ange någon avgränsnings ruta.

Du bör skapa en avgränsnings ruta som innehåller alla (eller de flesta) data. Endast åtgärder som beräknas för de objekt som är helt inne i avgränsnings rutan kommer att kunna använda rums indexet. Du bör inte göra den begränsade avgränsnings rutan betydligt större än nödvändigt eftersom detta påverkar frågans prestanda negativt.

Här är ett exempel på en indexerings princip som indexerar **geometri** data med **geospatialConfig** inställt på `geometry`:

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
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Index principen ovan har en **boundingBox** av (-10, 10) för x-koordinater och (-20, 20) för y-koordinater. Behållaren med index principen ovan kommer att indexera alla punkter, polygoner, multipolygoner och lin Est rings som är helt inom den här regionen.

> [!NOTE]
> Om du försöker lägga till en indexerings princip med en **boundingBox** till en behållare med data typen `geography`, kommer den att Miss Förslut. Du bör ändra behållarens **geospatialConfig** till `geometry` innan du lägger till en **boundingBox**. Du kan lägga till data och ändra resten av din indexerings princip (t. ex. sökvägar och typer) antingen före eller efter att du väljer den geospatiala data typen för behållaren.

## <a name="next-steps"></a>Nästa steg

Bredvid nu när du har lärt dig hur du kommer igång med geospatialt stöd i Azure Cosmos DB, kan du:

* Läs mer om [Azure Cosmos DB fråga](sql-query-getting-started.md)
* Lär dig mer om att [fråga spatialdata med Azure Cosmos DB](sql-query-geospatial-query.md)
* Läs mer om [geospatiala data om geospatiala och geospatiala JSON-platser i Azure Cosmos DB](sql-query-geospatial-intro.md)