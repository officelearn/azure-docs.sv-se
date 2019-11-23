---
title: Elastic Database-jobb (förhandsversion)
description: Configure Elastic Database Jobs (preview) to run Transact-SQL (T-SQL) scripts across a set of one or more Azure SQL databases
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
ms.openlocfilehash: 283b4004f34372104eb083496400772884f5965e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420374"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Create, configure, and manage elastic jobs

In this article, you will learn how to create, configure, and manage elastic jobs.

If you have not used Elastic jobs, [learn more about the job automation concepts in Azure SQL Database](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Skapa och konfigurera agenten

1. Skapa eller identifiera en tom SQL-databas på S0-nivå eller högre. This database will be used as the *Job database* during Elastic Job agent creation.
2. Create an Elastic Job agent in the [portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) or with [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Creating Elastic Job agent](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Skapa, kör och hantera jobb

1. Create a credential for job execution in the *Job database* using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Define the target group (the databases you want to run the job against) using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. Skapa autentiseringsuppgifter för en jobbagent i varje databas som jobbet ska köras mot [(lägg till användaren (eller rollen) till varje databas i gruppen)](sql-database-control-access.md). Exempel finns i [PowerShell-självstudien](elastic-jobs-powershell.md).
4. Create a job using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Lägg till jobbsteg med [PowerShell](elastic-jobs-powershell.md) eller [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
6. Run a job using [PowerShell](elastic-jobs-powershell.md#run-the-job) or [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Monitor job execution status using the portal, [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) or [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portalen](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Autentiseringsuppgifter för att köra jobb

Jobbet använder [databasbegränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) för att ansluta till de databaser som anges av målgruppen vid körning. Om en målgrupp innehåller servrar eller pooler används de här databasbegränsade autentiseringsuppgifterna för att ansluta till huvuddatabasen för att räkna upp de tillgängliga databaserna.

Att konfigurera rätt autentiseringsuppgifter för att köra ett jobb kan vara något förvirrande, så tänk på följande punkter:

- De databasbegränsade autentiseringsuppgifterna måste skapas i *jobbdatabasen*.
- **All target databases must have a login with [sufficient permissions](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) for the job to complete successfully** (`jobuser` in the diagram below).
- Credentials can be reused across jobs, and the credential passwords are encrypted and secured from users who have read-only access to job objects.

Följande bild är utformad för att underlätta förståelse och konfiguration av rätt autentiseringsuppgifter för jobb. **Kom ihåg att skapa användaren i varje databas (alla *målanvändardatabaser*) som jobbet behöver köras mot**.

![Autentiseringsuppgifter för elastiska jobb](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Metodtips för säkerhet

Några metodtips för att arbeta med elastiska jobb:

- Begränsa användningen av API:er till betrodda personer.
- Autentiseringsuppgifter ska ha minsta möjliga behörigheter som krävs för att utföra jobbsteget. For more information, see [Authorization and Permissions SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- When using a server and/or pool target group member, it is highly suggested to create a separate credential with rights on the master database to view/list databases that is used to expand the database lists of the server(s) and/or pool(s) prior to the job execution.

## <a name="agent-performance-capacity-and-limitations"></a>Agentprestanda, kapacitet och begränsningar

Elastiska jobb använder minimalt med beräkningsresurser medan de väntar på att jobb med lång körningstid blir klara.

Beroende på storleken på målgruppen med databaser och önskad körningstid för ett jobb (antalet samtidiga arbetare) kräver agenten olika mängder beräkningsresurser och prestanda från *jobbdatabasen* (ju fler mål och ju högre antal jobb, desto större beräkningsresurser krävs det).

För tillfället är förhandsgranskningen begränsad till 100 samtidiga jobb.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Förhindra jobb från att minska måldatabasens prestanda

För att resurser inte ska överbelastas vid körning av jobb mot databaser i en elastisk SQL-pool kan jobb konfigureras för att begränsa antalet databaser som ett jobb kan köras mot samtidigt.

Set the number of concurrent databases a job runs on by setting the `sp_add_jobstep` stored procedure's `@max_parallelism` parameter in T-SQL, or `Add-AzSqlElasticJobStep -MaxParallelism` in PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Metodtips för att skapa jobb

### <a name="idempotent-scripts"></a>Idempotent-skript
T-SQL-skripten för ett jobb måste vara [idempotent](https://en.wikipedia.org/wiki/Idempotence). **Idempotent** innebär att om skriptet lyckas och körs igen, så erhålls samma resultat. Ett skript kan misslyckas på grund av tillfälliga nätverksproblem. I så fall försöker jobbet automatiskt köra skriptet på nytt ett förinställt antal gånger innan det ger upp. Ett idempotent-skript ger samma resultat även om det har lyckats köra två eller fler gånger.

En enkel metod är att testa om ett objekt finns innan det skapas.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

På liknande sätt måste ett skript kunna lyckas köra genom att logiskt testa och motverka eventuella tillstånd som det hittar.



## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell.md)
- [Skapa och hantera elastiska jobb med Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
