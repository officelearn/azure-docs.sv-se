---
title: Azure SQL Elastic Database-jobb | Microsoft Docs
description: Konfigurera Elastic Database-jobb för att köra Transact-SQL (T-SQL)-skript i en uppsättning med en eller flera Azure SQL-databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srinia
ms.author: srinia
ms.reviewer: sstein
manager: craigg
ms.date: 01/22/2018
ms.openlocfilehash: ee9e9389962cd454f9861e561dd76c96d019a1f0
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469350"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Skapa, konfigurera och hantera elastiska jobb

I den här artikeln får lära du dig att skapa, konfigurera och hantera elastiska jobb. Om du inte har använt elastiska jobb [mer information om jobbet automation begreppen i Azure SQL Database](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Skapa och konfigurera agenten

1. Skapa eller identifiera en tom SQL-databas på S0-nivå eller högre. Den här databasen kommer att användas som den *jobbet databasen* under skapande av elastiska jobb agent.
2. Skapa en elastisk jobbagent i [portalen](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) eller med [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Skapa elastiska jobbagenten](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Skapa, kör och hantera jobb

1. Skapa autentiseringsuppgifter för jobbkörning i *jobbdatabasen* med [PowerShell](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets) eller [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Definiera målgruppen (de databaser som du vill köra jobbet mot) med [PowerShell](elastic-jobs-powershell.md#define-the-target-databases-you-want-to-run-the-job-against) eller [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. Skapa autentiseringsuppgifter för en jobbagent i varje databas som jobbet ska köras mot [(lägg till användaren (eller rollen) till varje databas i gruppen)](sql-database-control-access.md). Exempel finns i [PowerShell-självstudien](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets).
4. Skapa ett jobb med [PowerShell](elastic-jobs-powershell.md#create-a-job) eller [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Lägg till jobbsteg med [PowerShell](elastic-jobs-powershell.md#create-a-job-step) eller [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
6. Kör ett jobb med [PowerShell](elastic-jobs-powershell.md#run-the-job) eller [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Övervaka jobbkörningsstatus med portalen, [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) eller [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portalen](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Autentiseringsuppgifter för att köra jobb

Jobbet använder [databasbegränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) för att ansluta till de databaser som anges av målgruppen vid körning. Om en målgrupp innehåller servrar eller pooler används de här databasbegränsade autentiseringsuppgifterna för att ansluta till huvuddatabasen för att räkna upp de tillgängliga databaserna.

Att konfigurera rätt autentiseringsuppgifter för att köra ett jobb kan vara något förvirrande, så tänk på följande punkter:

- De databasbegränsade autentiseringsuppgifterna måste skapas i *jobbdatabasen*.
- **Alla måldatabaserna måste ha en inloggning med [behörighet](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) för jobbet har slutförts** (`jobuser` i diagrammet nedan).
- Autentiseringsuppgifter kan återanvändas i jobb och lösenord för autentiseringsuppgifter krypteras och skyddas från användare som har skrivskyddad åtkomst till jobbobjekt.

Följande bild är utformad för att underlätta förståelse och konfiguration av rätt autentiseringsuppgifter för jobb. **Kom ihåg att skapa användaren i varje databas (alla *målanvändardatabaser*) som jobbet behöver köras mot**.

![Autentiseringsuppgifter för elastiska jobb](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Metodtips för säkerhet

Några metodtips för att arbeta med elastiska jobb:

- Begränsa användningen av API:er till betrodda personer.
- Autentiseringsuppgifter ska ha minsta möjliga behörigheter som krävs för att utföra jobbsteget. Mer information finns i [auktorisering och behörigheter för SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- När du använder en server och/eller pool mål medlem kan rekommenderar vi med hög att du skapar separata autentiseringsuppgifter med behörighet i master-databasen för att visa/lista databaser som används för att expandera listorna databasen av servrar och/eller lagringspoolerna före jobbkörningen.

## <a name="agent-performance-capacity-and-limitations"></a>Agentprestanda, kapacitet och begränsningar

Elastiska jobb använder minimalt med beräkningsresurser medan de väntar på att jobb med lång körningstid blir klara.

Beroende på storleken på målgruppen med databaser och önskad körningstid för ett jobb (antalet samtidiga arbetare) kräver agenten olika mängder beräkningsresurser och prestanda från *jobbdatabasen* (ju fler mål och ju högre antal jobb, desto större beräkningsresurser krävs det).

För tillfället är förhandsgranskningen begränsad till 100 samtidiga jobb.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Förhindra jobb från att minska måldatabasens prestanda

För att resurser inte ska överbelastas vid körning av jobb mot databaser i en elastisk SQL-pool kan jobb konfigureras för att begränsa antalet databaser som ett jobb kan köras mot samtidigt.

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
