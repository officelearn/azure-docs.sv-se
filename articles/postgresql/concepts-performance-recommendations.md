---
title: Prestanda rekommendationer – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs funktionen prestanda rekommendation i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768477"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Prestanda rekommendationer i Azure Database for PostgreSQL-enskild server

**Gäller för:** Azure Database for PostgreSQL-enskild server version 9,6, 10, 11

Funktionen prestanda rekommendationer analyserar dina databaser för att skapa anpassade förslag för bättre prestanda. För att skapa rekommendationerna tittar analysen på olika databas egenskaper, inklusive schema. Aktivera [frågearkivet](concepts-query-store.md) på servern för att utnyttja funktionen prestanda rekommendationer fullt ut. När du har implementerat en prestanda rekommendation bör du testa prestanda för att utvärdera effekterna av ändringarna. 

## <a name="permissions"></a>Behörigheter
Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **prestanda rekommendationer** från avsnittet **intelligent prestanda** i meny raden på Azure Portal sidan för din postgresql-server.

![Landningssida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Välj **analysera** och välj en databas som kommer att påbörja analysen. Det kan ta flera minuter att slutföra analysen beroende på din arbets belastning. När analysen är klar, visas ett meddelande i portalen. Analysen utför en djup granskning av din databas. Vi rekommenderar att du utför analyser under perioder med låg belastning. 

I fönstret **rekommendationer** visas en lista med rekommendationer om de hittades.

![Ny sida med prestanda rekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Rekommendationerna tillämpas inte automatiskt. Om du vill tillämpa rekommendationen kopierar du frågetexten och kör den från din valda klient. Kom ihåg att testa och övervaka för att utvärdera rekommendationen. 

## <a name="recommendation-types"></a>Rekommendations typer

För närvarande stöds två typer av rekommendationer: *skapa index* och *släpp index*.

### <a name="create-index-recommendations"></a>Skapa index rekommendationer
*Skapa index* rekommendationer föreslå nya index för att påskynda de vanligaste frågorna för körning eller tids krävande frågor i arbets belastningen. Den här rekommendations typen kräver att [query Store](concepts-query-store.md) är aktiverat. Query Store samlar in frågedata och ger detaljerad information om fråge körning och frekvens statistik som används av analysen för att göra rekommendationen.

### <a name="drop-index-recommendations"></a>Ta bort index rekommendationer
Förutom att identifiera saknade index, Azure Database for PostgreSQL analysera prestanda för befintliga index. Om ett index antingen används sällan eller är redundant, rekommenderar analysen att släppa det.

## <a name="considerations"></a>Att tänka på
* Prestanda rekommendationer är inte tillgängliga för [Läs repliker](concepts-read-replicas.md).
## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.

