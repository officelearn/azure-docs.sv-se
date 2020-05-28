---
title: Innehålls referens för snabb start för enkel databas
description: Hitta en innehålls referens för alla snabb starter som hjälper dig att snabbt komma igång med enkla databaser i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: c4a699a6b531feee98f9c6f83ba19cfef9d27620
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054607"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Komma igång med enkla databaser i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[En enkel databas](../index.yml) är en fullständigt hanterad PaaS-databas som en tjänst (DbaaS) som är en perfekt lagringsmotor för moderna molnskapade program. I det här avsnittet får du lära dig hur du snabbt konfigurerar och skapar en enda databas i Azure SQL Database.

## <a name="quickstart-overview"></a>Översikt över Snabbstart

I det här avsnittet visas en översikt av tillgängliga artiklar som hjälper dig att snabbt komma igång med enkla databaser. Med följande snabb Starter kan du snabbt skapa en enda databas, konfigurera en brand Väggs regel på server nivå och sedan importera en databas till den nya enkla databasen med hjälp av en `.bacpac` fil:

- [Skapa en enskild databas med hjälp av Azure Portal](single-database-create-quickstart.md).
- När databasen har skapats behöver du [skydda databasen genom att konfigurera brandväggsregler](firewall-create-server-level-portal-quickstart.md).
- Om du har en befintlig databas på SQL Server som du vill migrera till Azure SQL Database bör du installera [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) som analyserar dina databaser på SQL Server och hittar eventuella problem som kan blockera migreringen. Om du inte hittar några problem kan du exportera din databas som en `.bacpac`-fil och [importera den med hjälp av Azure-portalen eller SqlPackage](database-import.md).


## <a name="automating-management-operations"></a>Automatisera hanteringsåtgärder

Du kan använda PowerShell eller Azure CLI för att skapa, konfigurera och skala databasen.

- [Skapa och konfigurera en enskild databas med PowerShell](scripts/create-and-configure-database-powershell.md) eller [Azure CLI](scripts/create-and-configure-database-cli.md)
- [Uppdatera en enskild databas och skala resurser med PowerShell](scripts/monitor-and-scale-database-powershell.md) eller [Azure CLI](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrera till en enkel databas med minimal avbrottstid

Med de här snabbstarterna kan du snabbt skapa eller importera databasen till Azure med hjälp av en `.bacpac`-fil. Men `.bacpac` `.dacpac` filer är utformade för att snabbt flytta databaser över olika versioner av SQL Server och inom Azure SQL, eller för att implementera kontinuerlig integrering i din DevOps-pipeline. Den här metoden är dock inte avsedd för migrering av produktionsdatabaser med minimal avbrottstid, eftersom du skulle behöva sluta lägga till nya data, vänta tills exporten av källdatabasen till en `.bacpac`-fil är klar och sedan vänta tills importen till Azure SQL Database är klar. All denna väntan resulterar i avbrottstid för ditt program, särskilt för stora databaser. För att flytta produktionsdatabasen behöver du ett bättre sätt att migrera som garanterar minimal avbrottstid för migrering. För detta använder du [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) för att migrera din databas med minimal avbrottstid. DMS åstadkommer detta genom att inkrementellt överföra ändringar som gjorts i källdatabasen till den enkla databas som återställs. På så sätt kan du snabbt växla programmet från käll- till måldatabas med minimal avbrottstid.

## <a name="hands-on-learning-modules"></a>Praktiska inlärnings moduler

Följande Microsoft Learn moduler hjälper dig att lära dig kostnads fritt om Azure SQL Database.

- [Etablera en databas i SQL Database att lagra program data](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Utveckla och konfigurera ett ASP.NET-program som frågar en Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Skydda din Azure SQL-databas](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Nästa steg

- [Här finns en lista på hög nivå över funktioner som stöds i Azure SQL Database](features-comparison.md).
- Lär dig hur du [gör databasen säkrare](secure-database-tutorial.md).
- Det finns mer avancerade anvisningar i [så använder du en enkel databas i Azure SQL Database](how-to-content-reference-guide.md).
- Det finns fler exempelskript skrivna i [PowerShell](powershell-script-content-guide.md) och [Azure CLI](az-cli-script-samples-content-guide.md).
- Läs mer om [hanterings-API: et](single-database-manage.md) som du kan använda för att konfigurera dina databaser.
- [Identifiera rätt Azure SQL Database eller SQL-hanterad instans-SKU för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).
