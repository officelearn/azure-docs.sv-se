---
title: Frågeprestanda insikt - Azure-databas för MySQL
description: I den här artikeln beskrivs funktionen Frågeprestandainsikt i Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a924f51fac6d43ae4a4530ac4f61f2e8b1f4e8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537065"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Information om frågeprestanda i Azure Database for MySQL

**Gäller:** Azure-databas för MySQL 5.7

Query Performance Insight hjälper dig att snabbt identifiera vad dina längsta frågor är, hur de förändras med tiden och vad som väntar påverkar dem.

## <a name="common-scenarios"></a>Vanliga scenarier

### <a name="long-running-queries"></a>Långvariga frågor

- Identifiera de senaste X-timmarnas tidskrävande frågor
- Identifiera de vanligaste N-frågorna som väntar på resurser
 
### <a name="wait-statistics"></a>Vänta statistik

- Förstå vänta naturen för en fråga
- Förstå trender för resursväntar och var resurskonkurrens finns

## <a name="permissions"></a>Behörigheter

Behörighet för **ägare** eller **deltagare** krävs för att visa texten för frågorna i Query Performance Insight. **Läsaren** kan visa diagram och tabeller men inte frågetext.

## <a name="prerequisites"></a>Krav

För att Frågeprestanda insikt ska fungera måste data finnas i [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa resultatinsikter

På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store.

På portalsidan på azure-databasen för MySQL-servern väljer du **Query Performance Insight** under avsnittet Intelligent **prestanda** i menyraden.

### <a name="long-running-queries"></a>Långvariga frågor

Fliken **Tidskrävande frågor** visar de 5 vanligaste frågorna efter genomsnittlig varaktighet per körning, aggregerade i 15-minutersintervall. Du kan visa fler frågor genom att välja i listrutan **Antal frågor.** Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda ikonerna zooma in och ut för att visa en mindre eller större tidsperiod.

![Frågor Performance Insight tidskrävande frågor](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Vänta statistik

> [!NOTE]
> Väntestatistik är avsedd för felsökning av frågeprestandaproblem. Vi rekommenderar att du endast är aktiverad för felsökning. <br>Om felmeddelandet visas i Azure-portalen "*Problemet påträffades för 'Microsoft.DBforMySQL'; inte kan uppfylla begäran. Om det här problemet kvarstår eller är oväntat kontaktar du supporten med den här informationen.*" medan du visar väntestatistik använder du en mindre tidsperiod.

Vänta statistik ger en vy över de vänta händelser som inträffar under körningen av en viss fråga. Läs mer om typerna för väntehändelse i Dokumentationen för [MySQL-motorn](https://go.microsoft.com/fwlink/?linkid=2098206).

Välj fliken **Väntestatistik** för att visa motsvarande visualiseringar av vänttillfällen på servern.

Frågor som visas i väntstatistikvyn grupperas efter de frågor som uppvisar de största väntetiderna under det angivna tidsintervallet.

![Frågeprestandainsikt väntar statistik](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database för MySQL.