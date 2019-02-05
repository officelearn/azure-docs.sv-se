---
title: Snabbstart – enkla databaser i Azure SQL Database | Microsoft Docs
description: Lär dig att snabbt komma igång med enkla databaser i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464824"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Komma igång med enkla databaser i Azure SQL Database

[En enkel databas](sql-database-single-index.yml) i Azure SQL Database är en fullständigt hanterad PaaS-databas som en tjänst (DbaaS) som är en perfekt lagringsmotor för moderna molnskapade program. I det här avsnittet lär du dig att snabbt konfigurera och skapa SQL Database.

## <a name="quickstart-overview"></a>Snabbstart, översikt

I det här avsnittet visas en översikt av tillgängliga artiklar som hjälper dig att snabbt komma igång med enkla databaser. Det enklaste sättet att skapa din första SQL Database är att använda [Azure-portalen](sql-database-get-started-portal.md), där du kan konfigurera nödvändiga parametrar.
Efter skapandet behöver du [skydda databasen genom att konfigurera brandväggsregler](sql-database-get-started-portal-firewall.md). 

Om du har en befintlig databas på SQL Server som du vill migrera till Azure bör du installera [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), som analyserar databaserna på SQL Server och hittar eventuella problem som blockerar migrering till enkel databas. Om du inte hittar några problem kan du exportera din databas som en `.bacpac`-fil och [importera den med hjälp av Azure-portalen eller SqlPackage](sql-database-import.md).

Med de här snabbstarterna kan du snabbt konfigurera, skapa och importera databaserna i Azure-molnet.

## <a name="automating-management-operations"></a>Automatisera hanteringsåtgärder

Med Azure-portalen kan du enkelt skapa och ändra den enkla databasen. Alternativt kan du använda [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) eller [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md) för att skapa databaser.
Du kan även uppdatera din enkla databas och skala resurser med hjälp av [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) eller [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md).

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrera till en enkel databas med minimal avbrottstid

Med artiklarna i de här snabbstarterna kan du snabbt skapa eller importera databasen till Azure med hjälp av `.bacpac`. Dock är `.bacpac`- och `.dacpack`-filer utformade för att snabbt flytta databaser mellan olika versioner av SQL Server och Azure SQL Database (enkel databas, elastisk pool eller hanterad instans) eller för att implementera kontinuerlig integrering i din DevOps-pipeline. Den här metoden är dock inte avsedd för migrering av produktionsdatabaserna med minimal avbrottstid eftersom du skulle behöva vänta med att exportera källdatabasen som en `.bacpac`-fil och importera den till Azure SQL Database, vilket skulle leda till avbrottstid för programmet, särskilt om databasen är förhållandevis stor. Om du flyttar produktionsdatabasen behöver du förmodligen ett bättre sätt att migrera som garanterar minimal avbrottstid för migrering. [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) är en tjänst som kan migrera din databas med minimal avbrottstid. På så sätt kan du snabbt växla programmet från käll- till måldatabas med minimal avbrottstid.

## <a name="next-steps"></a>Nästa steg

* [Här finns en lista på hög nivå över funktioner som stöds i Azure SQL Database](sql-database-features.md). 
* Lär dig hur du [gör databasen säkrare](sql-database-security-tutorial.md). 
* Mer avancerade självstudier finns i [instruktionsavsnittet](sql-database-howto-single-database.md). 
* Det finns fler exempelskript skrivna i [PowerShell](sql-database-powershell-samples.md) och [Azure CLI](sql-database-cli-samples.md). 
* Läs mer om det [API för hantering](sql-database-single-databases-manage.md) som du kan använda för att konfigurera databaserna. 
