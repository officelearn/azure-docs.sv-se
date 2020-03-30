---
title: Azure Cosmos DB-indexeringsprinciper
description: Lär dig hur du konfigurerar och ändrar standardindexeringsprincipen för automatisk indexering och högre prestanda i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292072"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexeringsprinciper i Azure Cosmos DB

I Azure Cosmos DB har varje behållare en indexeringsprincip som bestämmer hur behållarens objekt ska indexeras. Standardindexeringsprincipen för nyskapade behållare indexerar varje egenskap för varje objekt, verkställer intervallindex för alla strängar eller tal och rumsliga index för alla GeoJSON-objekt av typen Point. På så sätt kan du få hög frågeprestanda utan att behöva tänka på indexering och indexhantering i förväg.

I vissa fall kan det vara bra att åsidosätta det här automatiska beteendet så att det passar dina behov bättre. Du kan anpassa en behållares indexeringsprincip genom att ange *indexeringsläge*och inkludera eller utesluta *egenskapssökvägar*.

> [!NOTE]
> Metoden för att uppdatera indexeringsprinciper som beskrivs i den här artikeln gäller endast Azure Cosmos DB:s SQL-API (Core).

## <a name="indexing-mode"></a>Indexeringsläge

Azure Cosmos DB stöder två indexeringslägen:

- **Konsekvent**: Indexet uppdateras synkront när du skapar, uppdaterar eller tar bort objekt. Det innebär att konsekvensen i dina läsfrågor blir den [konsekvens som konfigurerats för kontot](consistency-levels.md).
- **Ingen**: Indexeringen är inaktiverad på behållaren. Detta används ofta när en behållare används som ett rent nyckelvärdeslager utan behov av sekundära index. Det kan också användas för att förbättra prestanda för bulkåtgärder. När massåtgärderna har slutförts kan indexläget ställas in på Konsekvent och sedan övervakas med [indextransformationsprogressen](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) tills det är klart.

> [!NOTE]
> Azure Cosmos DB stöder också ett lazy-indexeringsläge. Lazy indexering utför uppdateringar av indexet på en mycket lägre prioritet nivå när motorn inte gör något annat arbete. Detta kan resultera i **inkonsekventa eller ofullständiga** frågeresultat. Om du planerar att fråga en Cosmos-behållare bör du inte välja lat indexering.

Som standard är indexeringsprincipen inställd på `automatic`. Det uppnås genom att `automatic` ange egenskapen i `true`indexeringsprincipen till . Om du `true` anger den här egenskapen så att Azure CosmosDB automatiskt kan indexera dokument när de skrivs.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Inklusive och exklusive egenskapssökvägar

