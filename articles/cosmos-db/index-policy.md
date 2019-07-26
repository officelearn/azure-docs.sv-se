---
title: Azure Cosmos DB indexerings principer
description: Lär dig hur du konfigurerar och ändrar standard indexerings principen för automatisk indexering och bättre prestanda i Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467882"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexerings principer i Azure Cosmos DB

I Azure Cosmos DB har varje behållare en indexerings princip som avgör hur behållarens objekt ska indexeras. Standard indexerings principen för nyskapade behållare indexerar varje egenskap för varje objekt, som framtvingar intervall index för valfri sträng eller siffra, och rums index för valfritt interjson-objekt av typen Point. På så sätt kan du få höga prestanda för frågor utan att behöva tänka på indexering och index hantering.

I vissa fall kanske du vill åsidosätta det automatiska beteendet så att det passar dina behov bättre. Du kan anpassa en behållares indexerings princip genom att ställa in dess *indexerings läge*och ta med eller undanta *egenskaps Sök vägar*.

> [!NOTE]
> Metoden för att uppdatera indexerings principer som beskrivs i den här artikeln gäller endast Azure Cosmos DB s SQL-API (Core).

## <a name="indexing-mode"></a>Indexerings läge

Azure Cosmos DB stöder två indexerings lägen:

- **Konsekvent**: Om en behållares indexerings princip är inställd på konsekvent uppdateras indexet synkront när du skapar, uppdaterar eller tar bort objekt. Det innebär att konsekvensen för dina Läs frågor är den [konsekvens som kon figurer ATS för kontot](consistency-levels.md).

- **Ingen**: Om en behållares indexerings princip är inställd på ingen, inaktive ras indexeringen i den behållaren. Detta används vanligt vis när en behållare används som ett rent nyckel värdes lager utan behov av sekundära index. Det kan också hjälpa till att påskynda Mass infognings åtgärder.

## <a name="including-and-excluding-property-paths"></a>Inklusive och exklusive egenskaps Sök vägar

