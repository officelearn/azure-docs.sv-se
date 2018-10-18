---
title: Query Performance Insight i Azure Database för PostgreSQL
description: Den här artikeln beskrivs Query Performance Insight-funktionen i Azure Database för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 38cfac8932859db7daa76e831372c97d97f0b24c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376319"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Gäller för:** Azure Database för PostgreSQL 9.6 och 10

> [!IMPORTANT]
> Funktionen för Query Performance Insight är allmänt tillgänglig förhandsversion. 

Query Performance Insight hjälper dig att snabbt identifiera vilka körs längst frågorna är hur den ändras över tid och vilka väntar påverkar dem.

## <a name="permissions"></a>Behörigheter
**Ägare** eller **deltagare** behörigheter som krävs för att visa texten för frågorna i Query Performance Insight. **Läsare** kan visa diagram och tabeller, men inte frågetext.

## <a name="prerequisites"></a>Förutsättningar
För Query Performance Insight ska fungera, data måste finnas i den [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa prestandainsikter
Den [Query Performance Insight](concepts-query-performance-insight.md) vyn i Azure-portalen visar visualiseringar på nyckelinformation från Query Store. 

På sidan portal för din Azure Database for PostgreSQL-server väljer **frågeprestandan Insight** under den **Support och felsökning** delen av menyraden.

![Query Performance Insight tidskrävande frågor](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Den **tidskrävande frågor** fliken visar de 5 främsta frågorna efter Genomsnittlig varaktighet per körning, aggregeras i 15 minuters mellanrum. Du kan visa fler frågor genom att välja den **antalet frågor** nedrullningsbar listruta. Diagrammets färger kan ändras för en specifik fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda Zooma in och ut ikoner för att visa en mindre eller större tidsperiod respektive.

I tabellen nedan diagrammet ger mer information om tidskrävande frågor under den tidsperioden.

Välj den **vänta statistik** fliken för att visa motsvarande visualiseringar på väntar på servern.

![Query Performance Insight vänta statistik](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database för PostgreSQL.


