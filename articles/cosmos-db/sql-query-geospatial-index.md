---
title: Indexera geospatiala data med Azure Cosmos DB
description: Indexera rumsliga data med Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137911"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexera geospatiala data med Azure Cosmos DB

Vi har utformat Azure Cosmos DB:s databasmotor för att verkligen vara schemaoberoende och ge förstklassigt stöd för JSON. Den skrivoptimerade databasmotorn för Azure Cosmos DB förstår inbyggt rumsliga data som representeras i GeoJSON-standarden.

I ett nötskal projiceras geometrin från geodetiska koordinater på ett 2D-plan och delas sedan gradvis in i celler med hjälp av ett **quadtree**. Dessa celler mappas till 1D baserat på cellens placering i en **Hilbert utrymme fyllning kurva**, som bevarar ort punkter. Dessutom när platsdata indexeras, går den igenom en process som kallas **tessellation**, det vill ha alla celler som skär en plats identifieras och lagras som nycklar i Azure Cosmos DB-index. Vid frågetid är argument som punkter och polygoner också tessellated att extrahera relevanta cell-ID-områden och sedan användas för att hämta data från indexet.

Om du anger en indexeringsprincip som innehåller spatialt index för /* (alla sökvägar) indexeras alla data som hittas i behållaren för effektiva rumsliga frågor.

> [!NOTE]
> Azure Cosmos DB stöder indexering av poäng, linjesträngningar, polygoner och multipolygoner
>
>

## <a name="modifying-geospatial-data-type"></a>Ändra geospatial datatyp

I behållaren `geospatialConfig` anger anger anger du hur geospatiala data ska indexeras. Du bör `geospatialConfig` ange en per behållare: geografi eller geometri. Om inget anges `geospatialConfig` kommer den som standard att vara geografidatatypen. När du `geospatialConfig`ändrar indexeras alla befintliga geospatiala data i behållaren.

> [!NOTE]
> Azure Cosmos DB stöder för närvarande ändringar av geospatialConfig i .NET SDK endast i version 3.6 och högre.
>

Här är ett exempel på hur du ändrar den geospatiala datatypen till genom att `geometry` ange egenskapen `geospatialConfig` och lägga till en **boundingBox:**

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

## <a name="geography-data-indexing-examples"></a>Exempel på indexering av geografidata

Följande JSON-kodavsnitt visar en indexeringsprincip med **geography** rumslig indexering aktiverad för geografidatatypen. Den gäller för rumsliga data med geografidatatypen och indexerar alla GeoJSON Point, Polygon, MultiPolygon eller LineString som finns i dokument för rumsliga frågor. Om du ändrar indexeringsprincipen med Hjälp av Azure-portalen kan du ange följande JSON för indexeringsprincip för att aktivera spatial indexering på din behållare:

**Containerindexeringsprincip JSON med geografisk rumslig indexering**

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
> Om geojson-värdet för platsen i dokumentet är felaktigt eller ogiltigt, indexeras det inte för rumsliga frågor. Du kan validera platsvärden med hjälp av ST_ISVALID och ST_ISVALIDDETAILED.

Du kan också [ändra indexeringsprincipen](how-to-manage-indexing-policy.md) med Hjälp av Azure CLI, PowerShell eller valfri SDK.

## <a name="geometry-data-indexing-examples"></a>Exempel på indexering av geometridata

Med **geometridatatypen,** som liknar geografidatatypen, måste du ange relevanta sökvägar och typer som ska indexeras. Dessutom måste du också `boundingBox` ange en inom indexeringsprincipen för att ange önskat område som ska indexeras för den specifika sökvägen. Varje geospatial sökväg`boundingBox`kräver sin egen .

Markeringsramen består av följande egenskaper:

- **xmin**: den minsta indexerade x-koordinaten
- **ymin**: den minsta indexerade y-koordinaten
- **xmax**: den maximala indexerade x-koordinaten
- **ymax:** den maximala indexerade y-koordinaten

En begränsningsram krävs eftersom geometriska data upptar ett plan som kan vara oändligt. Rumsliga index kräver dock ett begränsat utrymme. För **geografidatatypen** är jorden gränsen och du behöver inte ange en markeringsram.

Du bör skapa en begränsningsram som innehåller alla (eller de flesta) av dina data. Endast åtgärder som beräknas på objekt som är helt inuti markeringsramen kan använda det rumsliga indexet. Du bör inte göra begränsningsramen betydligt större än nödvändigt eftersom detta påverkar frågeprestanda negativt.

Här är ett exempel indexeringsprincip som indexerar **geometridata** med **geospatialConfig** inställd `geometry`på:

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

Ovanstående indexeringsprincip har en **boundingBox** på (-10, 10) för x-koordinater och (-20, 20) för y-koordinater. Behållaren med ovanstående indexeringsprincip indexerar alla punkter, polygoner, multipolygoner och radsträngar som är helt inom den här regionen.

> [!NOTE]
> Om du försöker lägga till en indexeringsprincip `geography` med en **boundingBox** i en behållare med datatyp misslyckas den. Du bör ändra behållarens **geospatialConfig** vara `geometry` innan du lägger till en **boundingBox**. Du kan lägga till data och ändra resten av indexeringsprincipen (till exempel sökvägar och typer) antingen före eller efter att du har valt den geospatiala datatypen för behållaren.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kommer igång med geospatial support i Azure Cosmos DB kan du nästa du:

* Läs mer om [Azure Cosmos DB Query](sql-query-getting-started.md)
* Läs mer om [att fråga spatialdata med Azure Cosmos DB](sql-query-geospatial-query.md)
* Läs mer om [geospatial- och GeoJSON-platsdata i Azure Cosmos DB](sql-query-geospatial-intro.md)