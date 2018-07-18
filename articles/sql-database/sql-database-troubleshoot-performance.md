---
title: Övervakning och prestandajustering – Azure SQL Database | Microsoft Docs
description: Tips för justering i Azure SQL Database via utvärdera och förbättra prestanda.
services: sql-database
author: v-shysun
manager: craigg
editor: ''
keywords: prestandajustering för SQL, prestandajustering för databas, sql prestandajustering tips, prestandajustering för sql-databas
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: v-shysun
ms.openlocfilehash: 79f41ab133cba539e5f855b3ab8fa21723694acb
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092570"
---
# <a name="monitoring-and-performance-tuning"></a>Övervakning och prestandajustering

Azure SQL Database hanteras automatiskt och flexibla datatjänst där du kan enkelt övervaka användning, lägga till eller ta bort resurser (processor, minne, i/o), hitta rekommendationer som kan förbättra databasens prestanda eller låt databasen som anpassas efter din arbetsbelastning och automatiskt optimera prestanda.

Den här artikeln innehåller en översikt över övervaknings- och prestandajusteringsalternativ som är tillgängliga i Azure SQL Database.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Övervakning och felsökning databasprestanda

Azure SQL Database kan du enkelt övervaka din databasanvändning av och identifiera frågor som kan orsaka problem med prestanda. Du kan övervaka databasprestanda med Azure portal eller system vyer. Du har följande alternativ för övervakning och felsökning databasprestanda:

1. I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**, väljer du databasen och sedan använda övervakning diagrammet för att söka efter resurser som närmar sig sin maximala. DTU-användning visas som standard. Klicka på **redigera** att ändra tidsintervall och värden som visas.
2. Använd [Query Performance Insight](sql-database-query-performance.md) att identifiera de frågor som lägger ut det mesta av resurser.
3. Du kan använda dynamiska hanteringsvyer (DMV) Extended Events (`XEvents`), och Query Store i SSMS för att få prestandaparametrar i realtid.

Se den [prestanda vägledning avsnittet](sql-database-performance-guidance.md) att hitta tekniker som du kan använda för att förbättra prestanda i Azure SQL Database om du har identifierat några problem med hjälp av dessa rapporter eller vyer.

> [!IMPORTANT] 
> Det rekommenderas att du alltid använder den senaste versionen av Management Studio för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optimera databasen för att förbättra prestanda

Azure SQL Database hjälper dig att identifiera möjligheter att förbättra och optimera frågeprestanda utan att ändra resurser genom att granska [rekommendationer för prestandajustering](sql-database-advisor.md). Index som saknas och dåligt optimerade frågor är vanliga orsaker till dåliga databasprestanda. Du kan använda de här justeringsrekommendationerna att förbättra prestanda för din arbetsbelastning.
Du kan också låta Azure SQL database till [automatiskt optimera prestandan för dina frågor](sql-database-automatic-tuning.md) genom att tillämpa alla identifierade rekommendationer och verifiera att de förbättras databasens prestanda. Du kan använda följande alternativ för att förbättra databasens prestanda:

1. Använd [SQL Database Advisor](sql-database-advisor-portal.md) att visa rekommendationer för att skapa och släppa index, Parameterisera frågorna och åtgärda problem med databasscheman.
2. [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) och låta Azure SQL-databasen automatiskt korrigering identifieras prestandaproblem.

## <a name="improving-database-performance-with-more-resources"></a>Förbättra databasens prestanda med fler resurser

Slutligen, om det finns inga användbara objekt som kan förbättra databasens prestanda kan du ändra mängden resurser som är tillgängliga i Azure SQL Database. Du kan tilldela mer resurser genom att ändra den [DTU tjänstnivå](sql-database-service-tiers-dtu.md) för en fristående databas eller öka edtu: er för en elastisk pool när som helst. Du kan också om du använder den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md), du kan ändra antingen tjänstnivå eller öka resurserna för din databas. 
1. För fristående databaser kan du [ändra tjänstnivåer](sql-database-service-tiers-dtu.md) eller [beräkningsresurser](sql-database-service-tiers-vcore.md)på begäran för att förbättra databasprestanda.
2. Överväg att använda för flera databaser [elastiska pooler](sql-database-elastic-pool-guidance.md) att skala resurser automatiskt.

## <a name="tune-and-refactor-application-or-database-code"></a>Justera och omstrukturera program eller databasen

Du kan ändra koden för att använda databasen, ändra index, tvinga planer på eller använda tips att manuellt anpassa databasen till din arbetsbelastning mer optimalt. Hitta vissa vägledning och tips för manuell justering och skriva om koden i den [prestanda vägledning avsnittet](sql-database-performance-guidance.md) artikeln.


## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera automatisk justering i Azure SQL Database och att funktionen för automatisk justering helt hantera din arbetsbelastning kan se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- För att använda manuell inställning, kan du granska [Justeringsrekommendationer i Azure-portalen](sql-database-advisor-portal.md) och tillämpa manuellt de som förbättrar prestandan för dina frågor.
- Ändra resurser som är tillgängliga i din databas genom att ändra [Azure SQL Database-tjänstnivåer](sql-database-performance-guidance.md)