En anpassad indexerings princip kan ange egenskaps Sök vägar som uttryckligen tas med eller undantas från indexering. Genom att optimera antalet sökvägar som indexeras kan du minska mängden lagrings utrymme som används av din behållare och förbättra svars tiden för Skriv åtgärder. Dessa sökvägar definieras enligt [metoden som beskrivs i avsnittet indexerings översikt](index-overview.md#from-trees-to-property-paths) med följande tillägg:

- en sökväg som leder till ett skalärt värde (sträng eller siffra) slutar med`/?`
- element från en matris behandlas tillsammans genom `/[]` notationen (i stället `/1` för `/0`osv.)
- `/*` jokertecknet kan användas för att matcha alla element under noden

Ta samma exempel igen:

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

- `headquarters`sökvägen är`employees``/headquarters/employees/?`
- `locations`sökvägen är`country``/locations/[]/country/?`
- sökvägen till något under `headquarters` är`/headquarters/*`

När en sökväg uttryckligen ingår i indexerings principen, måste den också definiera vilka index typer som ska användas för den sökvägen, och för varje index typ gäller den datatyp som indexet avser:

| Indextyp | Tillåtna mål data typer |
| --- | --- |
| Intervall | Sträng eller siffra |
| Spatial | Punkt, lin Est ring eller polygon |

Vi kan `/headquarters/employees/?` till exempel inkludera sökvägen och ange att ett `Range` index ska tillämpas på den sökvägen för båda `String` värdena och `Number` .

### <a name="includeexclude-strategy"></a>Inkludera/exkludera strategi

Alla indexerings principer måste innehålla rot Sök vägen `/*` antingen som en inkluderad eller undantagen sökväg.

- Inkludera rot Sök vägen för att selektivt exkludera sökvägar som inte behöver indexeras. Detta är den rekommenderade metoden eftersom Azure Cosmos DB indexera alla nya egenskaper som kan läggas till i din modell proaktivt.
- Undanta rot Sök vägen för att selektivt inkludera sökvägar som behöver indexeras.

- För sökvägar med vanliga tecken som innehåller alfanumeriska tecken och _ (under streck) behöver du inte undanta Sök vägs strängen runt dubbla citat tecken (till exempel "/Path/?"). För sökvägar med andra specialtecken måste du undvika Sök vägs strängen runt dubbla citat tecken (till exempel "/\"Path-ABC\"/?"). Om du förväntar dig specialtecken i sökvägen kan du kringgå alla säkerhets vägar. Det spelar ingen roll om du avvisar alla sökvägar och bara de som innehåller specialtecken.

- System egenskapen "etag" undantas från indexering som standard, om inte etag läggs till i den inkluderade sökvägen för indexering.

Se [det här avsnittet](how-to-manage-indexing-policy.md#indexing-policy-examples) för indexerings princip exempel.

## <a name="composite-indexes"></a>Sammansatta index

Frågor som `ORDER BY` har två eller fler egenskaper kräver ett sammansatt index. För närvarande används sammansatta index bara av flera `ORDER BY` frågor. Som standard definieras inga sammansatta index så att du kan [lägga till sammansatta index](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) vid behov.

När du definierar ett sammansatt index anger du:

- Två eller flera egenskaps sökvägar. Den sekvens i vilken egenskaps Sök vägar definieras.
- Ordningen (stigande eller fallande).

Följande överväganden används när du använder sammansatta index:

- Om de sammansatta index Sök vägarna inte matchar ordningen på egenskaperna i ORDER BY-satsen kan inte det sammansatta indexet stödja frågan

- Ordningen för sammansatta index Sök vägar (stigande eller fallande) ska också matcha ordningen i ORDER BY-satsen.

- Det sammansatta indexet stöder också en ORDER BY-sats med motsatt ordning på alla sökvägar.

Tänk på följande exempel där ett sammansatt index definieras för egenskaperna a, b och c:

| **Sammansatt index**     | **Exempel `ORDER BY` fråga**      | **Stöds av index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

Du bör anpassa indexerings principen så att du kan hantera alla nödvändiga `ORDER BY` frågor.

## <a name="modifying-the-indexing-policy"></a>Ändra indexerings principen

En behållares indexerings princip kan uppdateras när [som helst genom att använda Azure Portal eller någon av de SDK](how-to-manage-indexing-policy.md): er som stöds. En uppdatering av indexerings principen utlöser en omvandling från det gamla indexet till den nya, som utförs online och på plats (så att ingen ytterligare lagrings utrymme förbrukas under driften). Den gamla principens index omvandlas effektivt till den nya principen utan att det påverkar Skriv tillgängligheten eller det data flöde som har allokerats på behållaren. Omvandling av index är en asynkron åtgärd och den tid det tar att slutföra beror på det etablerade data flödet, antalet objekt och deras storlek. 

> [!NOTE]
> När Omindexering pågår, kan frågor inte returnera alla matchande resultat och kommer att göra det utan att returnera några fel. Det innebär att frågeresultaten kanske inte är konsekventa förrän index omvandlingen har slutförts. Det är möjligt att spåra förloppet för index omvandlingen [med hjälp av en av SDK: erna](how-to-manage-indexing-policy.md).

Om den nya indexerings principens läge är inställt på konsekvent, kan ingen annan indexerings princip ändras när index transformationen pågår. En index omvandling som körs kan avbrytas genom att ställa in indexerings principens läge på ingen (som omedelbart släpper indexet).

## <a name="indexing-policies-and-ttl"></a>Indexerings principer och TTL

[TTL-funktionen (Time-to-Live)](time-to-live.md) kräver att indexeringen är aktiv på den behållare som den är påslagen. Detta innebär att:

- Det går inte att aktivera TTL på en behållare där indexerings läget är inställt på ingen,
- Det går inte att ställa in indexerings läget på none i en behållare där TTL har Aktiver ATS.

För scenarier där ingen egenskaps Sök väg behöver indexeras, men TTL krävs, kan du använda en indexerings princip med:

- ett indexerings läge har angetts till konsekvent och
- ingen sökväg har inkluderats och
- `/*`som den enda undantagna sökvägen.

## <a name="obsolete-attributes"></a>Föråldrade attribut

När du arbetar med indexerings principer kan du stöta på följande attribut som nu är föråldrade:

- `automatic`är ett booleskt värde som definierats i roten för en indexerings princip. Nu ignoreras den och kan ställas in `true`på när verktyget som du använder kräver det.
- `precision`är ett tal definierat på index nivå för inkluderade sökvägar. Nu ignoreras den och kan ställas in `-1`på när verktyget som du använder kräver det.
- `hash`är en index typ som nu ersätts av typ av intervall.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Översikt över indexering](index-overview.md)
- [Hantera indexerings principen](how-to-manage-indexing-policy.md)
