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
ms.date: 10/23/2018
ms.openlocfilehash: 0d728d81a29c5520938c8553c026727c0f94cc43
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957011"
---
# <a name="monitoring-and-performance-tuning"></a>Övervakning och prestandajustering

Azure SQL Database hanteras automatiskt och flexibla datatjänst där du kan enkelt övervaka användning, lägga till eller ta bort resurser (processor, minne och I/O), hitta rekommendationer som kan förbättra databasens prestanda eller låt databasen som anpassas efter din arbetsbelastning och automatiskt optimera prestanda.

## <a name="monitoring-database-performance"></a>Övervaka databasprestanda

Prestandaövervakning för en SQL-databas i Azure startar med att övervaka resursutnyttjandet i förhållande till nivån på databasprestanda som du valt. Azure SQL Database hjälper dig att identifiera möjligheter att förbättra och optimera frågeprestanda utan att ändra resurser genom att granska [rekommendationer för prestandajustering](sql-database-advisor.md). Index som saknas och dåligt optimerade frågor är vanliga orsaker till dåliga databasprestanda. Du kan använda de här justeringsrekommendationerna att förbättra prestanda för din arbetsbelastning. Du kan också låta Azure SQL database till [automatiskt optimera prestandan för dina frågor](sql-database-automatic-tuning.md) genom att tillämpa alla identifierade rekommendationer och verifiera att de förbättras databasens prestanda.

Du har följande alternativ för övervakning och felsökning databasprestanda:

- I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-databaser**, väljer du databasen och sedan använda övervakning diagrammet för att söka efter resurser som närmar sig sin maximala. DTU-användning visas som standard. Klicka på **redigera** att ändra tidsintervall och värden som visas.
- Använd [Query Performance Insight](sql-database-query-performance.md) att identifiera de frågor som lägger ut det mesta av resurser.
- Använd [SQL Database Advisor](sql-database-advisor-portal.md) att visa rekommendationer för att skapa och släppa index, Parameterisera frågorna och åtgärda problem med databasscheman.
- Använd [smarta insikter för Azure SQL](sql-database-intelligent-insights.md) för automatisk övervakning av databasens prestanda. När ett prestandaproblem har identifierats genereras en diagnostiklogg med information om och rot orsak Analysis (RCA) av problemet. Rekommendation för förbättring av prestanda tillhandahålls när det är möjligt.
- [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md) och låta Azure SQL-databasen automatiskt korrigering identifieras prestandaproblem.
- Använd [dynamiska hanteringsvyer (DMV)](sql-database-monitoring-with-dmvs.md), [utökade händelser](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr), och [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) mer detaljerad felsökning av problem med prestanda.

> [!TIP]
> Se [prestandavägledning](sql-database-performance-guidance.md) att hitta tekniker som du kan använda för att förbättra prestanda i Azure SQL Database när du har identifierat prestandaproblemet med hjälp av en eller flera av metoderna ovan.

## <a name="monitor-databases-using-the-azure-portal"></a>Övervaka databaser med Azure-portalen

I den [Azure-portalen](https://portal.azure.com/), du kan övervaka en enskild databas s användning genom att välja databasen och klicka på den **övervakning** diagram. Det öppnar **Mått**-fönstret, vilket du kan ändra genom att klicka på **Redigera diagram**-knappen. Lägg till följande mått:

- CPU-procent
- DTU-procent
- Data IO-procent
- Databasstorlek i procent

När du har lagt till de här måtten, kan du fortsätta att visa dem i den **övervakning** diagram med mer information på den **mått** fönster. Alla fyra mätvärdena visar ett snittvärde för utnyttjandeprocent i förhållande till din databas **DTU:er**. Se den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) artiklar för mer information om tjänstnivåer.  

