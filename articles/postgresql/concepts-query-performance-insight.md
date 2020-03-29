---
title: Frågeprestandainsikt - Azure-databas för PostgreSQL - Single Server
description: I den här artikeln beskrivs funktionen Frågeprestandainsikt i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768392"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**Gäller:** Azure-databas för PostgreSQL - Single Server version 9.6, 10, 11

Query Performance Insight hjälper dig att snabbt identifiera vad dina längsta frågor är, hur de förändras med tiden och vad som väntar påverkar dem.

## <a name="permissions"></a>Behörigheter
Behörighet för **ägare** eller **deltagare** krävs för att visa texten för frågorna i Query Performance Insight. **Läsaren** kan visa diagram och tabeller men inte frågetext.

## <a name="prerequisites"></a>Krav
För att Frågeprestanda insikt ska fungera måste data finnas i [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa resultatinsikter
På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store. 

På portalsidan i Azure Database för PostgreSQL-server väljer du **Frågeprestandainsikt** under avsnittet **Intelligent prestanda** i menyraden.

![Frågor Performance Insight tidskrävande frågor](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Fliken **Tidskrävande frågor** visar de fem vanligaste frågorna efter genomsnittlig varaktighet per körning, aggregerade i 15-minutersintervall. Du kan visa fler frågor genom att välja dem i den nedrullningsbara listrutan för **Antal frågor**. Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda ikonerna zooma in och ut för att visa en mindre eller större tidsperiod.

Tabellen under diagrammet innehåller mer information om de tidskrävande frågorna i det tidsfönstret.

Välj fliken **Väntestatistik** för att visa motsvarande visualiseringar av vänttillfällen på servern.

![Frågeprestandainsikt väntar statistik](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Överväganden
* Frågeprestanda insikt är inte tillgänglig för [lästa repliker](concepts-read-replicas.md).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.


