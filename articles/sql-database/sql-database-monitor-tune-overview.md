---
title: Övervakning och prestandajustering – Azure SQL Database | Microsoft Docs
description: Tips för justering i Azure SQL Database via utvärdera och förbättra prestanda.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/06/2018
ms.openlocfilehash: 7a83bb527a4ccea10e8d9d59d9b5f8e6e75ae106
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857813"
---
# <a name="monitoring-and-performance-tuning"></a>Övervakning och prestandajustering

Azure SQL Database hanteras automatiskt och flexibla datatjänst där du kan enkelt övervaka användning, lägga till eller ta bort resurser (processor, minne, i/o), hitta rekommendationer som kan förbättra databasens prestanda eller låt databasen som anpassas efter din arbetsbelastning och automatiskt optimera prestanda.

## <a name="the-state-of-an-active-query"></a>Status för aktiva frågor

För att förbättra prestandan för Azure SQL Database, förstår du att varje aktiva frågebegäran från ditt program är antingen i en löpande eller väntande läge. När du felsöker problem med prestandan i Azure SQL Database, Tänk på följande diagram:

![Tillstånd för arbetsbelastning](./media/sql-database-monitor-tune-overview/workload-states.png)

För en arbetsbelastning med prestandaproblem, utfärda prestanda Mina bero på CPU-konkurrens (en **körs-relaterade** villkor) eller enskilda frågor väntar på något (en **väntar-relaterade** villkor) .

- **Hög CPU-användning i Azure SQL database**:

  Du kan se överdriven CPU användning orsakar prestandaproblem på följande villkor:

  - För många frågor som körs
  - För många kompilerar frågor
  - En eller flera av de frågor som körs använder en icke-optimala frågeplan

  Om så är fallet för din arbetsbelastning är målet att identifiera och finjustera frågorna eller uppgradera beräkningsstorleken eller tjänstenivån för att öka kapaciteten för din Azure SQL-databas att absorbera CPU-kraven. Läs mer i skala resurser för enskilda databaser [skala resurser för enkel databas i Azure SQL Database](sql-database-single-database-scale.md) och skala resurser för elastiska pooler finns i [skala resurser för elastisk pool i Azure SQL Databasen](sql-database-elastic-pool-scale.md).

- **En enskild fråga väntar på något**

  Enskilda frågor kan ha prestandaproblem på grund av frågan väntar på att något. Målet är att ta bort eller minska väntetiden i det här scenariot.

### <a name="determine-if-you-have-a-running-related-performance-issue"></a>Avgör om du har ett körs minnesrelaterade prestandaproblem

Du kan identifiera körs minnesrelaterade prestandaproblem på flera olika sätt. De vanligaste metoderna är:

- Använd den [Azure-portalen](#monitor-databases-using-the-azure-portal) övervakar processoranvändning i procent.
- Använd följande [dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) returnerar CPU, I/O och minne i samband med en Azure SQL Database-databas. Det finns en rad för var 15: e sekund, även om det finns ingen aktivitet i databasen. Historiska data bevaras under en timme.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) returnerar CPU-användning och lagring data för en Azure SQL Database. Data som samlas in och sammanställs inom fem minuter långa intervall.

> [!TIP]
> Som en generell riktlinje CPU-användning är konsekvent vid eller över 80%, måste köras minnesrelaterade prestandaproblem.

### <a name="determine-if-you-have-a-waiting-related-performance-issue"></a>Avgör om du har en väntande minnesrelaterade prestandaproblem

Först måste vara säker på att detta inte är en hög CPU, körs minnesrelaterade prestandaproblem. Om den inte är nästa steg att identifiera övre väntar som är associerade med din arbetsbelastning.  Vanliga metoder för att visa upp vänta kategorier av typen:

