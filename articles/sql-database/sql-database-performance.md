---
title: Övervaka och förbättra prestanda
description: Azure SQL Database tillhandahåller prestanda verktyg som hjälper dig att identifiera områden som kan förbättra den aktuella frågans prestanda.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: f7e5f698a822cd68d8319102e1b8bddbbfa8ad70
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821324"
---
# <a name="monitor-and-improve-performance"></a>Övervaka och förbättra prestanda

Azure SQL Database identifierar potentiella problem i databasen och rekommenderar åtgärder som kan förbättra arbets Belastningens prestanda genom att tillhandahålla intelligenta justerings åtgärder och rekommendationer.

Om du vill granska databasens prestanda använder du panelen **prestanda** på sidan Översikt eller navigerar till avsnittet "support + fel sökning":

   ![Visa prestanda](./media/sql-database-performance/entries.png)

I avsnittet "support + fel sökning" kan du använda följande sidor:


1. [Prestanda översikt](#performance-overview) för att övervaka databasens prestanda. 
2. [Prestanda rekommendationer](#performance-recommendations) för att hitta prestanda rekommendationer som kan förbättra arbets Belastningens prestanda.
3. [Query Performance Insight](#query-performance-insight) för att hitta de främsta resurs krävande frågorna.
4. [Automatisk justering](#automatic-tuning) som gör att Azure SQL Database automatiskt optimerar databasen.

## <a name="performance-overview"></a>Prestanda översikt

Den här vyn ger en översikt över databasens prestanda och hjälper dig med prestanda justering och fel sökning. 

![Prestanda](./media/sql-database-performance/performance.png)

* Panelen **rekommendationer** ger en analys av justerings rekommendationerna för din databas (de tre främsta rekommendationerna visas om det finns mer). Genom att klicka på den här panelen går du till **[prestanda rekommendationer](#performance-recommendations)** . 
* Panelen **Justera aktivitet** innehåller en översikt över pågående och slutförda justerings åtgärder för din databas, vilket ger dig en snabb överblick över historiken för justerings aktivitet. Om du klickar på den här panelen går du till vyn fullständig fin justering för din databas.
* Panelen **Automatisk justering** visar [konfigurationen för automatisk justering](sql-database-automatic-tuning-enable.md) för din databas (justerings alternativ som tillämpas automatiskt på databasen). Klicka på den här panelen för att öppna dialog rutan automatiserings konfiguration.
* Panelen **databas frågor** visar en sammanfattning av frågans prestanda för din databas (total DTU-användning och främsta resurs krävande frågor). Genom att klicka på den här panelen går du **[query Performance Insight](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Prestandarekommendationer

Den här sidan innehåller [rekommendationer](sql-database-advisor.md) för intelligent justering som kan förbättra databasens prestanda. Följande typer av rekommendationer visas på den här sidan:

* Rekommendationer om vilka index som ska skapas eller släppas.
* Rekommendationer när schema problem identifieras i databasen.
* Rekommendationer när frågor kan dra nytta av parameter frågor.

![Prestanda](./media/sql-database-performance/recommendations.png)

Du kan också hitta fullständig historik över justerings åtgärder som har tillämpats tidigare.

Lär dig hur du hittar en rekommendationer för att tillämpa rekommendationer i artikeln [hitta och tillämpa prestanda rekommendationer](sql-database-advisor-portal.md) .

## <a name="automatic-tuning"></a>Automatisk inställning

Azure SQL-databaser kan justera databas prestanda automatiskt genom att tillämpa [prestanda rekommendationer](sql-database-advisor.md). Läs mer i artikeln om [Automatisk justering](sql-database-automatic-tuning.md). Om du vill aktivera den kan du läsa [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight

Med [query Performance Insight](sql-database-query-performance.md) kan du ägna mindre tid åt att felsöka databas prestanda genom att tillhandahålla:

* Djupare insikt i förbrukningen av dina databas resurser (DTU). 
* De främsta processor krävande frågorna, som kan vara anpassade för bättre prestanda. 
* Möjlighet att öka detalj nivån till information om en fråga. 

  ![Instrument panel för prestanda](./media/sql-database-query-performance/performance.png)

Mer information om den här sidan finns i artikeln **[så här använder du Query Performance Insight](sql-database-query-performance.md)** .

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure SQL Database prestanda vägledning för enskilda databaser](sql-database-performance-guidance.md)
* [När ska en elastisk pool användas?](sql-database-elastic-pool-guidance.md)

