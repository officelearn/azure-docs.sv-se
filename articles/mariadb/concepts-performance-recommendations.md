---
title: Prestanda rekommendationer – Azure Database for MariaDB
description: Den här artikeln beskriver prestanda rekommendations funktionen i Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528106"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Prestandarekommendationer i Azure Database for MariaDB

**Gäller för:** Azure Database for MariaDB 10,2

Funktionen prestanda rekommendationer analyserar dina databaser för att skapa anpassade förslag för bättre prestanda. För att skapa rekommendationerna tittar analysen på olika databas egenskaper, inklusive schema. Aktivera [frågearkivet](concepts-query-store.md) på servern för att utnyttja funktionen prestanda rekommendationer fullt ut. Om prestanda schema är inaktiverat aktiverar Query Store performance_schema och en delmängd av de prestanda schema instrument som krävs för funktionen. När du har implementerat en prestanda rekommendation bör du testa prestanda för att utvärdera effekterna av ändringarna.

## <a name="permissions"></a>Behörigheter

Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer

Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **prestanda rekommendationer** från avsnittet **intelligent prestanda** i meny raden på Azure Portal sidan för din MariaDB-Server.

![Landningssida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Välj **analysera** och välj en databas som kommer att påbörja analysen. Det kan ta flera minuter att slutföra analysen, beroende på din arbets belastning. När analysen är klar, visas ett meddelande i portalen. Analysen utför en djup granskning av din databas. Vi rekommenderar att du utför analyser under perioder med låg belastning.

I fönstret **rekommendationer** visas en lista med rekommendationer om de hittades och det relaterade fråge-ID som skapade rekommendationen. Med fråge-ID kan du använda vyn [MySQL. query_store](concepts-query-store.md#mysqlquery_store) för att läsa mer om frågan.

![Ny sida med prestanda rekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Rekommendationerna tillämpas inte automatiskt. Om du vill tillämpa rekommendationen kopierar du frågetexten och kör den från din valda klient. Kom ihåg att testa och övervaka för att utvärdera rekommendationen.

## <a name="recommendation-types"></a>Rekommendations typer

För närvarande stöds endast *create index* -rekommendationer.

### <a name="create-index-recommendations"></a>Skapa index rekommendationer

*Skapa index* rekommendationer föreslå nya index för att påskynda de vanligaste frågorna för körning eller tids krävande frågor i arbets belastningen. Den här rekommendations typen kräver att [query Store](concepts-query-store.md) är aktiverat. Query Store samlar in frågedata och ger detaljerad information om fråge körning och frekvens statistik som används av analysen för att göra rekommendationen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for MariaDB.