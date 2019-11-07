---
title: Prestanda rekommendationer i Azure Database for MySQL
description: Den här artikeln beskriver prestanda rekommendations funktionen i Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1371b286f5e9bd82345e17c11e910c68fac9fed2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603153"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Prestanda rekommendationer i Azure Database for MySQL

**Gäller för:** Azure Database for MySQL 5,7

Funktionen prestanda rekommendationer analyserar dina databaser för att skapa anpassade förslag för bättre prestanda. För att skapa rekommendationerna tittar analysen på olika databas egenskaper, inklusive schema. Aktivera [frågearkivet](concepts-query-store.md) på servern för att utnyttja funktionen prestanda rekommendationer fullt ut. Om prestanda schema är inaktiverat aktiverar Query Store performance_schema och en delmängd av de prestanda schema instrument som krävs för funktionen. När du har implementerat en prestanda rekommendation bör du testa prestanda för att utvärdera effekterna av ändringarna.

## <a name="permissions"></a>Behörigheter

Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer

Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **prestanda rekommendationer** från avsnittet **intelligent prestanda** i meny raden på Azure Portal sidan för MySQL-servern.

![Landningssida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Välj **analysera** och välj en databas som kommer att påbörja analysen. Det kan ta flera minuter att slutföra analysen, beroende på din arbets belastning. När analysen är klar, visas ett meddelande i portalen. Analysen utför en djup granskning av din databas. Vi rekommenderar att du utför analyser under perioder med låg belastning.

I fönstret **rekommendationer** visas en lista med rekommendationer om de hittades och det relaterade fråge-ID som skapade rekommendationen. Med fråge-ID kan du använda vyn [MySQL. query_store](concepts-query-store.md#mysqlquery_store) för att lära dig mer om frågan.

![Ny sida med prestanda rekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Rekommendationerna tillämpas inte automatiskt. Om du vill tillämpa rekommendationen kopierar du frågetexten och kör den från din valda klient. Kom ihåg att testa och övervaka för att utvärdera rekommendationen.

## <a name="recommendation-types"></a>Rekommendations typer

För närvarande stöds endast *create index* -rekommendationer.

### <a name="create-index-recommendations"></a>Skapa index rekommendationer

*Skapa index* rekommendationer föreslå nya index för att påskynda de vanligaste frågorna för körning eller tids krävande frågor i arbets belastningen. Den här rekommendations typen kräver att [query Store](concepts-query-store.md) är aktiverat. Query Store samlar in frågedata och ger detaljerad information om fråge körning och frekvens statistik som används av analysen för att göra rekommendationen.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for MySQL.