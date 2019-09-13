---
title: Indexering i Azure Cosmos DB
description: Förstå hur indexering fungerar i Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 4d961f8635a52a09011543b793ce8a87eaa4ea9e
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914200"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexering i Azure Cosmos DB – översikt

Azure Cosmos DB är en schema oberoende-databas som gör att du kan iterera i ditt program utan att behöva hantera schema-eller index hantering. Som standard indexerar Azure Cosmos DB automatiskt varje egenskap för alla objekt i din [behållare](databases-containers-items.md#azure-cosmos-containers) utan att behöva definiera något schema eller konfigurera sekundära index.

Målet med den här artikeln är att förklara hur Azure Cosmos DB indexerar data och hur de använder index för att förbättra frågans prestanda. Vi rekommenderar att du går igenom det här avsnittet innan du utforskar hur du anpassar [indexerings principer](index-policy.md).

## <a name="from-items-to-trees"></a>Från objekt till träd

Varje gång ett objekt lagras i en behållare projiceras innehållet som ett JSON-dokument och konverteras sedan till en träd representation. Det innebär att varje egenskap hos objektet visas som en nod i ett träd. En rotnod för pseudo skapas som överordnad till alla de första nivå egenskaperna för objektet. Löv noderna innehåller faktiska skalära värden som utförs av ett objekt.

Anta till exempel följande objekt:

```json
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
```

Den representeras av följande träd:

![Det föregående objektet representeras som ett träd](./media/index-overview/item-as-tree.png)

Observera hur matriser kodas i trädet: varje post i en matris får en mellanliggande nod som är märkt med indexet för posten i matrisen (0, 1 osv.).

## <a name="from-trees-to-property-paths"></a>Från träd till egenskaps Sök vägar

Anledningen till varför Azure Cosmos DB transformerar objekt i träd beror på att det tillåter att egenskaper refereras till av sina sökvägar i dessa träd. För att hämta sökvägen till en egenskap kan vi gå över trädet från rotnoden till den egenskapen och sammanfoga etiketterna för varje nod.

Här är Sök vägarna för varje egenskap från det exempel objekt som beskrivs ovan:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

När ett objekt skrivs, indexerar Azure Cosmos DB effektivt varje egenskaps bana och dess motsvarande värde.

## <a name="index-kinds"></a>Index typer

Azure Cosmos DB stöder för närvarande tre typer av index:

**Intervall** index typen används för:

- Likhets frågor:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

- Intervall frågor:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (fungerar för `>`, `<`, `>=` ,`<=`, )`!=`

- `ORDER BY`skickar

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`skickar

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Intervall index kan användas för skalära värden (sträng eller tal).

Typ av **rums** index används för:

- Geospatiala avstånds frågor: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geospatial inom frågor: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

Rums index kan användas på korrekt formaterade geospatiala [JSON](geospatial.md) -objekt. Punkter, lin Est rings, polygoner och multipolygoner stöds för närvarande.

Den **sammansatta** index typen används för:

- `ORDER BY`frågor om flera egenskaper:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Frågor med ett filter och `ORDER BY`. Dessa frågor kan använda ett sammansatt index om filter egenskapen har lagts till `ORDER BY` i-satsen.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Frågor med ett filter på två eller flera egenskaper där minst en egenskap är ett likhets filter

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

## <a name="querying-with-indexes"></a>Fråga med index

De sökvägar som extraherades vid indexering av data gör det enkelt att söka efter indexet när en fråga bearbetas. Genom att matcha `WHERE` -satsen i en fråga med listan över indexerade sökvägar är det möjligt att identifiera de objekt som matchar frågespråket mycket snabbt.

Överväg till exempel följande fråga: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Frågans predikat (filtrering av objekt, där alla platser har "Frankrike" som sitt land), matchar sökvägen som marker ATS i rött nedan:

![Matcha en angiven sökväg inom ett träd](./media/index-overview/matching-path.png)

> [!NOTE]
> En `ORDER BY` sats som sorteras efter en enskild egenskap behöver *alltid* ett intervall index och kommer att Miss betes om sökvägen den refererar till inte har en. På samma sätt behöver `ORDER BY` en fråga som order by flera egenskaper *alltid* ett sammansatt index.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Indexeringspolicy](index-policy.md)
- [Hantera indexerings principen](how-to-manage-indexing-policy.md)
