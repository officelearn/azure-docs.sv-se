---
title: Query Performance Insight i Azure Database för PostgreSQL
description: Den här artikeln beskrivs Query Performance Insight-funktionen i Azure Database för PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 1a71fb81acc91036ce12ff15f6b2762b808c7473
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541631"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Gäller för:** Azure Database för PostgreSQL 9.6 och 10

> [!IMPORTANT]
> Funktionen för Query Performance Insight är allmänt tillgänglig förhandsversion. 

Query Performance Insight hjälper dig att snabbt identifiera vilka körs längst frågorna är hur den ändras över tid och vilka väntar påverkar dem.

## <a name="permissions"></a>Behörigheter
Behörighet för **ägare** eller **deltagare** krävs för att visa texten för frågorna i Query Performance Insight. **Läsaren** kan visa diagram och tabeller men inte frågetext.

## <a name="prerequisites"></a>Förutsättningar
För Query Performance Insight ska fungera, data måste finnas i den [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa prestandainsikter
På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store. 

På sidan portal för din Azure Database for PostgreSQL-server väljer **frågeprestandan Insight** under den **Support och felsökning** delen av menyraden.

![Query Performance Insight tidskrävande frågor](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

På fliken **Långvariga frågor** visas de 5 främsta frågorna efter genomsnittlig varaktighet per körning, aggregerat i 15-minutersintervall. Du kan visa fler frågor genom att välja dem i den nedrullningsbara listrutan för **Antal frågor**. Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda Zooma in och ut ikoner för att visa en mindre eller större tidsperiod respektive.

I tabellen nedan diagrammet ger mer information om tidskrävande frågor under den tidsperioden.

Välj fliken **Väntestatistik** för att visa motsvarande visualiseringar av vänttillfällen på servern.

![Väntestatistik för Query Performance Insight](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.