En anpassad indexeringsprincip kan ange egenskapssökvägar som uttryckligen inkluderas eller utesluts från indexeringen. Genom att optimera antalet sökvägar som indexeras kan du sänka mängden lagringsutrymme som används av behållaren och förbättra svarstiden för skrivåtgärder. Dessa sökvägar definieras enligt [den metod som beskrivs i avsnittet indexeringsöversikt](index-overview.md#from-trees-to-property-paths) med följande tillägg:

- en bana som leder till ett skalärvärde (sträng eller tal) slutar med`/?`
- element från en matris adresseras tillsammans genom notationen `/[]` (i stället för `/0`, `/1` etc.)
- `/*` jokertecknet kan användas för att matcha alla element under noden

Med samma exempel igen:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- 's `headquarters` `employees` väg är`/headquarters/employees/?`

- " `locations` `country` sökvägen är`/locations/[]/country/?`

- vägen till något `headquarters` under är`/headquarters/*`

Vi kan till exempel `/headquarters/employees/?` inkludera sökvägen. Den här sökvägen skulle säkerställa att vi indexerar egenskapen anställda men inte indexerar ytterligare kapslade JSON i den här egenskapen.

## <a name="includeexclude-strategy"></a>Inkludera/exkludera strategi

Alla indexeringsprinciper måste `/*` inkludera rotsökvägen som antingen en inkluderad eller en utesluten sökväg.

- Inkludera rotsökvägen för att selektivt utesluta sökvägar som inte behöver indexeras. Det här är den rekommenderade metoden eftersom det låter Azure Cosmos DB proaktivt indexera alla nya egenskaper som kan läggas till i din modell.
- Uteslut rotsökvägen för att selektivt inkludera sökvägar som måste indexeras.

- För banor med vanliga tecken som innehåller: alfanumeriska tecken och _ (understreck) behöver du inte fly från bansträngen runt dubbla citattecken (till exempel "/bana/?"). För banor med andra specialtecken måste du fly från sökvägssträngen\"runt\"dubbla citattecken (till exempel "/ path-abc /?"). Om du förväntar dig specialtecken i din väg kan du undkomma alla vägar för säkerhet. Funktionellt det gör ingen skillnad om du fly varje väg Vs bara de som har specialtecken.

- Systemegenskapen `_etag` är undantagen från indexering som standard, såvida inte etag läggs till i den inkluderade sökvägen för indexering.

- Om indexeringsläget är inställt på `id` `_ts` **konsekvent**, systemegenskaperna och indexeras automatiskt.

När du inkluderar och utesluter sökvägar kan du stöta på följande attribut:

- `kind`kan vara `range` `hash`antingen eller . Intervallindexfunktionen ger alla funktioner i ett hash-index, så vi rekommenderar att du använder ett intervallindex.

- `precision`är ett tal som definierats på indexnivå för inkluderade sökvägar. Ett värde `-1` anger maximal precision. Vi rekommenderar att du `-1`alltid ställer in det här värdet på .

- `dataType`kan vara `String` `Number`antingen eller . Detta anger vilka typer av JSON-egenskaper som ska indexeras.

När dessa egenskaper inte har angetts har de följande standardvärdena:

| **Egenskapsnamn**     | **Standardvärdet** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` och `Number` |

I [det här avsnittet](how-to-manage-indexing-policy.md#indexing-policy-examples) finns exempel på indexering av principer för att inkludera och utesluta banor.

## <a name="spatial-indexes"></a>Rumsliga index

När du definierar en rumslig sökväg i ```type``` indexeringsprincipen bör du definiera vilket index som ska användas på sökvägen. Möjliga typer för rumsliga index är:

* Punkt

* Polygon

* MultiPolygon (multipolygon)

* LineString (Radsträng)

Azure Cosmos DB, som standard, kommer inte att skapa några rumsliga index. Om du vill använda rumsliga SQL-inbyggda funktioner bör du skapa ett rumsligt index på de önskade egenskaperna. I [det här avsnittet](geospatial.md) finns exempel på indexering av principer för att lägga till rumsliga index.

## <a name="composite-indexes"></a>Sammansatta index

Frågor som har `ORDER BY` en sats med två eller flera egenskaper kräver ett sammansatt index. Du kan också definiera ett sammansatt index för att förbättra prestanda för många likhets- och intervallfrågor. Som standard definieras inga sammansatta index, så du bör [lägga till sammansatta index](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) efter behov.

När du definierar ett sammansatt index anger du:

- Två eller flera egenskapssökvägar. Den ordning i vilken egenskapssökvägar definieras är viktiga.

- Ordningen (stigande eller fallande).

> [!NOTE]
> När du lägger till ett sammansatt index används befintliga intervallindex tills det nya sammansatta indextillägget är klart. När du lägger till ett sammansatt index kanske du inte omedelbart följer prestandaförbättringar. Det är möjligt att spåra förloppet för indexomvandlingen [med hjälp av en av SDK:erna](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>ORDER BY-frågor om flera egenskaper:

Följande överväganden används när kompositindex används för `ORDER BY` frågor med en sats med två eller flera egenskaper:

- Om kompositindexsökvägarna inte matchar sekvensen `ORDER BY` för egenskaperna i satsen kan kompositindexet inte stödja frågan.

- Ordningen på sammansatta indexsökvägar (stigande eller fallande) ska också matcha `order` i `ORDER BY` satsen.

- Kompositindexet stöder `ORDER BY` också en sats med motsatt ordning på alla banor.

Tänk på följande exempel där ett sammansatt index definieras på egenskapers namn, ålder och _ts:

| **Sammansatt index**     | **Exempel `ORDER BY` på fråga**      | **Stöds av sammansattindex?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Du bör anpassa indexeringsprincipen så `ORDER BY` att du kan hantera alla nödvändiga frågor.

### <a name="queries-with-filters-on-multiple-properties"></a>Frågor med filter för flera egenskaper

Om en fråga har filter på två eller flera egenskaper kan det vara bra att skapa ett sammansatt index för dessa egenskaper.

Tänk dig till exempel följande fråga som har ett likhetsfilter på två egenskaper:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Den här frågan blir effektivare, tar mindre tid och förbrukar färre RU: s, om den kan utnyttja ett sammansatt index på (namn ASC, ålder ASC).

Frågor med intervallfilter kan också optimeras med ett sammansatt index. Frågan kan dock bara ha ett enda intervallfilter. Intervallfilter `>`inkluderar `<` `<=`, `>=`, `!=`, och . Intervallfiltret ska definieras sist i kompositindexet.

Tänk på följande fråga med både likhets- och intervallfilter:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Den här frågan blir effektivare med ett sammansatt index på (namn ASC, ålder ASC). Frågan skulle dock inte använda ett sammansatt index på (ålder ASC, namn ASC) eftersom likhetsfiltren måste definieras först i kompositindexet.

Följande överväganden används när kompositindex skapas för frågor med filter på flera egenskaper

- Egenskaperna i frågans filter ska matcha egenskaperna i kompositindex. Om en egenskap finns i kompositindexet men inte ingår i frågan som ett filter används inte kompositindexet.
- Om en fråga har ytterligare egenskaper i filtret som inte har definierats i ett sammansatt index används en kombination av sammansatta index och intervallindex för att utvärdera frågan. Detta kommer att kräva färre RU: s än att uteslutande använda intervallindex.
- Om en egenskap har`>`ett `<` `<=`intervallfilter ( , , , `>=`eller `!=`) ska den här egenskapen definieras sist i kompositindexet. Om en fråga har mer än ett intervallfilter används inte kompositindexet.
- När du skapar ett sammansatt index för `ORDER` att optimera frågor med flera filter påverkar kompositindexet inte resultatet. Den här egenskapen är valfri.
- Om du inte definierar ett sammansatt index för en fråga med filter på flera egenskaper lyckas frågan fortfarande. RU-kostnaden för frågan kan dock minskas med ett sammansatt index.

Tänk på följande exempel där ett sammansatt index definieras på egenskapers namn, ålder och tidsstämpel:

| **Sammansatt index**     | **Exempel på fråga**      | **Stöds av sammansattindex?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Frågor med ett filter och en ORDER BY-sats

Om en fråga filtrerar på en eller flera egenskaper och har olika egenskaper i ORDER BY-satsen kan det vara bra att lägga till egenskaperna i filtret i `ORDER BY` satsen.

Genom att till exempel lägga till egenskaperna i filtret i satsen ORDER BY kan följande fråga skrivas om för att utnyttja ett sammansatt index:

Fråga med intervallindex:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Fråga med sammansatt index:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Samma mönster- och frågeoptimeringar kan generaliseras för frågor med flera likhetsfilter:

Fråga med intervallindex:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Fråga med sammansatt index:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

Följande överväganden används när du skapar sammansatta index för `ORDER BY` att optimera en fråga med ett filter och en sats:

* Om frågefiltren för egenskaper ska dessa `ORDER BY` inkluderas först i satsen.
* Om du inte definierar ett sammansatt index för en fråga `ORDER BY` med ett filter på en egenskap och en separat sats med en annan egenskap, lyckas frågan fortfarande. RU-kostnaden för frågan kan dock minskas med ett sammansatt index, `ORDER BY` särskilt om egenskapen i satsen har en hög kardinalitet.
* Alla överväganden för att skapa `ORDER BY` sammansatta index för frågor med flera egenskaper samt frågor med filter på flera egenskaper gäller fortfarande.


| **Sammansatt index**                      | **Exempel `ORDER BY` på fråga**                                  | **Stöds av sammansattindex?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Ändra indexeringsprincipen

En behållares indexeringsprincip kan uppdateras när som helst [med hjälp av Azure-portalen eller en av de SDK-filer som stöds](how-to-manage-indexing-policy.md). En uppdatering av indexeringsprincipen utlöser en omvandling från det gamla indexet till det nya, som utförs online och på plats (så inget ytterligare lagringsutrymme förbrukas under åtgärden). Den gamla principens index omvandlas effektivt till den nya principen utan att påverka skrivtillgängligheten eller dataflödet som etablerats på behållaren. Indexomvandling är en asynkron åtgärd, och den tid det tar att slutföra beror på det etablerade dataflödet, antalet artiklar och deras storlek.

> [!NOTE]
> När du lägger till ett intervall eller ett rumsligt index kanske frågor inte returnerar alla matchande resultat, och det gör du utan att returnera några fel. Det innebär att frågeresultaten kanske inte är konsekventa förrän indexomvandlingen är klar. Det är möjligt att spåra förloppet för indexomvandlingen [med hjälp av en av SDK:erna](how-to-manage-indexing-policy.md).

Om den nya indexeringsprincipens läge är inställt på Konsekvent kan ingen annan indexeringsprincipändring tillämpas medan indexomvandlingen pågår. En löpande indexomvandling kan avbrytas genom att indexeringsprincipens läge ställs in på Ingen (vilket omedelbart kommer att släppa indexet).

## <a name="indexing-policies-and-ttl"></a>Indexeringsprinciper och TTL

Funktionen [Time-to-Live (TTL)](time-to-live.md) kräver att indexeringen är aktiv på behållaren som den är aktiverad. Detta innebär att:

- det inte är möjligt att aktivera TTL på en behållare där indexeringsläget är inställt på Ingen,
- Det går inte att ställa in indexeringsläget till Ingen på en behållare där TTL är aktiverat.

För scenarier där ingen egenskapssökväg behöver indexeras, men TTL krävs, kan du använda en indexeringsprincip med:

- ett indexeringsläge inställt på Konsekvent, och
- ingen inkluderad sökväg, och
- `/*`som den enda uteslutna banan.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Översikt över indexering](index-overview.md)
- [Så här hanterar du indexeringsprincipen](how-to-manage-indexing-policy.md)
