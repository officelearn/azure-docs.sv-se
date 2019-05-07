---
title: Query Performance Insight i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver Query Performance Insight-funktionen i Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d45b79e2ca3b3d478102bebdcff3c8892bef2cb5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067543"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Gäller för:** Azure Database för PostgreSQL – enskild Server 9.6 och 10

Query Performance Insight hjälper dig att snabbt identifiera vilka körs längst frågorna är hur den ändras över tid och vilka väntar påverkar dem.

## <a name="permissions"></a>Behörigheter
Behörighet för **ägare** eller **deltagare** krävs för att visa texten för frågorna i Query Performance Insight. **Läsaren** kan visa diagram och tabeller men inte frågetext.

## <a name="prerequisites"></a>Nödvändiga komponenter
För Query Performance Insight ska fungera, data måste finnas i den [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa prestandainsikter
På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store. 

På sidan portal för din Azure Database for PostgreSQL-server väljer **frågeprestandan Insight** under den **Intelligent prestanda** delen av menyraden.

![Query Performance Insight tidskrävande frågor](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Den **tidskrävande frågor** fliken visar de fem främsta frågorna efter Genomsnittlig varaktighet per körning, aggregeras med 15 minuters intervall. Du kan visa fler frågor genom att välja dem i den nedrullningsbara listrutan för **Antal frågor**. Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda Zooma in och ut ikoner för att visa en mindre eller större tidsperiod respektive.

I tabellen nedan diagrammet ger mer information om tidskrävande frågor under den tidsperioden.

Välj fliken **Väntestatistik** för att visa motsvarande visualiseringar av vänttillfällen på servern.

![Query Performance Insight väntar statistik](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.


