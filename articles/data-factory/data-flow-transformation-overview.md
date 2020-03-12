---
title: Översikt över mappning av data flödes omvandling
description: En översikt över de olika omvandlingarna som är tillgängliga i mappnings data flödet
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086922"
---
# <a name="mapping-data-flow-transformation-overview"></a>Översikt över mappning av data flödes omvandling

Nedan visas en lista över de omvandlingar som stöds för närvarande i mappnings data flödet. Klicka på varje omvandling för att lära dig mer om konfigurationen.

| Namn | Kategori | Beskrivning |
| ---- | -------- | ----------- |
| [Mängden](data-flow-aggregate.md) | Schema modifierare | Definiera olika typer av agg regeringar som SUM, MIN, MAX och COUNT grupperade efter befintliga eller beräknade kolumner. | 
| [Ändra rad](data-flow-alter-row.md) | Rads modifierare | Ange INSERT-, DELETE-, Update-och upsert-principer på rader. |
| [Villkorlig delning](data-flow-conditional-split.md) | Flera indata/utdata | Dirigera rader med data till olika data strömmar baserat på matchnings villkor. |
| [Härledd kolumn](data-flow-derived-column.md) | Schema modifierare | skapa nya kolumner eller ändra befintliga fält med hjälp av data flödets uttrycks språk. | 
| [Finns](data-flow-exists.md) | Flera indata/utdata | Kontrol lera om dina data finns i en annan källa eller Stream. | 
| [Synkroniseringsfilter](data-flow-filter.md) | Rads modifierare | Filtrera en rad baserat på ett villkor. |
| [Tillämpa](data-flow-flatten.md) | Schema modifierare |  Ta mat ris värden i hierarkiska strukturer, till exempel JSON och avregistrera dem i enskilda rader. |
| [Anslut dig](data-flow-join.md) | Flera indata/utdata |  Kombinera data från två källor eller strömmar. |
| [Sökning](data-flow-lookup.md) | Flera indata/utdata | Referens data från en annan källa. |
| [Ny gren](data-flow-new-branch.md) | Flera indata/utdata | Tillämpa flera uppsättningar av åtgärder och omvandlingar mot samma data ström. |
| [Vridning](data-flow-pivot.md) | Schema modifierare | En agg regering där en eller flera grupperade kolumner har sina distinkta rad värden omvandlade till enskilda kolumner. |
| [Välj](data-flow-select.md) | Schema modifierare | Alias-kolumner och strömmande namn, och släpp eller ordna om kolumner |
| [Sjönk](data-flow-sink.md) | - | Ett slutgiltigt mål för dina data |
| [Ordning](data-flow-sort.md) | Rads modifierare | Sortera inkommande rader i den aktuella data strömmen |
| [Källa](data-flow-source.md) | - | En data källa för data flödet |
| [Surrogat nyckel](data-flow-surrogate-key.md) | Schema modifierare | Lägg till ett mellanliggande nyckel värde som inte är mellan företag |
| [Union](data-flow-union.md) | Flera indata/utdata | Kombinera flera data strömmar lodrätt |
| [Unpivot](data-flow-unpivot.md) | Schema modifierare | Pivotera kolumner i rad värden |
| [Terminalfönstret](data-flow-window.md) | Schema modifierare |  Definiera Window-baserade agg regeringar för kolumner i dina data strömmar. |
