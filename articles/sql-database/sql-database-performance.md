---
title: Övervaka och förbättra prestanda – Azure SQL Database | Microsoft Docs
description: Azure SQL Database innehåller prestandaverktyg som hjälper dig att identifiera områden som kan förbättra aktuella frågeprestanda.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 8a458af27bd517be7c3ce0b5ad30c6d575d494c0
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522449"
---
# <a name="monitor-and-improve-performance"></a>Övervaka och förbättra prestanda
Azure SQL-databas identifierar potentiella problem i din databas och rekommenderar åtgärder som kan förbättra prestanda genom att tillhandahålla intelligent justeringsåtgärder och rekommendationer.

Om du vill granska din databasprestanda, använda den **prestanda** panelen på översiktssidan eller navigera till ”Support och felsökning” avsnittet:

   ![Visa prestanda](./media/sql-database-performance/entries.png)

I den ”Support och felsökning” avsnittet, du kan använda följande sidor:


1. [Prestandaöversikt](#performance-overview) att övervaka prestanda för din databas. 
2. [Prestandarekommendationer](#performance-recommendations) att hitta prestandarekommendationer som kan förbättra prestanda för din arbetsbelastning.
3. [Query Performance Insight](#query-performance-insight) att hitta översta resurskrävande frågor.
4. [Automatisk justering](#automatic-tuning) så att Azure SQL Database automatiskt Optimera databasen.

## <a name="performance-overview"></a>Prestandaöversikt
Den här vyn innehåller en sammanfattning av databasens prestanda och hjälper dig med anpassning av prestanda och felsökning. 

![Prestanda](./media/sql-database-performance/performance.png)

* Den **rekommendationer** panel visar en uppdelning av justeringsrekommendationer för databasen (tre översta rekommendationer visas om det finns flera). Klicka på den här panelen tar dig till  **[prestandarekommendationer](#performance-recommendations)**. 
* Den **Justeringsaktivitet** panelen innehåller en sammanfattning av pågående och slutförda justering åtgärder för din databas, vilket ger dig en snabb överblick över historiken för justeringsaktivitet. Klicka på den här panelen tar dig till fullständig justering historikvyn för din databas.
* Den **automatisk justering** panelen visar den [automatisk justering configuration](sql-database-automatic-tuning-enable.md) för databasen (justering alternativ som ska vidtas automatiskt din databas). Om du klickar på den här panelen öppnas konfigurationsdialogruta automation.
* Den **databasfrågor** panel visar en sammanfattning av frågeprestanda för databasen (övergripande DTU användnings- och top resurskrävande frågor). Klicka på den här panelen tar dig till  **[Query Performance Insight](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Den här sidan innehåller intelligent [justeringsrekommendationer](sql-database-advisor.md) som kan förbättra din databasprestanda. Följande typer av rekommendationer visas på den här sidan:

* Rekommendationer om vilka index för att skapa eller ta bort.
* Rekommendationer när problem med databasscheman identifieras i databasen.
* Rekommendationer när frågor kan dra nytta av frågor med parametrar.

![Prestanda](./media/sql-database-performance/recommendations.png)

Du kan också hitta komplett historik över justering åtgärder som har tillämpats tidigare.

Lär dig att hitta en tillämpa prestandarekommendationer i [hitta och tillämpa prestandarekommendationer](sql-database-advisor-portal.md) artikeln.

## <a name="automatic-tuning"></a>Automatisk inställning
Azure SQL-databaser automatiskt kan justera databasprestanda för genom att använda [prestandarekommendationer](sql-database-advisor.md). Mer information, [automatisk justering artikeln](sql-database-automatic-tuning.md). Aktivera det genom att läsa [så här aktiverar du automatisk justering](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](sql-database-query-performance.md) kan du ägna mindre tid felsökning databasens prestanda genom att tillhandahålla:

* Bättre insikt i dina databaser-resursförbrukning (DTU). 
* De frågor som potentiellt kan anpassas för som förbrukar mest Processorkraft bättre prestanda. 
* Möjligheten att granska nedåt i detaljerna för en fråga. 

  ![instrumentpanel för prestanda](./media/sql-database-query-performance/performance.png)

Mer information om den här sidan finns i artikeln  **[hur du använder Query Performance Insight](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure SQL Database-prestandaråd för enskilda databaser](sql-database-performance-guidance.md)
* [När ska en elastisk pool användas?](sql-database-elastic-pool-guidance.md)

