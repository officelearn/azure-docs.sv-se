---
title: Query Performance Insight i Azure Database for MySQL
description: I den här artikeln beskrivs Query Performance Insight funktionen i Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: b7395a8ab71e860c2e584339dcd581077a4f4020
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595449"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Query Performance Insight i Azure Database for MySQL

**Gäller för:** Azure Database for MySQL 5,7

> [!IMPORTANT]
> Query Performance Insight är en för hands version.

Query Performance Insight hjälper dig att snabbt identifiera vad dina längsta kör frågor är, hur de ändras med tiden och vilka vänte tider som påverkar dem.

## <a name="common-scenarios"></a>Vanliga scenarier

### <a name="long-running-queries"></a>Tids krävande frågor

- Identifiera de längsta körnings frågorna de senaste X timmarna
- Identifiera de N främsta frågorna som väntar på resurser
 
### <a name="wait-statistics"></a>Vänta med statistik

- Förstå vänte natur för en fråga
- Förstå trender för resurs väntar och där resurs innehåll finns

## <a name="permissions"></a>Behörigheter

Behörighet för **ägare** eller **deltagare** krävs för att visa texten för frågorna i Query Performance Insight. **Läsaren** kan visa diagram och tabeller men inte frågetext.

## <a name="prerequisites"></a>Krav

För att Query Performance Insight ska fungera måste data finnas i [frågearkivet](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Visa prestanda insikter

På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store.

På Portal-sidan på Azure Database for MySQL-servern väljer du **query Performance Insight** under avsnittet **intelligent prestanda** i meny raden.

### <a name="long-running-queries"></a>Tids krävande frågor

På fliken **tids krävande frågor** visas de 5 främsta frågorna med genomsnittlig varaktighet per körning, sammanställt i 15-minuters intervall. Du kan visa fler frågor genom att välja från List rutan **antal frågor** . Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster. Du kan också använda ikonerna zooma in och ut för att visa en kortare eller längre tids period.

![Query Performance Insight tids krävande frågor](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Vänta med statistik

> [!NOTE]
> Väntande statistik är avsedd för fel sökning av problem med frågans prestanda. Vi rekommenderar att du bara aktive ras i fel söknings syfte. <br>Om du får fel meddelandet i Azure Portal "*problemet upptäcktes för" Microsoft. DBforMySQL "; Det går inte att slutföra begäran. Kontakta supporten med den här informationen om problemet fortsätter eller är oväntat.* " Använd en kortare tids period när du visar väntande statistik.

Vänta med statistik är en vy över de väntande händelser som inträffar när en speciell fråga körs. Läs mer om händelse typerna wait i [MySQL Engine-dokumentationen](https://go.microsoft.com/fwlink/?linkid=2098206).

Välj fliken **Väntestatistik** för att visa motsvarande visualiseringar av vänttillfällen på servern.

Frågor som visas i vyn vänta med statistik grupperas efter de frågor som uppvisar störst vänte tid under det angivna tidsintervallet.

![Query Performance Insight väntar på statistik](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for MySQL.