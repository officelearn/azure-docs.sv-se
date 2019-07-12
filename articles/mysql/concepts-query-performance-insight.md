---
title: Query Performance Insight i Azure Database för MySQL
description: Den här artikeln beskriver Query Performance Insight-funktionen i Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8f142933ebf955cbe3aa119f42779109fb6ef7db
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589077"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Query Performance Insight i Azure Database för MySQL

**Gäller för:**  Azure Database för MySQL 5.7

> [!NOTE]
> Query Performance Insight är i förhandsversion.

Query Performance Insight hjälper dig att snabbt identifiera vilka körs längst frågorna är hur den ändras över tid och vilka väntar påverkar dem.

## <a name="common-scenarios"></a>Vanliga scenarier

### <a name="long-running-queries"></a>Långvariga frågor

- Identifiera längsta frågor som körs i förflutna X timmar
- Identifiera de x främsta Felgrupperna frågor som väntar på resurser
 
### <a name="wait-statistics"></a>Vänta statistik

- Förstå vänta karaktär för en fråga
- Förstå trender för resursen väntar och där det inte finns resurskonkurrens

## <a name="permissions"></a>Behörigheter

**Ägare** eller **deltagare** behörigheter som krävs för att visa texten för frågorna i Query Performance Insight. ** Läsare** kan visa diagram och tabeller, men inte frågetext.

## <a name="prerequisites"></a>Förutsättningar

För Query Performance Insight ska fungera, data måste finnas i den [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa prestandainsikter

På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store.

På sidan portal för din Azure Database for MySQL-server väljer **Query Performance Insight** under den **Intelligent prestanda** delen av menyraden.

### <a name="long-running-queries"></a>Långvariga frågor

Den **tidskrävande frågor** fliken visar de 5 främsta frågorna efter Genomsnittlig varaktighet per körning, aggregeras med 15 minuters intervall. Du kan visa fler frågor genom att välja den **antalet frågor** nedrullningsbar listruta. Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda Zooma in och ut ikoner för att visa en mindre eller större tidsperiod respektive.

![Query Performance Insight tidskrävande frågor](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Vänta statistik

> [!NOTE]
> Vänta statistik är avsedda för att felsöka prestandaproblem för frågan. Vi rekommenderar för att endast för felsökning.

Vänta statistik ger en överblick över wait-händelser som inträffar under körningen av en specifik fråga. Mer information om händelsetyper vänta i den [MySQL engine-dokumentationen](https://go.microsoft.com/fwlink/?linkid=2098206).

Välj den **vänta statistik** fliken för att visa motsvarande visualiseringar på väntar på servern.

Frågor som visas i vyn vänta är grupperade efter de frågor som uppvisar den största väntar under det angivna tidsintervallet.

![Query Performance Insight väntar statistik](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database för MySQL.