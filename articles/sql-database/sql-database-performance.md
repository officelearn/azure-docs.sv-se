---
title: Övervaka och förbättra prestanda - Azure SQL Database | Microsoft Docs
description: Azure SQL Database tillhandahåller prestandaverktyg som hjälper dig att identifiera områden som kan förbättra aktuella frågeprestanda.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c97aa3aba9b705eeb0e3f40f289af389e6f380b0
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-and-improve-performance"></a>Övervaka och förbättra prestanda
Azure SQL-databas identifierar potentiella problem i din databas och rekommenderar åtgärder som kan förbättra prestandan för din arbetsbelastning genom att tillhandahålla intelligent prestandajustering åtgärder och rekommendationer.

Om du vill granska databasens prestanda använder den **prestanda** panelen på översiktssidan i eller navigera till ”stöd för + felsökning” avsnittet:

   ![Prestanda för vyer](./media/sql-database-performance/entries.png)

I den ”stöd för + felsökning” avsnittet, kan du använda följande sidor:


1. [Översikt över prestanda](#performance-overview) att övervaka prestanda för din databas. 
2. [Rekommendationer](#performance-recommendations) att hitta rekommendationer som kan förbättra prestandan för din arbetsbelastning.
3. [Query Performance Insight](#query-performance-insight) att hitta den övre förbrukningsfrågorna.
4. [Automatisk justering](#automatic-tuning) så att Azure SQL Database automatiskt optimera din databas.

## <a name="performance-overview"></a>Översikt över prestanda
Den här vyn innehåller en sammanfattning av databasens prestanda och hjälper dig med prestanda justera och felsökning. 

![Prestanda](./media/sql-database-performance/performance.png)

* Den **rekommendationer** panelen visar en uppdelning av justera rekommendationer för databasen (de tre översta rekommendationer visas om det finns flera). Klicka på den här panelen tar dig till  **[rekommendationer](#performance-recommendations)**. 
* Den **justera aktiviteten** panelen innehåller en översikt över pågående och slutförda justera åtgärder för din databas, vilket ger en snabb överblick över historiken för inställning av aktiviteten. Klicka på den här panelen går du till fullständig prestandajustering historikvyn för din databas.
* Den **automatisk justering** panelen visar den [automatisk justering configuration](sql-database-automatic-tuning-enable.md) för databasen (justering alternativ som automatiskt tillämpas på databasen). Om du klickar på den här panelen öppnas konfigurationsdialogruta automation.
* Den **databasfrågor** panelen visas en sammanfattning av frågeprestanda för databasen (övergripande DTU användnings- och upp resurs förbrukningsfrågorna). Klicka på den här panelen tar dig till  **[Query Performance Insight](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Den här sidan innehåller intelligent [justera rekommendationer](sql-database-advisor.md) som kan förbättra dina databasprestanda. Följande typer av rekommendationer visas på den här sidan:

* Rekommendationer om vilka index för att skapa eller ta bort.
* Rekommendationer när schemat problem identifieras i databasen.
* Rekommendationer när frågor kan dra nytta av frågor med parametrar.

![Prestanda](./media/sql-database-performance/recommendations.png)

Du kan också hitta komplett historik över justera åtgärder som har använts tidigare.

Lär dig att hitta apply rekommendationer i [söka efter och tillämpa rekommendationer](sql-database-advisor-portal.md) artikel.

## <a name="automatic-tuning"></a>Automatisk inställning
Azure SQL-databaser kan automatiskt finjustera databasens prestanda genom att använda [rekommendationer](sql-database-advisor.md). Mer information, [automatisk justering artikel](sql-database-automatic-tuning.md). Om du vill aktivera den läsa [hur du aktiverar automatisk justering](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](sql-database-query-performance.md) kan du ägna mindre tid felsökning databasens prestanda genom att tillhandahålla:

* Djupare inblick i dina databaser resursförbrukning (DTU). 
* Högsta CPU förbrukningsfrågorna som potentiellt kan anpassas för bättre prestanda. 
* Möjlighet att öka detaljnivån till information om en fråga. 

  ![instrumentpanel för prestanda](./media/sql-database-query-performance/performance.png)

Mer information om den här sidan finns i artikeln  **[använda Query Performance Insight](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure SQL Database-prestandaråd för enskilda databaser](sql-database-performance-guidance.md)
* [När en elastisk pool att användas?](sql-database-elastic-pool-guidance.md)

