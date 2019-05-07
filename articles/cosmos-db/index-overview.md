---
title: Indexering i Azure Cosmos DB
description: Förstå hur indexering fungerar i Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 44706e5ebe2442dcb45dfc45e2c322938cf7dca9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068647"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexering i Azure Cosmos DB - översikt

Azure Cosmos DB är en schemaoberoende databas där du kan iterera på ditt program utan att behöva bry dig om schema- eller indexhantering. Azure Cosmos DB indexerar automatiskt varje egenskap för alla objekt i din [behållare](databases-containers-items.md#azure-cosmos-containers) utan att behöva definiera ett schema eller konfigurera sekundära index.

Målet med den här artikeln är att förklara hur Azure Cosmos DB indexerar data och hur den används index för att förbättra frågeprestanda. Det rekommenderas att gå igenom det här avsnittet innan du börjar utforska hur du anpassar [indexeringsprinciper](index-policy.md).

## <a name="from-items-to-trees"></a>Från objekt att träd

Varje gång ett objekt lagras i en behållare, dess innehåll är planerat som ett JSON-dokument och sedan konverteras till en trädrepresentation. Det innebär att varje egenskap om det här objektet hämtar representeras som en nod i ett träd. En pseudo-rotnod skapas som en överordnad till alla första nivån egenskaperna för objektet. Lövnoderna innehåller de faktiska skalära värden som utförs av ett objekt.

Överväg det här objektet som ett exempel:

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

Det representeras av följande träd:

![Föregående objekt visas som ett träd](./media/index-overview/item-as-tree.png)

Observera hur matriser kodas i trädet: hämtar en mellanliggande nod som är märkt med index för den posten inom matrisen för varje post i en matris (0, 1 osv.).

## <a name="from-trees-to-property-paths"></a>Från träd till egenskapen sökvägar

Anledningen till varför Azure Cosmos DB omvandlar objekt till träd är eftersom den tillåter egenskaper refereras av deras sökvägar i dessa träd. För att hämta sökvägen för en egenskap, vi passerar trädet från rotnoden till egenskapen och sammanfoga etiketter för varje tvärgående nod.

Här följer sökvägarna för varje egenskap från exempel-objektet som beskrivs ovan:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

När ett objekt skrivs indexerar Azure Cosmos DB effektivt varje egenskap sökväg och dess motsvarande värde.

## <a name="index-kinds"></a>Index-typer

Azure Cosmos DB stöder för närvarande två typer av index:

Den **intervallet** index typ används till:

- likhetsfrågor: 

   ```sql SELECT * FROM container c WHERE c.property = 'value'```

- Omfångsfrågor: 

   ```sql SELECT * FROM container c WHERE c.property > 'value'``` (fungerar för `>`, `<`, `>=`, `<=`, `!=`)

- `ORDER BY` frågor:

   ```sql SELECT * FROM container c ORDER BY c.property```

- `JOIN` frågor: 

   ```sql SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'```

Intervallet index kan användas på skalära värden (sträng eller en siffra).

Den **spatial** index typ används till:

- geospatial avståndet frågor: 

   ```sql SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40```

- geospatial i frågor: 

   ```sql SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })```

Spatialindex kan användas på korrekt formaterad [GeoJSON](geospatial.md) objekt. Punkter, LineStrings och polygoner stöds för närvarande.

Den **sammansatta** index typ används till:

- `ORDER BY` frågor med flera egenskaper: 

   ```sql SELECT * FROM container c ORDER BY c.firstName, c.lastName```

## <a name="querying-with-indexes"></a>Frågor med index

Sökvägar som extraheras vid indexering av data gör det enkelt att söka efter indexet vid bearbetning av en fråga. Genom att matcha den `WHERE` -satsen för en fråga med i listan över indexerade sökvägar, är det möjligt att identifiera de objekt som matchar frågepredikatet mycket snabbt.

Tänk dig följande fråga: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Frågepredikatet (filtrering med objekt där vilken plats som har ”France” som dess land) matchar den sökväg som är markerat i rött nedan:

![Matchar en angiven sökväg i ett träd](./media/index-overview/matching-path.png)

> [!NOTE]
> En `ORDER BY` sats som grupperas efter en enskild egenskap *alltid* måste flera index- och kommer att misslyckas om sökvägen den refererar till inte har en. På samma sätt kan en multi `ORDER BY` fråga *alltid* måste ett sammansatt index.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Indexeringspolicy](index-policy.md)
- [Så här hanterar du indexeringsprincip](how-to-manage-indexing-policy.md)
