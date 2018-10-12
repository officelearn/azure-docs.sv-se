---
title: Översikt över Contoso migrering till Azure | Microsoft Docs
description: Innehåller en översikt över migreringsstrategi och scenarier som används av Contoso för att migrera sina lokala datacenter till Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 30be44b91dbfba0c292c55670e920052d2c8ae3d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092872"
---
# <a name="contoso-migration-overview"></a>Contoso-migrering: översikt


Den här artikeln visar hur fiktiva organisationen Contoso migrerar infrastruktur på plats till den [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) molnet. 

Det här dokumentet är först i en serie artiklar som visar hur det fiktiva företaget Contoso migrerar till Azure. Serien innehåller information och scenarier som illustrerar hur du konfigurerar en migrering av infrastruktur och kör olika typer av migreringar. Scenarier växer i komplexitet och vi lägger till ytterligare artiklar med tiden. Artiklarna visar hur företaget Contoso har slutförts uppdraget migreringen, men pekare för allmän läsning och specifika anvisningar finns i.

## <a name="introduction"></a>Introduktion

Azure ger åtkomst till en omfattande uppsättning molntjänster. Du kan använda dessa tjänster som utvecklare och IT-proffs, för att bygga, distribuera och hantera program på en rad olika verktyg och ramverk, genom ett globalt nätverk av datacenter. När ditt företag möter utmaningarna med digitaliseringen hjälper Azure-molnet dig att optimera resurser och åtgärder, interagera med dina kunder och anställda och transformera dina produkter.

Men även om det finns stora fördelar med att migrera till Azure och molnet i form av hastighet och flexibilitet, minskade kostnader, prestanda och tillförlitlighet, kan det för många organisationer ibland vara nödvändigt att köra lokala datacenter ibland. För att lösa problem med barriärer vid molnimplementering tillhandahåller Azure en hybridmolnstrategi, som bygger broar mellan dina lokala datacenter och det offentliga Azure-molnet. Till exempel med hjälp av Azure-molnresurser som Azure Backup för att skydda lokala resurser eller använda Azure analytics för att få insikter om lokala arbetsbelastningar. 

Som en del av hybridmolnstrategi tillhandahåller Azure allt fler lösningar för att migrera lokala appar och arbetsbelastningar till molnet. Med de här enkla stegen kan du noggrant bedöma dina lokala resurser för att ta reda på hur de skulle fungera i Azure-molnet. Sedan kan du på ett säkert sätt migrera dina resurser till Azure. När resurserna är igång och körs i Azure, kan du optimera dem för att behålla och förbättra åtkomsten, flexibiliteten, säkerheten och tillförlitligheten.

## <a name="migration-strategies"></a>Migreringsstrategier

Strategier för migrering till molnet faller inom fyra olika kategorier: rehost, omstrukturera, Omforma eller återskapa. Strategin du antar beror på din affärsdrivande faktorer och mål för migrering. Du kan använda flera strategier. Du kan till exempel välja att ange ny värd (lift and shift) enkelt appar eller appar som inte är viktiga för din verksamhet, men Omforma som är mer komplexa och affärskritisk. Låt oss titta på strategierna.


**Strategi** | **Definition** | **När du ska använda** 
--- | --- | --- 
**Ange ny värd** | Kallas ofta en ”lift and shift”-migrering. Det här alternativet kräver inte ändringar i koden och vi du migrera dina befintliga appar till Azure snabbt. Varje app migreras som är att dra nytta av molnet, utan risk och kostnad som hör till ändringar i koden. | När du behöver snabbt flytta appar till molnet.<br/><br/> När du vill flytta en app utan att ändra den.<br/><br/> När dina appar har byggts så att de kan utnyttja [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) skalbarhet efter migreringen.<br/><br/> När appar är viktiga för din verksamhet, men du behöver inte omedelbart ändringar av funktionerna i appen.
**Omstrukturera** | Kallas ofta ”ompaketering”, omstrukturering kräver minimala ändringar till appar, så att de kan ansluta till [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/), och använda molntjänster.<br/><br/> Exempelvis kan migrera du befintliga appar till Azure App Service eller Azure Kubernetes Service (AKS).<br/><br/> Eller så kan du omstrukturerar relationella och icke-relationsdatabaser till alternativ som Azure SQL Database Managed Instance, Azure Database for MySQL, Azure Database för PostgreSQL och Azure Cosmos DB. | Om din app kan enkelt paketerats om för att fungera i Azure.<br/><br/> Om du vill använda innovativa DevOps-metoder som tillhandahålls av Azure, eller du funderar DevOps och använder en behållarstrategi för arbetsbelastningar.<br/><br/> Du måste tänka portabilitet för dina befintliga kodbas och tillgängliga utvecklingskompetens för omstrukturering.
**Omforma** | Ny arkitektur för migrering fokuserar på Ändra och utöka funktionalitet och kodbas att optimera app-arkitektur för skalbarhet i molnet.<br/><br/> Du kan till exempel dela upp en monolitisk App i en grupp av mikrotjänster och som fungerar tillsammans och skala enkelt.<br/><br/> Eller så kan du omforma relationsdata och icke-relationella databaser till en helt hanterad DBaaS lösningar, till exempel Azure SQL Database Managed Instance, Azure Database for MySQL, Azure Database för PostgreSQL och Azure Cosmos DB. | När dina appar behöver större revideringar för att lägga till nya funktioner eller ska fungera effektivt på en molnplattform.<br/><br/> När du vill använda befintliga investeringar i programmet uppfylla skalbarhetskraven, tillämpa innovativa Azure DevOps-metoder och minska användningen av virtuella datorer.
**Återskapa** | Återskapa tar saker a steg ytterligare genom att bygga en app från grunden med hjälp av Azure-molntekniker.<br/><br/> Du kan till exempel skapa grönt fält appar med molnbaserade teknik som Azure Functions, Azure AI, Azure SQL Database Managed Instance och Azure Cosmos DB. | När du vill att snabb utveckling och befintliga appar har begränsad funktionalitet och livslängd.<br/><br/> När du är redo att påskynda företagets innovationer (inklusive DevOps-metoder som tillhandahålls av Azure) kan skapa nya program med molnbaserade teknik och dra nytta av förbättringarna av AI, Blockkedjor och IoT.

