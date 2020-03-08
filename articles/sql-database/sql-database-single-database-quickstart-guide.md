---
title: Snabb start – enstaka databaser
description: Lär dig att snabbt komma igång med enkla databaser i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: 6070b53e5f906bc378402d98275b8f798f57b505
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674436"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Komma igång med enkla databaser i Azure SQL Database

[En enkel databas](sql-database-single-index.yml) är en fullständigt hanterad PaaS-databas som en tjänst (DbaaS) som är en perfekt lagringsmotor för moderna molnskapade program. I det här avsnittet lär du dig att snabbt konfigurera och skapa en enkel databas i SQL Database.

## <a name="quickstart-overview"></a>Snabbstart, översikt

I det här avsnittet visas en översikt av tillgängliga artiklar som hjälper dig att snabbt komma igång med enkla databaser. Följande snabbstarter gör det möjligt att snabbt skapa en enkel databas, konfigurera en brandväggsregel för databasserver och sedan importera en databas i den nya enkla databasen med hjälp av en `.bacpac`-fil:

- [Skapa en enskild databas med hjälp av Azure-portalen](sql-database-single-database-get-started.md).
- När databasen har skapats behöver du [skydda databasen genom att konfigurera brandväggsregler](sql-database-server-level-firewall-rule.md).
- Om du har en befintlig databas på SQL Server som du vill migrera till Azure bör du installera [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), som analyserar databaserna på SQL Server och hittar eventuella problem som blockerar migrering till distributionsalternativet med enkel databas. Om du inte hittar några problem kan du exportera din databas som en `.bacpac`-fil och [importera den med hjälp av Azure-portalen eller SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automatisera hanteringsåtgärder

Du kan använda PowerShell eller Azure CLI för att skapa, konfigurera och skala databasen.

- [Skapa och konfigurera en enskild databas med PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) eller [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)
- [Uppdatera en enskild databas och skala resurser med PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) eller [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrera till en enkel databas med minimal avbrottstid

Med de här snabbstarterna kan du snabbt skapa eller importera databasen till Azure med hjälp av en `.bacpac`-fil. Dock är `.bacpac`- och `.dacpac`-filer utformade för att snabbt flytta databaser mellan olika versioner av SQL Server och distributionsalternativ i Azure SQL Database eller för att implementera kontinuerlig integrering i din DevOps-pipeline. Den här metoden är dock inte avsedd för migrering av produktionsdatabaser med minimal avbrottstid, eftersom du skulle behöva sluta lägga till nya data, vänta tills exporten av källdatabasen till en `.bacpac`-fil är klar och sedan vänta tills importen till Azure SQL Database är klar. All denna väntan resulterar i avbrottstid för ditt program, särskilt för stora databaser. För att flytta produktionsdatabasen behöver du ett bättre sätt att migrera som garanterar minimal avbrottstid för migrering. För detta använder du [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) för att migrera din databas med minimal avbrottstid. DMS åstadkommer detta genom att inkrementellt överföra ändringar som gjorts i källdatabasen till den enkla databas som återställs. På så sätt kan du snabbt växla programmet från käll- till måldatabas med minimal avbrottstid.

## <a name="hands-on-learning-modules"></a>Praktiska inlärnings moduler

Följande Microsoft Learn moduler hjälper dig att lära dig kostnads fritt om Azure SQL Database.

- [Etablera en Azure SQL-databas för att lagra program data](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Utveckla och konfigurera ett ASP.NET-program som frågar en Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Skydda din Azure SQL Database](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Nästa steg

- [Här finns en lista på hög nivå över funktioner som stöds i Azure SQL Database](sql-database-features.md).
- Lär dig hur du [gör databasen säkrare](sql-database-security-tutorial.md).
- Det finns mer avancerade anvisningar i [så använder du en enkel databas i Azure SQL Database](sql-database-howto-single-database.md).
- Det finns fler exempelskript skrivna i [PowerShell](sql-database-powershell-samples.md) och [Azure CLI](sql-database-cli-samples.md).
- Läs mer om det [API för hantering](sql-database-single-databases-manage.md) som du kan använda för att konfigurera databaserna.
- [Identifiera den högra Azure SQL Database/hanterade instans-SKU: n för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).
