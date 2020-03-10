---
title: Contoso-migrering | Microsoft Docs
description: Innehåller en översikt över de strategier och scenarier för migrering som används av contoso för att migrera sitt lokala data Center till Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78939193"
---
# <a name="contoso-migration-series"></a>Contoso-migreringsserie


Vi har en serie artiklar som visar hur den fiktiva organisationen contoso migrerar en lokal infrastruktur till [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) molnet. 

Serien innehåller information och scenarier som illustrerar hur du konfigurerar en migrering av infrastrukturen och hur du kör olika typer av migreringar. Scenarierna ökar komplexiteten när de fortskrider. Artiklarna visar hur contoso-företaget har slutfört migreringen, men pekarna för allmän läsning och detaljerade instruktioner finns i hela.

## <a name="migration-articles"></a>Artiklar om migrering

Artiklarna i serien sammanfattas i tabellen nedan.  

- Varje migreringsscenario styrs av något olika affärsmål som avgör migreringsstrategin.
- För varje distributionsscenario tillhandahåller vi information om affärsdrivande faktorer och mål, en föreslagen arkitektur, steg för att utföra migreringen, rekommendationer för rensning samt nästa steg efter att migreringen är klar.

**Artikel** | **Detaljer** 
--- | --- 
[Artikel 1: översikt](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Översikt över artikel serien, Contosos migrations strategi och de exempel appar som används i serien. 
[Artikel 2: Distribuera Azure-infrastruktur](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso förbereder den lokala infrastrukturen och dess Azure-infrastruktur för migrering. Samma infrastruktur används för alla flyttnings artiklar i serien. 
[Artikel 3: utvärdera lokala resurser för migrering till Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso kör en utvärdering av den lokala SmartHotel360-appen som körs på VMware. Contoso utvärderar virtuella appar i appar med hjälp av Azure Migrate tjänsten och appen SQL Server databasen med hjälp av Data Migration Assistant.
[Artikel 4: bli värd för en app på en virtuell Azure-dator och SQL Database Hanterad instans](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso kör en hiss-och-Shift-migrering till Azure för den lokala SmartHotel360-appen. Contoso migrerar app-frontend-VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Hanterad instans med hjälp av [Azure Database migration service](https://docs.microsoft.com/azure/dms/dms-overview).
[Artikel 5: bli värd för en app på virtuella Azure-datorer](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migrerar virtuella SmartHotel360-appar till virtuella Azure-datorer med hjälp av tjänsten Site Recovery. 
[Artikel 6: bli värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupper](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migrerar SmartHotel360-appen. Contoso använder Site Recovery för att migrera virtuella app-appar. Den använder Database Migration Service för att migrera app-databasen till ett SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgruppen. 
[Artikel 7: Rehost a Linux-appen på virtuella Azure-datorer](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso slutför en ökning och Skift-migrering av sin Linux osTicket-app till virtuella Azure-datorer med hjälp av tjänsten Site Recovery.
[Artikel 8: vara värd för en Linux-app på virtuella Azure-datorer och Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migrerar Linux osTicket-appen till virtuella Azure-datorer med hjälp av Site Recovery. Den migrerar appdatabasen till Azure Database for MySQL med hjälp av MySQL Workbench. 
[Artikel 9: omstrukturera en app i en Azure-webbapp och Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migrerar sin SmartHotel360-app till en Azure-webbapp och migrerar app-databasen till en Azure SQL Server-instans med databasen Migration Assistant.     
[Artikel 10: omstrukturerar en Linux-app i en Azure-webbapp och Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migrerar Linux osTicket-appen till en Azure-webbapp på flera Azure-regioner med hjälp av Azure Traffic Manager, integrerat med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. 
[Artikel 11: refakultet Team Foundation Server på Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migrerar sin lokala Team Foundation Server-distribution till Azure DevOps Services i Azure.
[Artikel 12: skapa en ny app i Azure-behållare och Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migrerar sin SmartHotel-app till Azure. Sedan återskapar den appens webb nivå som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database.
[Artikel 13: återskapa en app i Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso återkonstruerar sin SmartHotel-app med hjälp av ett utbud av Azure-funktioner och-tjänster, inklusive Azure App Service, Azure Kubernetes service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB.
[Artikel 14: skala en migrering till Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | När du har försökt att migrera kombinationer förbereder contoso för att skala till en fullständig migrering till Azure.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migrering av molnet.
- Lär dig mer om migrerings strategier för andra scenarier (käll-/mål par) i [guiden för databas migrering](https://datamigration.microsoft.com/).