- Den [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) innehåller vänta statistik per fråga över tid. I Query Store kombineras vänta typer i vänta kategorier. Mappningen av vänta kategorier för att vänta typer finns i [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql.md#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) returnerar information om den väntar påträffades av trådar som körs under åtgärden. Du kan använda den här aggregerade vyn för att diagnostisera prestandaproblem med Azure SQL Database och med specifika frågor och -batchar.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) returnerar information om kön vänta på uppgifter som väntar på några resurser.

I det föregående diagrammet visas är den vanligaste väntar:

- Lås (blockera)
- I/O
- TempDB-relaterade konkurrens
- Minne bevilja väntar

Beroende på vad som visas har varje wait-kategori en annan sökväg för felsökning.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Översikt över övervaka databasprestanda i Azure SQL Database

Prestandaövervakning för en SQL-databas i Azure startar med att övervaka resursutnyttjandet i förhållande till nivån på databasprestanda som du valt. Övervakning hjälper dig att avgöra om din databas har överflödig kapacitet eller har problem med eftersom resurserna är överutnyttjade ut och sedan bestämmer om det är dags att justera beräkningsstorleken och tjänstnivåer för din databas i den [DTU-baserade köpa modellen](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md). Du kan övervaka din databas i den [Azure-portalen](https://portal.azure.com) med hjälp av följande grafiska verktyg eller med hjälp av SQL [dynamiska hanteringsvyer (DMV)](sql-database-monitoring-with-dmvs.md).

Azure SQL Database hjälper dig att identifiera möjligheter att förbättra och optimera frågeprestanda utan att ändra resurser genom att granska [rekommendationer för prestandajustering](sql-database-advisor.md). Index som saknas och dåligt optimerade frågor är vanliga orsaker till dåliga databasprestanda. Du kan använda de här justeringsrekommendationerna att förbättra prestanda för din arbetsbelastning.
Du kan också låta Azure SQL database till [automatiskt optimera prestandan för dina frågor](sql-database-automatic-tuning.md) genom att tillämpa alla identifierade rekommendationer och verifiera att de förbättras databasens prestanda. Du har följande alternativ för övervakning och felsökning databasprestanda:

- I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**, väljer du databasen och sedan använda övervakning diagrammet för att söka efter resurser som närmar sig sin maximala. DTU-användning visas som standard. Klicka på **redigera** att ändra tidsintervall och värden som visas.
- Använd [Query Performance Insight](sql-database-query-performance.md) att identifiera de frågor som lägger ut det mesta av resurser.
- Använd [SQL Database Advisor](sql-database-advisor-portal.md) att visa rekommendationer för att skapa och släppa index, Parameterisera frågorna och åtgärda problem med databasscheman.
- Använd [smarta insikter för Azure SQL](sql-database-intelligent-insights.md) för automatisk övervakning av databasens prestanda. När ett prestandaproblem har identifierats genereras en diagnostiklogg med information om och rot orsak Analysis (RCA) av problemet. Rekommendation för förbättring av prestanda tillhandahålls när det är möjligt.
- [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) och låta Azure SQL-databasen automatiskt korrigering identifieras prestandaproblem.
- Du kan också använda [dynamiska hanteringsvyer (DMV)](sql-database-monitoring-with-dmvs.md), [utökade händelser (`XEvents`) (sql-database/sql-database-xevent-db-diff-from-svr.md), och [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) för prestandaövervakning parametrar i realtid. Se [prestandavägledning](sql-database-performance-guidance.md) att hitta tekniker som du kan använda för att förbättra prestanda i Azure SQL Database om du har identifierat några problem med hjälp av dessa rapporter eller vyer.

## <a name="monitor-databases-using-the-azure-portal"></a>Övervaka databaser med Azure-portalen

I [Azure-portalen](https://portal.azure.com/), kan du övervaka en enskild databas resursutnyttjning genom att välja din databas och klicka på **Övervaknings**-diagrammet. Det öppnar **Mått**-fönstret, vilket du kan ändra genom att klicka på **Redigera diagram**-knappen. Lägg till följande mått:

- CPU-procent
- DTU-procent
- Data IO-procent
- Databasstorlek i procent

När du har lagt till de här måtten, kan du fortsätta att visa dem i den **övervakning** diagram med mer information på den **mått** fönster. Alla fyra mätvärdena visar ett snittvärde för utnyttjandeprocent i förhållande till din databas **DTU:er**. Se den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) artiklar för mer information om tjänstnivåer.  

![Tjänstnivå-övervakning av databasprestanda.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Du kan också konfigurera aviseringar på prestandamåtten. Klicka på knappen **Lägg till avisering** i **mått**-fönstret. Följ guiden för att konfigurera aviseringen. Du har möjlighet att avisera om måttet överskrider ett visst tröskelvärde eller om måttet faller under ett visst tröskelvärde.

Om du exempelvis förväntar dig att arbetsbelastningen på din databas kommer att öka, kan du välja att konfigurera en e-postavisering när din databas kommer upp i 80 % för något av prestandamåtten. Du kan använda detta som en tidig varning att veta när du kanske att växla till nästa högre beräkningsstorleken.

Prestandamåtten kan också hjälpa dig att avgöra om du kan Nedgradera till en lägre beräkningsstorleken. Anta att du använder en Standard S2-databas och alla prestandamått visar att databasen i snitt inte använder mer än 10 % vid något tillfälle. Det är då troligt att databasen skulle fungera bra i Standard S1. Dock vara medveten om arbetsbelastningar som varierar kraftigt innan du beslutar att flytta till en lägre beräkningsstorleken.

## <a name="improving-database-performance-with-more-resources"></a>Förbättra databasens prestanda med fler resurser

Slutligen, om det finns inga användbara objekt som kan förbättra databasens prestanda kan du ändra mängden resurser som är tillgängliga i Azure SQL Database. Du kan tilldela mer resurser genom att ändra den [DTU tjänstnivå](sql-database-service-tiers-dtu.md) för en enkel databas eller öka edtu: er för en elastisk pool när som helst. Du kan också om du använder den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md), du kan ändra antingen tjänstnivå eller öka resurserna för din databas.

1. För enskilda databaser kan du [ändra tjänstnivåer](sql-database-service-tiers-dtu.md) eller [beräkningsresurser](sql-database-service-tiers-vcore.md) på begäran för att förbättra databasprestanda.
2. Överväg att använda för flera databaser [elastiska pooler](sql-database-elastic-pool-guidance.md) att skala resurser automatiskt.

## <a name="tune-and-refactor-application-or-database-code"></a>Justera och omstrukturera program eller databasen

Du kan ändra koden för att använda databasen, ändra index, tvinga planer på eller använda tips att manuellt anpassa databasen till din arbetsbelastning mer optimalt. Hitta vissa vägledning och tips för manuell justering och skriva om koden i den [prestanda vägledning avsnittet](sql-database-performance-guidance.md) artikeln.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera automatisk justering i Azure SQL Database och att funktionen för automatisk justering helt hantera din arbetsbelastning kan se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- För att använda manuell inställning, kan du granska [Justeringsrekommendationer i Azure-portalen](sql-database-advisor-portal.md) och tillämpa manuellt de som förbättrar prestandan för dina frågor.
- Ändra resurser som är tillgängliga i din databas genom att ändra [Azure SQL Database-tjänstnivåer](sql-database-performance-guidance.md)