## <a name="migration-articles"></a>Artiklar om datamigrering

Artiklarna i serien sammanfattas i tabellen nedan.  

- Varje Migreringsscenario drivs av något annorlunda affärsmål som fastställa migreringsstrategi för.
- Vi tillhandahåller information om affärsdrivande faktorer och mål, en föreslagna arkitektur, steg för att utföra migreringen och rekommendation för rensning och nästa steg när migreringen är klar för respektive distributionsscenario.

**Artikel** | **Detaljer** | **Status**
--- | --- | ---
Artikel 1: översikt | Översikt över artikelserien, Contosos migreringsstrategi och exempelappar som används i serien. | Den här artikeln
[Artikel 2: Distribuera Azure-infrastrukturen](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md)  | Contoso kör en utvärdering av dess lokal SmartHotel360-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av Azure Migrate-tjänsten och app-SQL Server-databasen med hjälp av Data Migration Assistant. | Tillgängligt
[Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel360-app. Contoso migrerar app frontend virtuell dator med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Tillgängligt   
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel360 app virtuella datorer till virtuella Azure-datorer med hjälp av Site Recovery-tjänsten. | Tillgängliga [artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel360 app virtuella datorer till Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel360-app. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängliga [artikel 7: Appvärd Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso Slutför en lift and shift-migrering av dess osTicket app i Linux på Azure virtuella datorer med Site Recovery-tjänsten. | Tillgängligt
[Artikel 8: Byta Appvärd en Linux på Azure virtuella datorer och Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar dess osTicket Linux-app till virtuella Azure-datorer med hjälp av Site Recovery. Den migrerar app-databas till Azure Database för MySQL med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app i en Azure webbapp och Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar dess SmartHotel360-app till en Azure webbapp och migrerar app-databasen till en Azure SQL Server-instans med Database Migration Assistant. | Tillgängligt    
[Artikel 10: Omstrukturera en Linux-app i en Azure-webbapp och en Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera Azure-regioner med Azure Traffic Manager, integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera Team Foundation Server på Azure DevOps-tjänsterna](contoso-migration-tfs-vsts.md) | Contoso migrerar dess lokal Team Foundation Server-distribution till Azure DevOps-tjänsterna i Azure. | Tillgängligt
[Artikel 12: Omforma en app i Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure. Sedan rearchitects den webbnivån appen som en Windows-behållare som körs i Azure Service Fabric och databasen med Azure SQL Database. | Tillgängligt    
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt 
[Artikel 14: Skala en migrering till Azure](contoso-migration-scale.md) | När du testar migreringen kombinationer, förbereder Contoso att skala upp till en fullständig migrering till Azure. | Tillgängligt

I den här artikeln Contoso ställer in alla infrastrukturelement som den behöver slutföra alla Migreringsscenarier. 







### <a name="demo-apps"></a>Demoappar

I artiklar använder två demoappar - SmartHotel360 och osTicket.

- **SmartHotel360**: den här appen har utvecklats av Microsoft som ett prov-app som du kan använda när du arbetar med Azure. Den tillhandahålls med öppen källkod och du kan hämta den från [GitHub](https://github.com/Microsoft/SmartHotel360). Det är en ASP.NET-app som är anslutna till en SQL Server-databas. Appen är för närvarande på två virtuella VMware-datorer som kör Windows Server 2008 R2 och SQL Server 2008 R2. Appen virtuella datorer är hanteras lokalt och hanteras av vCenter-servern.
- **osTicket**: en öppen källkod helpdesk biljetter app som körs på Linux. Du kan ladda ned det från [GitHub](https://github.com/osTicket/osTicket). Aktuella appen är på två virtuella VMware-datorer som kör Ubuntu 16.04 LTS, med hjälp av Apache 2, PHP 7.0 och MySQL 5.7
    

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](contoso-migration-infrastructure.md) Contoso ställer in en lokal och Azure-infrastrukturen för att förbereda för migrering.



