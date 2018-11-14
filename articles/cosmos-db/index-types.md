---
title: Index-typer i Azure Cosmos DB
description: Översikt över index typer i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: f3c5d7bc1907e94ff2e590fe77cc531ac4b01f4c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629229"
---
# <a name="index-types-in-azure-cosmos-db"></a>Index-typer i Azure Cosmos DB

Det finns flera alternativ där du konfigurerar indexeringsprincipen för en sökväg. Du kan ange en eller flera indexering definitioner för varje sökväg:

- **Datatyp:** sträng, nummer, punkt, Polygon eller LineString (kan innehålla endast en post per datatypen per sökväg).

- **Index-typ:** Hash (likhetsfrågor), intervall (likhet, intervall eller ORDER BY-frågor) eller avstånds (rumsliga förfrågningar).

- **Precision:** för ett Hash-index detta varierar från 1 till 8 för både strängar och siffror och standardvärdet är 3. För ett intervall index är värdet för maximal precision-1. Det kan variera mellan 1 och 100 (maximal precision) för sträng eller numeriska värden.

## <a name="index-kind"></a>Index-typ

Azure Cosmos DB stöder Hash-index och intervallsindex för varje sökväg som kan konfigureras för strängen eller talvärdet datatyper, eller båda.

- **Hash-index** stöder effektiv likhet och JOIN-frågor. De flesta användningar behöver Hash-index en högre precision än standardvärdet 3 byte. Datatypen kan vara en sträng eller en siffra.

- **Intervallsindex** stöder effektiv likhetsfrågor, omfångsfrågor (med hjälp av >, <>, =, < =,! =), och ORDER BY-frågor. ORDER By-frågor som standard även kräva maximala index precision (-1). Datatypen kan vara en sträng eller en siffra.

- **Spatialindexet** stöder effektiv spatial (inom och avståndet) frågor. Datatypen kan vara punkt, Polygon eller LineString. Azure Cosmos DB stöder också spatialindexet-typ för varje sökväg som kan anges för punkt, Polygon eller LineString-datatyper. Värdet på den angivna sökvägen måste vara ett giltigt GeoJSON-fragment som {”type”: ”Point”, ”coordinates”: [0.0, 10.0]}. Azure Cosmos DB har stöd för automatisk indexering av Point och Polygon LineString-datatyper.

Här följer exempel på frågor som Hash, intervallet, och rumsindex kan användas för att tillhandahålla:

| **Index-typ** | **Beskrivning/användningsfall** |
| ---------- | ---------------- |
| Hash  | Hash-över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen-c WHERE c.prop = ”value”<br><br>Hash över/sammanställer / [] /? (eller / eller/sammanställer /) kan användas för att effektivt hantera följande frågor:<br><br>Välj tagg från samlingen c JOIN-tagg i c.props var taggen = 5  |
| Intervall  | Variera över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen-c WHERE c.prop = ”value”<br><br>Välj från samlingen-c WHERE c.prop > 5<br><br>Välj samling c ORDER BY c.prop   |
| Spatial     | Variera över/prop /? (eller /) kan användas för att effektivt hantera följande frågor:<br><br>Välj från samlingen c<br><br>VAR ST_DISTANCE (c.prop, {”type”: ”pekar”, ”coordinates”: [0.0, 10.0]}) < 40<br><br>Välj från samlingen c där ST_WITHIN(c.prop, {"type": "Polygon",...})--med indexering för punkter som aktiverats<br><br>Välj från samlingen c där ST_WITHIN({"type": "Point",...}, c.prop)--med indexering för polygoner aktiverat.     |

## <a name="default-behavior-of-index-kinds"></a>Standardbeteendet för index-typer

- Om det finns inget intervallsindex för alla precision för att signalera att en sökning kan vara nödvändigt att betjäna frågan, i detta fall är som standard ett fel returneras för frågor med intervallet operatörer som > =.

- Intervallfrågor kan utföras utan ett intervallsindex med hjälp av REST-API ”x-ms-documentdb-enable-genomsökning” rubriken eller alternativet ”EnableScanInQuery” begäran med hjälp av .NET SDK. Om det finns några andra filter i frågan att Azure Cosmos DB kan använda indexet för att filtrera mot, returneras inget fel.

- Som standard returneras ett fel för rumsliga förfrågningar om det inte finns en spatialindexet eller andra filter som kan hanteras från indexet. Sådana frågor kan utföras som en genomsökning med hjälp av x-ms-documentdb-enable-genomsökning eller EnableScanInQuery.

## <a name="index-precision"></a>Index precision

- Du kan använda indexet precision för att göra en kompromiss mellan index storage overhead och frågeprestanda. För siffror rekommenderar vi använder standardkonfigurationen för precision-1 (max). Eftersom siffror är 8 byte i JSON, motsvarar detta en konfiguration av 8 byte. Välja ett lägre värde för noggrannhet på millisekunder, till exempel 1 och 7, index innebär det att värdena inom vissa intervall som mappas till samma-post. Därför kan du minska index lagringsutrymme, men Frågekörningen behöva bearbeta fler objekt. Därför måste förbrukar den mer dataflöde/ru: er.

- Index precision har mer praktiska program med sträng-intervall. Eftersom strängar kan vara en godtycklig längd, kan valet av index precisionen påverka prestanda för frågor för sträng-intervallet. Det kan även påverka mängden index lagringsutrymme som krävs. Sträng intervallet index kan konfigureras med ett index precision mellan 1 och 100 eller -1 (max). Om du vill utföra ORDER BY-frågor mot strängegenskaper, måste du ange en noggrannhet på 1 för motsvarande sökvägar.

- Spatialindex använda alltid standard index precisionen för alla typer (Point, LineString och Polygon). Standard index precisionen för rumsindex kan inte åsidosättas.

Azure Cosmos DB Returnerar ett fel när en fråga använder ORDER BY men inte har en intervallsindex mot den efterfrågade sökvägen med den maximala precisionen.

## <a name="next-steps"></a>Nästa steg

Mer information om indexering i Azure Cosmos DB finns i följande artiklar:

- [Översikt över indexering](index-overview.md)
- [Indexeringsprincip](indexing-policies.md)
- [Index sökvägar](index-paths.md)

