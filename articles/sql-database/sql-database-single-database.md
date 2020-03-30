---
title: Vad är en enda databas
description: Lär dig mer om en databas i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: fc63de4057def632d3ac1980e8cb3eaedbff2175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500754"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Vad är en enda databas i Azure SQL Database

Alternativet distribution av en enda databas skapar en databas i Azure SQL Database med sin egen uppsättning resurser och hanteras via en SQL Database-server. Med en enda databas är varje databas isolerad från varandra och bärbar, var och en med sin egen tjänstnivå inom den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) och en garanterad beräkningsstorlek.

> [!IMPORTANT]
> En enda databas är ett av tre distributionsalternativ för Azure SQL Database. De andra två är [elastiska pooler](sql-database-elastic-pool.md) och [hanterad instans](sql-database-managed-instance.md).
> [!NOTE]
> En ordlista med termer i Azure SQL Database finns i [ordlistan för SQL-databastermer](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dynamisk skalbarhet

Du kan skapa din första app på en liten, enda databas till låg kostnad i den serverlösa beräkningsnivån eller en liten beräkningsstorlek i den etablerade beräkningsnivån. Du ändrar [beräknings- eller tjänstnivån](sql-database-single-database-scale.md) manuellt eller programmässigt när som helst för att uppfylla behoven hos din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

## <a name="single-databases-and-elastic-pools"></a>Enkla databaser och elastiska pooler

En enskild databas kan flyttas till eller från en [elastisk pool](sql-database-elastic-pool.md) för resursdelning. För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. Elastiska pooler är utformade för att lösa detta problem. Konceptet är enkelt. Du allokerar prestandaresurser till en pool i stället för en enskild databas och betalar för poolens kollektiva prestandaresurser i stället för för enskilda databasprestanda.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Du använder de inbyggda [verktygen för prestandaövervakning](sql-database-performance-guidance.md) och [aviseringar](sql-database-insights-alerts-portal.md)i kombination med prestandaklassificeringarna. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner baserat på dina aktuella eller projekterade prestandakrav. Dessutom kan SQL Database [avge mått och resursloggar](sql-database-metrics-diag-logging.md) för enklare övervakning.

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

Enskilda databaser, elastiska pooler och hanterade instanser ger alla många tillgänglighetsegenskaper. Information finns i [Tillgänglighetsegenskaper](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Transact-SQL-skillnader

De flesta Transact-SQL-funktioner som program använder stöds fullt ut i både Microsoft SQL Server och Azure SQL Database. Till exempel fungerar de centrala SQL-komponenterna, till exempel datatyper, operatorer, sträng-, aritmetiska, logiska och markörfunktioner, på samma sätt i SQL Server och SQL Database. Det finns dock några T-SQL-skillnader i DDL -element (datadefinitionsspråk) och DML-element (datamanipulationsspråk) som resulterar i T-SQL-satser och frågor som bara delvis stöds (som vi diskuterar senare i den här artikeln).
Dessutom finns det vissa funktioner och syntax som inte stöds alls eftersom Azure SQL Database är utformad för att isolera funktioner från beroenden på huvuddatabasen och operativsystemet. De flesta aktiviteter på servernivå är därför olämpliga för SQL Database. T-SQL-satser och -alternativ är inte tillgängliga om de konfigurerar alternativ på servernivå, operativsystemkomponenter eller anger filsystemkonfiguration. När sådana funktioner krävs är ett lämpligt alternativ ofta tillgängligt på annat sätt från SQL Database eller från en annan Azure-funktion eller -tjänst.

Mer information finns i [Lösa Transact-SQL-skillnader under migreringen till SQL Database](sql-database-transact-sql-information.md).

## <a name="security"></a>Säkerhet

SQL Database innehåller en rad [inbyggda säkerhets- och efterlevnadsfunktioner](sql-database-security-overview.md) som hjälper ditt program att uppfylla olika säkerhets- och efterlevnadskrav.

> [!IMPORTANT]
> Azure SQL Database (alla distributionsalternativ) har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan över SQL Database-efterlevnadscertifieringar.

## <a name="next-steps"></a>Nästa steg

- Börja med [snabbstartsguiden för](sql-database-single-database-quickstart-guide.md)en enda databas om du snabbt vill komma igång med en enda databas .
- Mer information om hur du migrerar en SQL Server-databas till Azure finns i [Migrera till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
