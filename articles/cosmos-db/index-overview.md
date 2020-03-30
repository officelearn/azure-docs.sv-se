---
title: Indexering i Azure Cosmos DB
description: Förstå hur indexering fungerar i Azure Cosmos DB, olika typer av index som Intervall, Spatial, sammansatta index som stöds.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873632"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexering i Azure Cosmos DB – Översikt

Azure Cosmos DB är en schemaasantisk databas som gör att du kan iterera på ditt program utan att behöva hantera schema- eller indexhantering. Som standard indexerar Azure Cosmos DB automatiskt varje egenskap för alla objekt i [din behållare](databases-containers-items.md#azure-cosmos-containers) utan att behöva definiera något schema eller konfigurera sekundära index.

Målet med den här artikeln är att förklara hur Azure Cosmos DB indexerar data samt hur det använder index för att förbättra frågeprestanda. Vi rekommenderar att du går igenom det här avsnittet innan du utforskar hur [du anpassar indexeringsprinciper](index-policy.md).

## <a name="from-items-to-trees"></a>Från objekt till träd

Varje gång ett objekt lagras i en behållare projiceras dess innehåll som ett JSON-dokument och konverteras sedan till en trädrepresentation. Vad det betyder är att varje egenskap för objektet får representeras som en nod i ett träd. En pseudorotnod skapas som överordnad för alla egenskaper på den första nivån för objektet. Lövnoderna innehåller de faktiska skalärvärden som bärs av ett objekt.

Tänk på det här objektet som ett exempel:

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

Det skulle representeras av följande träd:

![Föregående objekt som representeras som ett träd](./media/index-overview/item-as-tree.png)

Observera hur matriser kodas i trädet: varje post i en matris får en mellanliggande nod märkt med indexet för den posten i matrisen (0, 1 osv.).

## <a name="from-trees-to-property-paths"></a>Från träd till egendomsvägar

Anledningen till att Azure Cosmos DB omvandlar objekt till träd är att det gör att egenskaper kan refereras av deras sökvägar i dessa träd. För att hämta sökvägen för en egenskap kan vi korsa trädet från rotnoden till den egenskapen och sammanfoga etiketterna för varje korsad nod.

Här är sökvägarna för varje egenskap från det exempelobjekt som beskrivs ovan:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

När ett objekt skrivs indexerar Azure Cosmos DB effektivt varje egenskaps sökväg och motsvarande värde.

## <a name="index-kinds"></a>Index sorter

Azure Cosmos DB stöder för närvarande tre typer av index.

### <a name="range-index"></a>Intervallindex

**Intervallindex** baseras på en ordnad trädliknande struktur. Områdesindexsdomen används för:

- Jämställdhetsfrågor:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Likhetsmatchning för ett matriselement
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Intervallfrågor:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (fungerar `>`för `<` `>=`, `<=` `!=`, , , )

- Kontroll av förekomsten av en fastighet:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Strängprefixmatchningar (INNES-nyckelordet utnyttjar inte intervallindexet):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`Frågor:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`Frågor:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Intervallindex kan användas på skalärvärden (sträng eller tal).

### <a name="spatial-index"></a>Rumsligt index

**Rumsliga** index möjliggör effektiva frågor om geospatiala objekt som - punkter, linjer, polygoner och multipolygon. Dessa frågor använder ST_DISTANCE, ST_WITHIN, ST_INTERSECTS nyckelord. Följande är några exempel som använder rumslig index typ:

- Geospatiala avståndsfrågor:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geospatial inom frågor:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Geospatiala intersect frågor:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Rumsliga index kan användas på korrekt formaterade [GeoJSON-objekt.](geospatial.md) Punkter, LineStrings, Polygoner och MultiPolygons stöds för närvarande.

### <a name="composite-indexes"></a>Sammansatta index

**Sammansatta** index ökar effektiviteten när du utför operationer på flera fält. Den sammansatta index typen används för:

- `ORDER BY`frågor om flera egenskaper:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Frågor med ett `ORDER BY`filter och . Dessa frågor kan använda ett sammansatt index om `ORDER BY` filteregenskapen läggs till i satsen.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Frågor med ett filter på två eller flera egenskaper där minst en egenskap är ett likhetsfilter

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Så länge ett filter predikat använder på av index slag, kommer frågemotorn utvärdera det först innan du skannar resten. Om du till exempel har en SQL-fråga som`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Ovanstående fråga filtreras först efter poster där förnamn = "Andrew" med hjälp av indexet. Det passerar sedan alla firstName = "Andrew" poster genom en efterföljande pipeline för att utvärdera CONTAINS-filtret predikat.

* Du kan snabba upp frågor och undvika fullständiga behållarsökningar när du använder funktioner som inte använder indexet (t.ex. innehåller) genom att lägga till ytterligare filter predikater som använder indexet. Ordningen på filtersatser är inte viktigt. Frågemotorn kommer att ta reda på vilka predikater som är mer selektiva och köra frågan därefter.


## <a name="querying-with-indexes"></a>Fråga med index

De sökvägar som extraheras vid indexering av data gör det enkelt att slå upp indexet när du bearbetar en fråga. Genom att `WHERE` matcha satsen för en fråga med listan över indexerade sökvägar är det möjligt att identifiera de objekt som matchar fråge predikatet mycket snabbt.

Tänk till exempel på `SELECT location FROM location IN company.locations WHERE location.country = 'France'`följande fråga: . Frågan predikat (filtrering på objekt, där någon plats har "Frankrike" som sitt land) skulle matcha sökvägen markerad i rött nedan:

![Matcha en viss bana i ett träd](./media/index-overview/matching-path.png)

> [!NOTE]
> En `ORDER BY` sats som order av en enskild egenskap behöver *alltid* ett intervallindex och misslyckas om sökvägen som refereras till inte har någon. På samma `ORDER BY` sätt behöver en fråga som order efter flera egenskaper *alltid* behöver ett sammansatt index.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Indexeringsprincip](index-policy.md)
- [Så här hanterar du indexeringsprincipen](how-to-manage-indexing-policy.md)
