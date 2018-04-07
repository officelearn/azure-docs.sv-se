---
title: Övervakning och prestandajustering - Azure SQL Database | Microsoft Docs
description: Tips för prestandajustering i Azure SQL Database via utvärdera och förbättra.
services: sql-database
author: v-shysun
manager: craigg
editor: ''
keywords: prestandajustering för SQL, prestandajustering för databasen, sql prestandajustering tips, prestandajustering för sql-databas
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: v-shysun
ms.openlocfilehash: 440f3bf41cd2dcedf964e7476323d7b3a1527624
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="monitoring-and-performance-tuning"></a>Övervaka och justera prestanda

Azure SQL Database hanteras automatiskt och flexibel datatjänst där du kan lätt övervaka användningen, lägga till eller ta bort resurser (processor, minne, i/o), hitta rekommendationer som kan förbättra prestandan för din databas eller låt databasen anpassa din arbetsbelastning och automatiskt optimera prestanda.

Den här artikeln innehåller en översikt över övervakning och prestandajustering alternativ som är tillgängliga i Azure SQL Database.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Övervakning och felsökning databasprestanda

Azure SQL-databas kan du lätt övervaka din databasanvändningen och identifiera frågor som kan orsaka prestandaproblem. Du kan övervaka databasprestanda med hjälp av Azure portal eller system vyer. Du har följande alternativ för övervakning och felsökning databasens prestanda:

1. I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**databasen och välj sedan använda övervakning diagrammet för att söka efter resurser som närmar sig sin högsta. DTU-förbrukning visas som standard. Klicka på **redigera** ändra tidsintervall och värden som visas.
2. Använd [Query Performance Insight](sql-database-query-performance.md) att identifiera de frågor som tillbringar det mesta av resurser.
3. Du kan använda dynamiska hanteringsvyer (av DMV: er), Extended Events (`XEvents`), och Frågearkivet i SSMS att hämta prestandaparametrar i realtid.

Finns det [prestanda vägledning avsnittet](sql-database-performance-guidance.md) att hitta tekniker som du kan använda för att förbättra prestanda i Azure SQL Database om du identifierar vissa problem med att använda dessa rapporter eller vyer.

> [!IMPORTANT] 
> Det rekommenderas att du alltid använder den senaste versionen av Management Studio för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optimera databasen för att förbättra prestanda

Azure SQL-databas kan du identifiera möjligheterna till att förbättra och optimera frågeprestanda utan att ändra resurser genom att granska [rekommendationer för prestandajustering](sql-database-advisor.md). Index som saknas och dåligt optimerade frågor är vanliga orsaker till dåliga databasprestanda. Du kan använda dessa prestandajustering rekommendationer för att förbättra prestandan för din arbetsbelastning.
Du kan också låta Azure SQL database [automatiskt optimera prestanda för dina frågor](sql-database-automatic-tuning.md) genom att använda alla identifierade rekommendationer och verifiering av att de förbättras databasens prestanda. Du kan använda följande alternativ för att förbättra prestanda för databasen:

1. Använd [SQL Database Advisor](sql-database-advisor-portal.md) att visa rekommendationer för att skapa och släppa index, Parameterisera frågor och åtgärda problem med schemat.
2. [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) och låta Azure SQL-databasen automatiskt korrigera identifieras prestandaproblem.

## <a name="improving-database-performance-with-more-resources"></a>Förbättrad databasprestanda med mer resurser

Slutligen om det finns ingen tillämplig objekt som kan förbättra prestandan för din databas kan ändra du mängden tillgängliga resurser i Azure SQL Database. Du kan tilldela fler resurser genom att ändra den [tjänstnivån](sql-database-service-tiers.md) för en fristående databas eller öka edtu: er för en elastisk pool när som helst.
1. För fristående databaser, kan du [ändra tjänstnivå](sql-database-service-tiers.md) på begäran för att förbättra prestanda för databasen.
2. Överväg att använda för flera databaser [elastiska pooler](sql-database-elastic-pool-guidance.md) att skala resurser automatiskt.

## <a name="tune-and-refactor-application-or-database-code"></a>Finjustera och flytta program eller databas

Du kan ändra programkoden mer optimalt använda databasen, ändra index, tvinga planer eller använda tips för att manuellt anpassa databasen till din arbetsbelastning. Hitta vissa råd och tips för manuell justering och skriva om koden i den [prestanda vägledning avsnittet](sql-database-performance-guidance.md) artikel.


## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera automatisk justering i Azure SQL Database och låta automatisk justering funktionen för att hantera din arbetsbelastning, se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Om du vill använda manuell inställning, kan du granska [justera rekommendationerna i Azure-portalen](sql-database-advisor-portal.md) och tillämpa manuellt med de som förbättrar prestandan för dina frågor.
- Ändra resurser som är tillgängliga i databasen genom att ändra [Azure SQL Database servicenivåer](sql-database-performance-guidance.md)
