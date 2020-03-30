---
title: Elastiska databasjobb (förhandsgranskning)
description: Konfigurera elastiska databasjobb (förhandsversion) för att köra Transact-SQL-skript (T-SQL) över en uppsättning av en eller flera Azure SQL-databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: e5b07ac0e9421cbca034b17c573cab16641f49f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214483"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Skapa, konfigurera och hantera elastiska jobb

I den här artikeln får du lära dig hur du skapar, konfigurerar och hanterar elastiska jobb.

Om du inte har använt Elastiska jobb [kan du läsa mer om jobbautomatiseringsbegreppen i Azure SQL Database](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Skapa och konfigurera agenten

1. Skapa eller identifiera en tom SQL-databas på S0-nivå eller högre. Den här databasen används som *jobbdatabas* under skapandet av elastiska jobbagenter.
2. Skapa en elastisk jobbagent i [portalen](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) eller med [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Skapa elastisk jobbagent](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Skapa, kör och hantera jobb

1. Skapa en autentiseringsuppgifter för jobbkörning i *jobbdatabasen* med [PowerShell](elastic-jobs-powershell.md) eller [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Definiera målgruppen (de databaser som du vill köra jobbet mot) med [PowerShell](elastic-jobs-powershell.md) eller [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. Skapa autentiseringsuppgifter för en jobbagent i varje databas som jobbet ska köras mot [(lägg till användaren (eller rollen) till varje databas i gruppen)](sql-database-manage-logins.md). Exempel finns i [PowerShell-självstudien](elastic-jobs-powershell.md).
4. Skapa ett jobb med [PowerShell](elastic-jobs-powershell.md) eller [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Lägg till jobbsteg med [PowerShell](elastic-jobs-powershell.md) eller [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
6. Kör ett jobb med [PowerShell](elastic-jobs-powershell.md#run-the-job) eller [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Övervaka jobbkörningsstatus med hjälp av portalen, [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) eller [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portalen](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Autentiseringsuppgifter för att köra jobb

Jobbet använder [databasbegränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) för att ansluta till de databaser som anges av målgruppen vid körning. Om en målgrupp innehåller servrar eller pooler används de här databasbegränsade autentiseringsuppgifterna för att ansluta till huvuddatabasen för att räkna upp de tillgängliga databaserna.

Att konfigurera rätt autentiseringsuppgifter för att köra ett jobb kan vara något förvirrande, så tänk på följande punkter:

- Autentiseringsuppgifterna för databasscope måste skapas i *jobbdatabasen*.
- **Alla måldatabaser måste ha en inloggning med [tillräcklig behörighet](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) för att jobbet ska kunna slutföras** (i`jobuser` diagrammet nedan).
- Autentiseringsuppgifter kan återanvändas mellan jobb och lösenorden för autentiseringsuppgifter krypteras och skyddas från användare som har skrivskyddad åtkomst till jobbobjekt.

Följande bild är utformad för att underlätta förståelse och konfiguration av rätt autentiseringsuppgifter för jobb. **Kom ihåg att skapa användaren i varje databas (alla *målanvändardatabaser*) som jobbet behöver köras mot**.

![Autentiseringsuppgifter för elastiska jobb](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Metodtips för säkerhet

Några metodtips för att arbeta med elastiska jobb:

- Begränsa användningen av API:er till betrodda personer.
- Autentiseringsuppgifter ska ha minsta möjliga behörigheter som krävs för att utföra jobbsteget. Mer information finns i [SQL Server för auktorisering och behörigheter](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- När du använder en server- och/eller poolmålgruppsmedlem föreslås det starkt att du skapar en separat autentiseringsmall med rättigheter i huvuddatabasen för att visa/lista databaser som används för att expandera databaslistorna för servern och/eller poolerna före jobbkörningen.

## <a name="agent-performance-capacity-and-limitations"></a>Agentprestanda, kapacitet och begränsningar

Elastiska jobb använder minimalt med beräkningsresurser medan de väntar på att jobb med lång körningstid blir klara.

Beroende på storleken på målgruppen med databaser och önskad körningstid för ett jobb (antalet samtidiga arbetare) kräver agenten olika mängder beräkningsresurser och prestanda från *jobbdatabasen* (ju fler mål och ju högre antal jobb, desto större beräkningsresurser krävs det).

För tillfället är förhandsgranskningen begränsad till 100 samtidiga jobb.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Förhindra jobb från att minska måldatabasens prestanda

För att resurser inte ska överbelastas vid körning av jobb mot databaser i en elastisk SQL-pool kan jobb konfigureras för att begränsa antalet databaser som ett jobb kan köras mot samtidigt.

Ange antalet samtidiga databaser som ett jobb körs `sp_add_jobstep` på genom `@max_parallelism` att ange den lagrade procedurens parameter i T-SQL eller `Add-AzSqlElasticJobStep -MaxParallelism` i PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Metodtips för att skapa jobb

### <a name="idempotent-scripts"></a>Idempotent-skript
T-SQL-skripten för ett jobb måste vara [idempotent](https://en.wikipedia.org/wiki/Idempotence). **Idempotent** innebär att om skriptet lyckas och körs igen, så erhålls samma resultat. Ett skript kan misslyckas på grund av tillfälliga nätverksproblem. I så fall försöker jobbet automatiskt köra skriptet på nytt ett förinställt antal gånger innan det ger upp. Ett idempotent-skript ger samma resultat även om det har lyckats köra två eller fler gånger.

En enkel metod är att testa om ett objekt finns innan det skapas.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

På liknande sätt måste ett skript kunna lyckas köra genom att logiskt testa och motverka eventuella tillstånd som det hittar.



## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell.md)
- [Skapa och hantera elastiska jobb med Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
