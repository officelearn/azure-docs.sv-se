---
title: Operativ Frågearkivet i Azure SQL Database
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
ms.date: 12/19/2018
ms.openlocfilehash: b4f999818fe3b3517ee3fb48c22e616ee50f2d88
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567142"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Kör Frågearkivet i Azure SQL Database

Query Store i Azure är en fullständigt hanterad databas funktion som kontinuerligt samlar in och presenterar detaljerad historisk information om alla frågor. Du kan tänka på Frågearkivet på ett sätt som liknar flyg Plans data inspelaren, vilket avsevärt fören klar fel sökning av frågor både för molnbaserade och lokala kunder. I den här artikeln beskrivs olika aspekter av operativ Frågearkivet i Azure. Med den här församlade frågans data kan du snabbt diagnostisera och lösa prestanda problem och därmed ägna mer tid åt att fokusera på deras verksamhet. 

Frågearkivet är [globalt tillgängligt](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) i Azure SQL Database sedan november 2015. Query Store är grunden för prestanda analys och justerings funktioner, till exempel [SQL Database Advisor och prestanda instrument panel](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Vid publiceringen av den här artikeln körs Frågearkivet i fler än 200 000 användar databaser i Azure och samlar in information om frågor i flera månader, utan avbrott.

> [!IMPORTANT]
> Microsoft håller på att aktivera Query Store för alla Azure SQL-databaser (befintliga och nya). 

## <a name="optimal-query-store-configuration"></a>Optimalt fråge arkivs konfiguration

I det här avsnittet beskrivs optimala konfigurations standarder som är utformade för att säkerställa en tillförlitlig åtgärd i Frågearkivet och beroende funktioner, till exempel [SQL Database Advisor och prestanda instrument panel](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Standard konfigurationen är optimerad för kontinuerlig insamling av data, vilket är den minsta tid som ägnas åt av/READ_ONLY tillstånd.

| Konfiguration | Beskrivning | Standard | Kommentar |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Anger gränsen för det data utrymme som Query Store kan ta inuti kund databasen |100 |Framtvingat för nya databaser |
| INTERVAL_LENGTH_MINUTES |Definierar storleken på tids perioden under vilken insamlad körnings statistik för frågeplan ska aggregeras och sparas. Varje aktiv frågeplan har högst en rad under en tids period som definierats med den här konfigurationen |60 |Framtvingat för nya databaser |
| STALE_QUERY_THRESHOLD_DAYS |Tidsbaserad rensnings princip som styr kvarhållningsperioden för beständig körnings statistik och inaktiva frågor |30 |Framtvingad för nya databaser och databaser med föregående standard (367) |
| SIZE_BASED_CLEANUP_MODE |Anger om automatisk data rensning äger rum när data storleken i Frågearkivet närmar sig gränsen |DISK |Tillämpas för alla databaser |
| QUERY_CAPTURE_MODE |Anger om alla frågor eller endast en delmängd av frågor spåras |DISK |Tillämpas för alla databaser |
| FLUSH_INTERVAL_SECONDS |Anger den maximala period under vilken insamlad körnings statistik sparas i minnet innan den töms till disk |900 |Framtvingat för nya databaser |
|  | | | |

> [!IMPORTANT]
> Dessa standardvärden används automatiskt i det sista steget i aktiveringen av Frågearkivet i alla Azure SQL-databaser (se föregående viktiga kommentar). Efter den här åtgärden kan Azure SQL Database inte ändra konfigurations värden som anges av kunderna, om de inte negativt påverkar den primära arbets belastningen eller tillförlitliga åtgärder i Frågearkivet.

Om du vill behålla dina anpassade inställningar använder du [Alter Database med Query Store-alternativ](https://msdn.microsoft.com/library/bb522682.aspx) för att återställa konfigurationen till föregående tillstånd. Kolla in [bästa praxis med frågearkivet](https://msdn.microsoft.com/library/mt604821.aspx) för att lära dig hur du väljer bästa konfigurations parametrar överst.

## <a name="next-steps"></a>Nästa steg

[SQL Database prestanda insikter](sql-database-performance.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande artiklar:

- [En flyg data registrering för din databas](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Övervaka prestanda med hjälp av Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
- [Query Store-användningsscenarier](https://msdn.microsoft.com/library/mt614796.aspx)
