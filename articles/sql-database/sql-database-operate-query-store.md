---
title: Använda Query Store i Azure SQL Database
description: Lär dig hur du använder Query Store i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 3ceb8569d952f2947870ce7314f869623b2d87f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584751"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Använda Query Store i Azure SQL Database

Query Store i Azure är en fullständigt hanterad databas-funktion som kontinuerligt samlas in och Detaljerad historisk information om alla frågor. Du kan tänka Query Store som liknar ett flygplan svart data låda som avsevärt förenklar frågeprestanda felsökning både för moln och lokala kunder. Den här artikeln beskriver specifika aspekter av operativsystem Query Store i Azure. Med dessa data i förväg insamlade fråga kan du snabbt diagnostisera och lösa problem med prestanda och därför lägga mer tid på kärnverksamheten. 

Query Store har [globalt tillgänglig](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) i Azure SQL Database sedan November 2015. Query Store är grunden för prestandaanalys och justeringsfunktioner, till exempel [SQL Database Advisor och prestanda instrumentpanel](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). För tillfället för publiceringen av den här artikeln körs Query Store i mer än 200 000 användardatabaser i Azure, samla in frågan-relaterad information i flera månader utan avbrott.

> [!IMPORTANT]
> Microsoft håller på att aktivera Query Store för alla Azure SQL-databaser (befintliga och nya). 

## <a name="optimal-query-store-configuration"></a>Optimala Query Store-konfiguration

Det här avsnittet beskrivs standardvärdena för optimal konfiguration som är utformade för att säkerställa tillförlitlig drift Query Store och beroende funktioner, till exempel [SQL Database Advisor och prestanda instrumentpanel](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Standardkonfigurationen är optimerat för kontinuerlig datainsamling som är minimal tid i OFF/READ_ONLY tillstånd.

| Konfiguration | Beskrivning | Standard | Kommentar |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Anger gränsen för datautrymme som Query Store kan vidta i kunddatabasen |100 |Tillämpas för nya databaser |
| INTERVAL_LENGTH_MINUTES |Definierar storleken på tidsperioden under vilken insamlade körningsstatistik för frågeplaner aggregeras och sparas. Varje aktiv frågeplan har högst en rad under en tidsperiod som definierats med den här konfigurationen |60 |Tillämpas för nya databaser |
| STALE_QUERY_THRESHOLD_DAYS |Tidsbaserade upprensningsprincip som styr kvarhållningsperioden för beständiga körningsstatistik och inaktiva frågor |30 |Tillämpas för nya databaser och databaser med föregående standard (367) |
| SIZE_BASED_CLEANUP_MODE |Anger om automatisk slutfasen i rensningen sker när Query Store datastorlek närmar sig gränsen |AUTOMATISK |Gäller för alla databaser |
| QUERY_CAPTURE_MODE |Anger om alla frågor eller endast en delmängd av frågor spåras |AUTOMATISK |Gäller för alla databaser |
| FLUSH_INTERVAL_SECONDS |Anger maximal tidsperiod under vilken avbildas runtime statistik sparas i minnet, innan till disk |900 |Tillämpas för nya databaser |
|  | | | |

> [!IMPORTANT]
> Dessa standardinställningar tillämpas automatiskt i det sista steget i Query Store-aktivering i alla Azure SQL-databaser (se föregående viktigt meddelande). Efter den här lyser ändra Azure SQL Database inte konfigurationsvärden som angetts av kunder, om inte de försämrade primära arbetsbelastning eller tillförlitliga åtgärder av Query Store.

Om du vill hålla dig med de anpassade inställningarna kan använda [ALTER DATABASE med Query Store alternativ](https://msdn.microsoft.com/library/bb522682.aspx) att återställa konfigurationen till föregående tillstånd. Kolla in [bästa praxis med Query Store](https://msdn.microsoft.com/library/mt604821.aspx) för att lära dig hur upp valde optimala konfigurationsparametrar.

## <a name="next-steps"></a>Nästa steg

[SQL Database Performance Insight](sql-database-performance.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande artiklar:

- [En svart låda för data för din databas](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Övervakning av prestanda med hjälp av Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
- [Query Store-användningsscenarier](https://msdn.microsoft.com/library/mt614796.aspx)
