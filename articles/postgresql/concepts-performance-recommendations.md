---
title: Prestandarekommendationer i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver funktionen rekommendation för prestanda i Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31d8c0fdf1b4df3ee00f3652c933b4b738384bea
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068847"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Prestandarekommendationer i Azure Database för PostgreSQL – enskild Server

**Gäller för:** Azure Database för PostgreSQL – enskild Server 9.6 och 10

Funktionen Prestandarekommendationer analyserar dina databaser för att skapa anpassade förslag för bättre prestanda. Analysen tittar på olika databasen egenskaper, inklusive schema för att generera rekommendationerna. Aktivera [Query Store](concepts-query-store.md) på servern för att kunna utnyttja funktionen Prestandarekommendationer fullt ut. När du implementerar en rekommendation för prestanda, bör du testa prestanda för att utvärdera effekten av ändringarna. 

## <a name="permissions"></a>Behörigheter
Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

Öppna **Prestandarekommendationer** från den **Intelligent prestanda** delen av menyraden på sidan för Azure portal för din PostgreSQL-server.

![Landningssida för prestandarekommendationer](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Välj **analysera** och välj en databas, som börjar analysen. Beroende på arbetsbelastningen, kan th analys ta flera minuter att slutföra. När analysen är klar, visas ett meddelande i portalen. Analys utför en djupgående undersökning av din databas. Vi rekommenderar att du utför analysis under perioder med låg. 

Den **rekommendationer** i fönstret visas en lista över rekommendationer om några.

![Ny sida för prestanda-rekommendationer](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Rekommendationer tillämpas inte automatiskt. Om du vill tillämpa rekommendationen kopiera frågetexten och köra den från klienten väljer. Kom ihåg att testa och övervaka för att utvärdera rekommendationen. 

## <a name="recommendation-types"></a>Rekommendationen typer

För närvarande stöds två typer av rekommendationer: *Skapa Index* och *Drop Index*.

### <a name="create-index-recommendations"></a>Skapa indexrekommendationer
*Skapa Index* rekommendationer föreslå nya index för att snabba upp de mest kör eller tidskrävande frågor i belastningen. Den här typen av rekommendation kräver [Query Store](concepts-query-store.md) aktiveras. Query Store samlar in frågeinformation om och ger detaljerad fråga runtime och frekvens statistik som används i analysen för att göra rekommendationen.

### <a name="drop-index-recommendations"></a>Ta bort indexrekommendationer
Förutom identifierar index som saknas, analyserar prestanda för befintliga index i Azure Database for PostgreSQL. Om ett index är data som sällan används eller redundant, rekommenderar analysatorn släppa den.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.

