---
title: Contoso migration serie | Microsoft-dokument
description: Ger en översikt över migreringsstrategin och scenarier som används av Contoso för att migrera sina lokala datacenter till Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78939193"
---
# <a name="contoso-migration-series"></a>Contoso-migreringsserie


Vi har en serie artiklar som visar hur den fiktiva organisationen Contoso migrerar lokal infrastruktur till [Microsoft Azure-molnet.](https://azure.microsoft.com/overview/what-is-azure/) 

Serien innehåller information och scenarier som illustrerar hur du ställer in en migrering av infrastruktur och kör olika typer av migreringar. Scenarier växer i komplexitet när de utvecklas. Artiklarna visar hur Contoso-företaget slutför sitt migreringsuppdrag, men tips för allmän läsning och specifika instruktioner finns i hela.

## <a name="migration-articles"></a>Flyttningsartiklar

Artiklarna i serien sammanfattas i tabellen nedan.  

- Varje migreringsscenario styrs av något olika affärsmål som avgör migreringsstrategin.
- För varje distributionsscenario tillhandahåller vi information om affärsdrivande faktorer och mål, en föreslagen arkitektur, steg för att utföra migreringen, rekommendationer för rensning samt nästa steg efter att migreringen är klar.

**Artikel** | **Detaljer** 
--- | --- 
[Artikel 1: Översikt](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Översikt över artikelserien, Contosos migreringsstrategi och exempelapparna som används i serien. 
[Artikel 2: Distribuera Azure-infrastruktur](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso förbereder sin lokala infrastruktur och sin Azure-infrastruktur för migrering. Samma infrastruktur används för alla migreringsartiklar i serien. 
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso gör en utvärdering av sin lokala SmartHotel360-app som körs på VMware. Contoso utvärderar virtuella app-datorer med hjälp av Azure Migrate-tjänsten och app SQL Server-databasen med hjälp av DataMigreringsassistenten.
[Artikel 4: Rehost en app på en Azure VM och SQL Database Managed Instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso kör en lift-and-shift-migrering till Azure för sin lokala SmartHotel360-app. Contoso migrerar appens klientdängd dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar appdatabasen till en Hanterad Azure SQL-databas-instans med hjälp av [Migreringstjänsten för Azure Database](https://docs.microsoft.com/azure/dms/dms-overview).
[Artikel 5: Rehost en app på Virtuella Azure-datorer](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migrerar sina virtuella SmartHotel360-app-datorer till virtuella Azure-datorer med hjälp av tjänsten Site Recovery. 
[Artikel 6: Rehost en app på Virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migrerar SmartHotel360-appen. Contoso använder Site Recovery för att migrera virtuella app-datorer. Den använder databasemigreringstjänsten för att migrera appdatabasen till ett SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. 
[Artikel 7: Rehost en Linux-app på Virtuella Azure-datorer](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso slutför en lift-and-shift-migrering av sin Linux-osTicket-app till virtuella Azure-datorer med hjälp av tjänsten Site Recovery.
[Artikel 8: Rehost en Linux-app på Virtuella Azure-datorer och Azure Database för MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migrerar sin Linux-osTicket-app till virtuella Azure-datorer med hjälp av Site Recovery. Den migrerar appdatabasen till Azure Database for MySQL med hjälp av MySQL Workbench. 
[Artikel 9: Återfactor en app i en Azure-webbapp och Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migrerar sin SmartHotel360-app till en Azure-webbapp och migrerar appdatabasen till en Azure SQL Server-instans med databasemigreringsassistenten.     
[Artikel 10: Refactor a Linux-app i en Azure-webbapp och Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migrerar sin Linux-osTicket-app till en Azure-webbapp i flera Azure-regioner med Azure Traffic Manager, integrerat med GitHub för kontinuerlig leverans. Contoso migrerar appdatabasen till en Azure-databas för MySQL-instans. 
[Artikel 11: Refactor Team Foundation Server på Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migrerar sin lokala Team Foundation Server-distribution till Azure DevOps Services i Azure.
[Artikel 12: Skapa en app i Azure-behållare och Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migrerar sin SmartHotel-app till Azure. Sedan återupptäcker den appwebbnivån som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database.
[Artikel 13: Återskapa en app i Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso återskapar sin SmartHotel-app med hjälp av en rad Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB.
[Artikel 14: Skala en migrering till Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Efter att ha provat migreringskombinationer förbereder Contoso att skala till en fullständig migrering till Azure.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) molnmigrering.
- Lär dig mer om migreringsstrategier för andra scenarier (käll-/målpar) i [guiden För databasmigrering](https://datamigration.microsoft.com/).
