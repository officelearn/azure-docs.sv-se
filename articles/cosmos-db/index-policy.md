---
title: Azure Cosmos DB indexeringsprinciper
description: Lär dig mer om att konfigurera och ändra standard indexeringspolicy för automatisk indexering och bättre prestanda i Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 67bc3076be91ade140b39b7dd8037299902546a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61046326"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexeringsprinciper i Azure Cosmos DB

I Azure Cosmos DB har en indexeringsprincip som bestämmer hur behållarens objekt ska indexeras i behållare. Standard indexeringspolicy för nyligen skapade behållare index varje egenskap för varje artikel, tillämpa intervallet index för en sträng eller en siffra, och spatialindex för alla GeoJSON-objekt i Skriv punkt. På så sätt kan du få hög frågeprestanda utan att behöva tänka indexering och indexhantering förskott.

I vissa situationer kan du åsidosätta detta automatiskt så att den bättre passar dina behov. Du kan anpassa indexeringsprincip för en behållare genom att ange dess *indexering läge*, och inkludera eller exkludera *egenskapen sökvägar*.

## <a name="indexing-mode"></a>Indexering läge

Azure Cosmos DB stöder två lägen för indexering:

- **Konsekvent**: Om en behållare indexeringsprincip anges för konsekvens, uppdateras indexet synkront när du skapar, uppdatera eller ta bort objekt. Det innebär att dina skrivskyddade frågor konsekvens är den [konsekvens som konfigurerats för kontot](consistency-levels.md).

- **Ingen**: Om en behållare indexeringsprincip har angetts till None, inaktiveras effektivt indexering på behållaren. Detta används vanligtvis när en behållare används som en ren nyckel / värdelagring utan att behöva sekundära index. Det kan också snabbare bulk infogningsåtgärder.

## <a name="including-and-excluding-property-paths"></a>Inkludera och exkludera egenskapen sökvägar

En anpassad indexeringsprincip kan ange egenskapen sökvägar som är uttryckligen inkluderas eller uteslutas från indexering. Du kan minska mängden lagringsutrymme som används av din behållare och förbättra svarstiden för skrivåtgärder genom att optimera antalet sökvägar som indexeras. Dessa sökvägar definieras efter [den metod som beskrivs i avsnittet indexering översikt](index-overview.md#from-trees-to-property-paths) med följande tillägg:

- en sökväg som leder till ett skalärt värde (sträng eller en siffra) slutar med `/?`
- element från en matris behandlas tillsammans via den `/[]` notation (i stället för `/0`, `/1` osv.)
- den `/*` med jokertecken kan användas för att matcha alla element under noden

Med det här exemplet igen:

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

- den `headquarters`'s `employees` sökvägen är `/headquarters/employees/?`
- den `locations`' `country` sökvägen är `/locations/[]/country/?`
- sökvägen till något under `headquarters` är `/headquarters/*`

En sökväg uttryckligen tas med i indexprincip finns det också att definiera vilka typer av index som ska kopplas till denna sökväg och för varje Indextyp av måste datatypen indexet gäller för:

| Indextyp | Tillåtna mål-datatyper |
| --- | --- |
| Intervall | Strängen eller talvärdet |
| Spatial | Point, LineString eller Polygon |

Vi kan till exempel innehålla den `/headquarters/employees/?` sökvägen och ange att en `Range` index ska tillämpas på samma sökväg för både `String` och `Number` värden.

### <a name="includeexclude-strategy"></a>Inkludera/exkludera strategi

Alla indexeringsprincip måste inkludera rotsökvägen `/*` som en inkluderad eller en Undantagen sökväg.

- Inkludera rotsökvägen för att selektivt undanta sökvägar som inte behöver indexeras. Detta är den rekommenderade metoden eftersom det kan användas av Azure Cosmos DB proaktivt Indexera nya egenskaper som kan läggas till i din modell.
- Undanta rotsökvägen för att selektivt ta med sökvägar som måste indexeras.

Se [i det här avsnittet](how-to-manage-indexing-policy.md#indexing-policy-examples) för indexering av exempel på.

## <a name="modifying-the-indexing-policy"></a>Ändra indexprincip

Indexeringsprincip för en behållare kan uppdateras när som helst [med hjälp av Azure-portalen eller en av de stödda SDK: erna](how-to-manage-indexing-policy.md). En uppdatering av indexprincip utlöser en omvandling från det gamla indexet till den nya, vilket utförs och på plats (så att inget ytterligare utrymme förbrukas under åtgärden). Gamla principens index omvandlas effektivt till den nya principen utan att påverka tillgängligheten för skrivning eller dataflödet som tillhandahållits för behållaren. Index omvandlingen är en asynkron åtgärd och den tid det tar för att slutföra beror på det etablerade dataflödet, antalet objekt och deras storlek. 

> [!NOTE]
> Medan omindexering pågår, frågor kan inte returnera alla matchande resultat och gör det utan att returnera eventuella fel. Det innebär att frågeresultatet inte kanske är konsekvent tills indexet omvandlingen har slutförts. Det är möjligt att spåra förloppet för index omvandling [med någon av de SDK: erna](how-to-manage-indexing-policy.md).

Om den nya indexprincip läge är inställt på konsekvens, kan inga andra indexering principändringen tillämpas när indexet transformeringen pågår. En pågående index omvandling kan avbrytas genom att ange den indexprincip läge None (som kommer omedelbart att släppa indexet).

## <a name="indexing-policies-and-ttl"></a>Principer för indexering och TTL

Den [Time-to-Live (TTL) funktionen](time-to-live.md) kräver indexering för att vara aktiv för behållaren som den är påslagen. Detta innebär att:

- Det går inte att aktivera TTL-värdet från en behållare där indexering läge är inställt på Ingen,
- Det går inte att ange läget som indexering NONE för en behållare där TTL är aktiverad.

Du kan använda en indexeringsprincip med scenarier där ingen egenskapssökväg måste indexeras, men TTL-värde krävs:

- en indexering inställd på konsekvens, och
- Ingen inkluderade sökväg och
- `/*` eftersom den enda exkluderade sökvägen.

## <a name="obsolete-attributes"></a>Föråldrade attribut

När du arbetar med principer för indexering, du kan stöta på följande attribut som är nu föråldrade:

- `automatic` är ett booleskt värde som definierats i roten av en princip för indexering. Den ignoreras nu och kan ställas in på `true`, när du använder verktyget kräver den.
- `precision` ett tal har definierats på nivån index för sökvägar som tas med. Den ignoreras nu och kan ställas in på `-1`, när du använder verktyget kräver den.
- `hash` är en typ av index som har ersatts av typ av intervall.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Indexering, översikt](index-overview.md)
- [Så här hanterar du indexeringsprincip](how-to-manage-indexing-policy.md)
