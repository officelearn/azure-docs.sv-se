---
title: Prestandarekommendationer i Azure Database for MariaDB
description: Den här artikeln beskriver funktionen rekommendation för prestanda i Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ad3e2157e7b8748174cc8b81d1debd5de0ba77b9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079488"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Prestandarekommendationer i Azure-databas för MariaDB

**Gäller för:** Azure Database for MariaDB 10.2s

> [!NOTE]
> Prestandarekommendationer genomgår förhandsgranskning. Stöd för Prestandarekommendationer i Azure-portalen distribueras och ännu inte tillgängliga i din region.

Funktionen Prestandarekommendationer analyserar dina databaser för att skapa anpassade förslag för bättre prestanda. Analysen tittar på olika databasen egenskaper, inklusive schema för att generera rekommendationerna. Aktivera [Query Store](concepts-query-store.md) på servern för att kunna utnyttja funktionen Prestandarekommendationer fullt ut. Om prestanda schemat är av, möjliggör aktivera Query Store performance_schema och en delmängd av prestanda schemat instrument som krävs för funktionen. När du implementerar en rekommendation för prestanda, bör du testa prestanda för att utvärdera effekten av ändringarna.

## <a name="permissions"></a>Behörigheter

Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer

Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **Prestandarekommendationer** från den **Intelligent prestanda** delen av menyraden i Azure portal sidan för MariaDB-servern.

![Landningssida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Välj **analysera** och välj en databas, som börjar analysen. Analysen kan ta flera minuter att slutföra beroende på din arbetsbelastning. När analysen är klar, visas ett meddelande i portalen. Analys utför en djupgående undersökning av din databas. Vi rekommenderar att du utför analysis under perioder med låg.

Den **rekommendationer** i fönstret visas en lista över rekommendationer om några och relaterade fråge-ID som genereras av den här rekommendationen. Med fråge-ID kan du använda den [mysql.query_store](concepts-query-store.md#mysqlquery_store) visa mer information om frågan.

![Ny sida för prestanda-rekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Rekommendationer tillämpas inte automatiskt. Om du vill tillämpa rekommendationen kopiera frågetexten och köra den från klienten väljer. Kom ihåg att testa och övervaka för att utvärdera rekommendationen.

## <a name="recommendation-types"></a>Rekommendationen typer

För närvarande endast *Create Index* rekommendationer som stöds.

### <a name="create-index-recommendations"></a>Skapa indexrekommendationer

*Skapa Index* rekommendationer föreslå nya index för att snabba upp de mest kör eller tidskrävande frågor i belastningen. Den här typen av rekommendation kräver [Query Store](concepts-query-store.md) aktiveras. Query Store samlar in frågeinformation om och ger detaljerad fråga runtime och frekvens statistik som används i analysen för att göra rekommendationen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for MariaDB.