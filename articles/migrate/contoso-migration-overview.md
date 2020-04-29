---
title: Contoso-migrering | Microsoft Docs
description: Länkar till exempel scenarier för Contoso-exempel för migrering till Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676318"
---
# <a name="contoso-migration-series"></a>Contoso-migreringsserie


Vi har en serie artiklar som visar hur den fiktiva organisationen contoso migrerar den lokala infrastrukturen till [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) molnet. 

Serien innehåller scenarier som illustrerar hur du konfigurerar en infrastruktur-migrering och hur du kör olika typer av migreringar. Scenarierna ökar komplexiteten när de fortskrider. Artiklarna visar hur contoso Company hanterar migreringen, men allmänna anvisningar och pekare finns i hela.

## <a name="migration-articles"></a>Artiklar om migrering

Artiklarna i serien sammanfattas i tabellen nedan.  

- Varje migreringsjobb styrs av något olika affärs behov, som fastställer migrations strategin.
- För varje distributions scenario tillhandahåller vi information om affärs drivande faktorer och mål, en föreslagen arkitektur, steg för att utföra migreringen och rekommendationer för rensning och nästa steg när migreringen är klar.


**Artiklar** | **Information** 
--- | --- 
[Artikel 1: översikt](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Översikt över artikel serien, Contosos migrations strategi och de exempel appar som används i serien. 
[Artikel 2: Distribuera Azure-infrastruktur](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla artiklar i serien. 
[Artikel 3: utvärdera lokala resurser för migrering till Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso kör en utvärdering av den lokala SmartHotel360-appen som körs på VMware. Contoso utvärderar virtuella appar i appar med hjälp av Azure Migrate tjänsten och appen SQL Server databasen med hjälp av Data Migration Assistant.
[Artikel 4: bli värd för en app på en virtuell Azure-dator och SQL Database Hanterad instans](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso kör en hiss-och-Shift-migrering till Azure för den lokala SmartHotel360-appen. Contoso migrerar app-frontend-VM med [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview). Contoso migrerar app-databasen till en Azure SQL Database Hanterad instans med hjälp av [Azure Database migration service](https://docs.microsoft.com/azure/dms/dms-overview).
[Artikel 5: bli värd för en app på virtuella Azure-datorer](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migrerar virtuella SmartHotel360-appar till virtuella Azure-datorer med hjälp av tjänsten Azure Migrate. 
[Artikel 6: bli värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupper](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso migrerar SmartHotel360-appen. Contoso använder Azure Migrate för att migrera virtuella app-appar. Den använder Database Migration Service för att migrera app-databasen till ett SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgruppen. 
[Artikel 7: Rehost a Linux-appen på virtuella Azure-datorer](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso slutför en hiss-och-Shift-migrering av sin Linux osTicket-app till virtuella Azure-datorer med hjälp av Azure Migrate.
[Artikel 8: vara värd för en Linux-app på virtuella Azure-datorer och Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migrerar Linux osTicket-appen till virtuella Azure-datorer med hjälp av Azure Migrate. Den migrerar app-databasen till Azure Database för MySQ med hjälp av Azure Database Migration Service (innehåller ett alternativt alternativ med MySQL Workbench).
[Artikel 9: omstrukturera en app i en Azure-webbapp och Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migrerar sin SmartHotel360-app till en Azure-webbapp och migrerar app-databasen till en Azure SQL Server-instans med hjälp av Azure Database Migration Service.
[Artikel 10: omstrukturera en Windows-app med Azure App Services och SQL-hanterad instans](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso migrerar en lokal Windows-baserad app till en Azure-webbapp och migrerar app-databasen till en hanterad Azure SQL-instans med hjälp av Azure Database Migration Service.
[Artikel 11: omstrukturerar en Linux-app i en Azure-webbapp och Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migrerar Linux osTicket-appen till en Azure-webbapp i flera Azure-regioner med hjälp av Azure Traffic Manager, integrerat med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. 
[Artikel 12: refakultet Team Foundation Server på Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migrerar sin lokala Team Foundation Server-distribution till Azure DevOps Services i Azure.
[Artikel 13: återskapa en app i Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso återkonstruerar sin SmartHotel-app med hjälp av ett utbud av Azure-funktioner och-tjänster, inklusive Azure App Service, Azure Kubernetes service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB.
[Artikel 14: skala en migrering till Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | När du har försökt att migrera kombinationer förbereder contoso för att skala till en fullständig migrering till Azure.



## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migrering av molnet.
- Lär dig mer om migrerings strategier för andra scenarier (käll-/mål par) i [guiden för databas migrering](https://datamigration.microsoft.com/).
