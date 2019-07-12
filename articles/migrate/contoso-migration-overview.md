---
title: Contoso migrering serien | Microsoft Docs
description: Innehåller en översikt över migreringsstrategi och scenarier som används av Contoso för att migrera sina lokala datacenter till Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: 82585a6f1df369e299bd9df4d57f6106aad4ead7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696208"
---
# <a name="contoso-migration-series"></a>Contoso-migreringsserie


Vi har en serie artiklar som visar hur fiktiva organisationen Contoso migrerar infrastruktur på plats till den [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) molnet. 

Serien innehåller information och scenarier som illustrerar hur du konfigurerar en migrering av infrastruktur och kör olika typer av migreringar. Scenarier växer i komplexitet när den fortsätter. Artiklarna visar hur företaget Contoso har slutförts uppdraget migreringen, men pekare för allmän läsning och specifika anvisningar finns i.

## <a name="migration-articles"></a>Artiklar om datamigrering

Artiklarna i serien sammanfattas i tabellen nedan.  

- Varje Migreringsscenario drivs av något annorlunda affärsmål som fastställa migreringsstrategi för.
- Vi tillhandahåller information om affärsdrivande faktorer och mål, en föreslagna arkitektur, steg för att utföra migreringen och rekommendation för rensning och nästa steg när migreringen är klar för respektive distributionsscenario.

**Artikel** | **Detaljer** 
--- | --- 
[Artikel 1: Översikt över](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. 
[Artikel 2: Distribuera Azure-infrastrukturen](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. 
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso kör en utvärdering av dess lokal SmartHotel360-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant.
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel360-app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migrerar dess SmartHotel360 app virtuella datorer till virtuella Azure-datorer med hjälp av Site Recovery-tjänsten. 
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migrerar SmartHotel360-app. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. 
[Artikel 7: Ange ny värd för en app för Linux på Azure virtuella datorer](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso Slutför en lift and shift-migrering av dess osTicket app i Linux på Azure virtuella datorer med Site Recovery-tjänsten.
[Artikel 8: Ange ny värd för en app för Linux på Azure virtuella datorer och Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migrerar dess osTicket Linux-app till virtuella Azure-datorer med hjälp av Site Recovery. Den migrerar app-databas till Azure Database för MySQL med MySQL Workbench. 
[Artikel 9: Omstrukturera en app i en Azure webbapp och Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migrerar dess SmartHotel360-app till en Azure webbapp och migrerar app-databasen till en Azure SQL Server-instans med Database Migration Assistant.     
[Artikel 10: Omstrukturera en Linux-app i en Azure-webbapp och en Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. 
[Artikel 11: Omstrukturera Team Foundation Server på Azure DevOps-tjänsterna](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migrerar dess lokal Team Foundation Server-distribution till Azure DevOps-tjänsterna i Azure.
[Artikel 12: Omforma en app i Azure-behållare och Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. 
[Artikel 13: Återskapa en app i Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB.  
[Artikel 14: Skala en migrering till Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | När du testar migreringen kombinationer, förbereder Contoso att skala upp till en fullständig migrering till Azure. 


    

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) molnmigrering. 