![Tjänstnivå-övervakning av databasprestanda.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Du kan också konfigurera aviseringar på prestandamåtten. Klicka på knappen **Lägg till avisering** i **mått**-fönstret. Följ guiden för att konfigurera aviseringen. Du har möjlighet att avisera om måttet överskrider ett visst tröskelvärde eller om måttet faller under ett visst tröskelvärde.

Om du exempelvis förväntar dig att arbetsbelastningen på din databas kommer att öka, kan du välja att konfigurera en e-postavisering när din databas kommer upp i 80 % för något av prestandamåtten. Du kan använda detta som en tidig varning att veta när du kanske att växla till nästa högsta beräkningsstorleken.

Prestandamåtten kan också hjälpa dig att avgöra om du kan Nedgradera till en lägre beräkningsstorleken. Anta att du använder en Standard S2-databas och alla prestandamått visar att databasen i snitt inte använder mer än 10 % vid något tillfälle. Det är då troligt att databasen skulle fungera bra i Standard S1. Dock vara medveten om arbetsbelastningar som varierar kraftigt innan du beslutar att flytta till en lägre beräkningsstorleken.

## <a name="troubleshoot-performance-issues"></a>Felsöka prestandaproblem

Börja med att förstå statusen för varje aktiv fråga och de villkor som kan orsakar prestandaproblem som är relevanta för varje arbetsbelastning tillstånd för att diagnostisera och lösa prestandaproblem. För att förbättra prestandan för Azure SQL Database, förstår du att varje aktiva frågebegäran från ditt program är antingen i en löpande eller väntande läge. När du felsöker problem med prestandan i Azure SQL Database, Tänk på följande diagram som du läser igenom den här artikeln att diagnostisera och lösa prestandaproblem.

![Tillstånd för arbetsbelastning](./media/sql-database-monitor-tune-overview/workload-states.png)

För en arbetsbelastning med prestandaproblem prestandaproblemet kan bero på CPU-konkurrens (en **körs-relaterade** villkor) eller enskilda frågor väntar på något (en **väntar-relaterade** villkor ).

## <a name="running-related-performance-issues"></a>Körs minnesrelaterade prestandaproblem

Som en generell riktlinje CPU-användning är konsekvent vid eller över 80%, måste köras minnesrelaterade prestandaproblem. Om du har en körs problem det kan bero på otillräckliga processorresurser eller kan vara relaterade till något av följande villkor:

- För många frågor som körs
- För många kompilerar frågor
- En eller flera av de frågor som körs använder en icke-optimala frågeplan

Om du har fastställt att du har ett körs minnesrelaterade prestandaproblem, är målet att identifiera exakta problemet med hjälp av en eller flera metoder. De vanligaste metoderna för att identifiera körs-relaterade problem är:

- Använd den [Azure-portalen](#monitor-databases-using-the-azure-portal) övervakar processoranvändning i procent.
- Använd följande [dynamiska hanteringsvyer](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) returnerar CPU, I/O och minne i samband med en Azure SQL Database-databas. Det finns en rad för var 15: e sekund, även om det finns ingen aktivitet i databasen. Historiska data bevaras under en timme.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) returnerar CPU-användning och lagring data för en Azure SQL Database. Data som samlas in och sammanställs inom fem minuter långa intervall.

> [!IMPORTANT]
> En uppsättning en T-SQL-frågor med dessa DMV: er för att felsöka problem med CPU, finns i [identifiera CPU prestandaproblem](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

När du har identifierat problemet kan du justera problemet frågor eller uppgradera beräkningsstorleken eller tjänstnivån ökar du kapaciteten för din Azure SQL database att absorbera CPU-kraven. Information om att skala resurser för enskilda databaser finns i [skala resurser för enkel databas i Azure SQL Database](sql-database-single-database-scale.md) och skala resurser för elastiska pooler finns i [skala resurser för elastisk pool i Azure SQL Databasen](sql-database-elastic-pool-scale.md). Information om att skala en hanterad instans finns i [på instansnivå resursbegränsningar](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

## <a name="waiting-related-performance-issues"></a>Väntetiden minnesrelaterade prestandaproblem

När du är säker på att du inte får en hög CPU, körs minnesrelaterade prestandaproblem, får du vänta minnesrelaterade prestandaproblem. Nämligen som CPU-resurser inte används effektivt eftersom Processorn väntar på några andra resurser. I det här fallet är nästa steg att identifiera vad din processorresurser väntar på. De vanligaste metoderna för att visa upp vänta kategorier av typen:

- Den [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) innehåller vänta statistik per fråga över tid. I Query Store kombineras vänta typer i vänta kategorier. Mappningen av vänta kategorier för att vänta typer finns i [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) returnerar information om den väntar påträffades av trådar som körs under åtgärden. Du kan använda den här aggregerade vyn för att diagnostisera prestandaproblem med Azure SQL Database och med specifika frågor och -batchar.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) returnerar information om kön vänta på uppgifter som väntar på några resurser.

I det föregående diagrammet visas är den vanligaste väntar:

- Lås (blockera)
- I/O
- `tempdb`-relaterade konkurrens
- Minne bevilja väntar

> [!IMPORTANT]
> En uppsättning finns en T-SQL-frågor med dessa DMV: er för att felsöka problemen väntar-relaterade:
>
> - [Identifiera prestandaproblem för i/o](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifiera `tempdb` prestandaproblem](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identifiera minne bevilja väntar](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)

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
