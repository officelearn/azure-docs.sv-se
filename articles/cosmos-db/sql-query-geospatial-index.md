---
title: Indexera geospatiala data med Azure Cosmos DB
description: Indexera spatialdata med Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566379"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexera geospatiala data med Azure Cosmos DB

Vi utformade Azure Cosmos DB databas motorn så att den verkligen är schema-oberoende och ger första klass stöd för JSON. Den Write-optimerade databas motorn i Azure Cosmos DB internt förstår spatialdata som representeras i standarden för polyjson.

I en kortfattat så Jenkins projiceras geometrin från Geodetic-koordinater till ett 2D-plan och delas sedan progressivt i cellerna med hjälp av en **quadtree**. Dessa celler mappas till 1D baserat på cellens position inom en **Fyllnings kurva för Hilbert utrymme**, som bevarar platsens plats. När plats data indexeras går de igenom en process som kallas **mosaik**, det vill säga att alla celler som korsar en plats identifieras och lagras som nycklar i Azure Cosmos DB indexet. När en fråga körs argument som punkter och polygoner är också fasetterade för att extrahera de relevanta cellintervall ID och sedan används för att hämta data från indexet.

Om du anger en indexerings princip som innehåller rums index för/* (alla sökvägar), indexeras alla data som finns i behållaren för effektiva rums frågor.

> [!NOTE]
> Azure Cosmos DB stöder indexering av punkter, lin Est rings, polygoner och multipolygoner
>
>

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
>
>
>

Du kan också [ändra indexerings principen](how-to-manage-indexing-policy.md) med hjälp av Azure CLI, POWERSHELL eller SDK.

## <a name="next-steps"></a>Nästa steg

Bredvid nu när du har lärt dig hur du kommer igång med geospatialt stöd i Azure Cosmos DB, kan du:

* Läs mer om [Azure Cosmos DB fråga](sql-query-getting-started.md)
* Lär dig mer om att [fråga spatialdata med Azure Cosmos DB](sql-query-geospatial-query.md)
* Läs mer om [geospatiala data om geospatiala och geospatiala JSON-platser i Azure Cosmos DB](sql-query-geospatial-intro.md)