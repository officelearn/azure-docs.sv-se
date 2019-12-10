---
title: Om Azure Migrate
description: Lär dig mer om Server utvärdering och migrering med tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 54aea9fac946713ee6c1709e952e3962bb7288e9
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951776"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Den här artikeln ger en snabb översikt över Azure Migrate.

Azure Migrate hjälper dig att migrera ditt företag från en lokal plats till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure.  Azure Migrate tillhandahåller:

- **Plattform för enhetlig migrering**: en enda portal för att starta, köra och spåra migrerings resan till Azure.
- **Verktygs mängd**: hubben ger Azure Migrate verktyg bedömning och migrering, och integreras med andra Azure-tjänster, samt andra verktyg och ISV-erbjudanden (oberoende program varu leverantörer).
- **Arbets belastningar**: Azure Migrate tillhandahåller utvärdering och migrering för:
    - **Servrar**: Använd Azure Migrate Server utvärdering, Azure Migrate Server-migrering och andra verktyg för utvärdering och migrering av servrar till virtuella Azure-datorer.
    - **Databaser**: utnyttja Microsoft-och ISV-verktyg för utvärdering och migrering av lokala databaser till Azure SQL DB eller Azure SQL-hanterad instans.
    - **Webb program**: Använd Azure App Service-assistenten för att utvärdera och migrera lokala webb program till Azure App Service.
    - **Virtuella skriv bord**: Använd ISV-verktyg för att utvärdera och migrera lokala virtuella Skriv bords infrastrukturer (VDI) till virtuella Windows-datorer i Azure.
    - **Data**: Använd Azure Data Box serie produkter för att snabbt och kostnads effektivt migrera stora mängder data till Azure.

## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns för närvarande två versioner av tjänsten Azure Migrate:

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: om du använde den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöds) bör du nu använda den aktuella versionen. Du kan inte längre skapa Azure Migrate projekt med hjälp av den tidigare versionen och vi rekommenderar att du inte gör nya identifieringar. Om du vill komma åt befintliga projekt går du till Azure Portal och söker efter och väljer **Azure Migrate**. På **Azure Migrate** -instrumentpanelen finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.



## <a name="isv-integration"></a>ISV-integrering

Förutom interna Azure-verktyg kan Azure Migrate integreras med ett antal ISV-erbjudanden. 

**ISV** | **Funktion**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrera servrar
[Cloudamize](https://www.cloudamize.com/platform) | Utvärdera servrar
[Samhyres teknik](https://www.corenttech.com/AzureMigrate/) | Utvärdera och migrera servrar
[Enhet 42](https://docs.device42.com/) | Utvärdera servrar
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Utvärdera VDI
[Rack](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrera servrar
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Utvärdera servrar
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Utvärdera servrar och databaser

## <a name="azure-tool-integration"></a>Integrering av Azure-verktyg

Tabellen sammanfattar andra verktyg som är integrerade i Azure Migrate.

**Verktyg** | **Detaljer**
--- | ---
Azure Migrate: Server utvärdering | Utvärdera servrar
Azure Migrate: Server-migrering | Migrera servrar
Databas Migration Assistant (DMA) | Utvärdera databaser
Database Migration Service (DMS) | Migrera databaser
Movere | Utvärdera servrar
Webbapp Migration Assistant | Utvärdera och migrera webb program



### <a name="selecting-a-tool"></a>Välja ett verktyg

Identifiera det verktyg som du behöver och Lägg till det i ett Azure Migrate-projekt.

- Om du lägger till ett ISV-verktyg eller en driv kraft:
    - Kom igång genom att skaffa en licens eller registrera dig för en kostnads fri utvärderings version, i enlighet med verktygs principen. Licensiering för verktyg är i enlighet med ISV-eller-verktygets licensierings modell.
    - I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Följ anvisningarna och dokumentationen för verktyget för att ansluta verktyget med Azure Migrate.
- Du kan centralt spåra din migrerings resa från Azure Migrate-projektet, via Azure och andra verktyg.



## <a name="azure-migrate-server-assessment-tool"></a>Verktyget för Azure Migrate Server bedömning

Azure Migrate: verktyget för Server bedömning identifierar och utvärderar lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar för migrering till Azure. Det hjälper dig att identifiera följande:

- **Azure-beredskap:** Utvärdera om lokala datorer är klara för migrering till Azure.
- **Azure-storlek:** Den uppskattade storleken på virtuella Azure-datorer efter migrering.
- **Kostnads uppskattning i Azure:** Beräknade kostnader för att köra lokala servrar i Azure.
- **Beroende visualisering:** Beroenden mellan servrar (om beroende visualisering är aktive rad) och bästa sätt att flytta beroende servrar till Azure.

Server utvärderingen använder en förenklad installation som du distribuerar lokalt och registrerar dig för Server utvärdering.

- Enheten identifierar lokala datorer.
- Den ansluter till Server utvärdering och skickar kontinuerligt metadata för datorer och prestanda data till Azure Migrate.
- Identifiering av enheter är agenten. Inget måste installeras på identifierade datorer.
- Efter identifieringen samlar du in identifierade datorer i grupper. Du samlar vanligt vis samman datorer som du vill migrera tillsammans.
- Du skapar en utvärdering för en grupp. Sedan kan du analysera utvärderingen för att ta reda på din strategi för migrering.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate-servermigreringsverktyg

Azure Migrate: verktyget Migreringsverktyg hjälper dig att migrera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, andra virtualiserade datorer och virtuella datorer i offentliga moln till Azure. Du kan migrera datorer efter att ha bedömt dem, eller utan en utvärdering.


## <a name="database-migration-assistant"></a>Databas Migration Assistant

Azure Migrate integreras med Microsoft Data Migration Assistant (DMA) för att utvärdera lokala SQL Server-databaser för migrering till Azure SQL DB, Azure SQL-hanterad instans eller virtuella Azure-datorer som kör SQL Server. DMA tillhandahåller information om möjliga spärrnings problem för migrering. Den identifierar funktioner som inte stöds, samt nya funktioner som du kan dra nytta av efter migreringen, och hjälper dig att identifiera rätt sökväg för migrering av databasen. [Läs mer](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration-service"></a>Database Migration Service

Azure Migrate integreras med Azure Database Migration Service (DMS) för att migrera lokala databaser till Azure. Använd DMS för att migrera lokala databaser till virtuella Azure-datorer som kör SQL, Azure SQL DB och Azure SQL Managed instances. [Läs mer](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="movere"></a>Movere

 
Superkraft är en SaaS-plattform som ökar Business Intelligence genom att noggrant presentera hela IT-miljöer inom en enda dag. När organisationer växer, förändras och digitalt optimeras, ger lösningen företag den säkerhet de behöver för att få insyn och kontroll över sina miljöer oavsett plattform, program eller geografi. Den här förflyttningen [förvärvades](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) av Microsoft och säljs inte längre som ett fristående erbjudande.  Kraftning är tillgängligt via Microsofts program för lösnings bedömning och molnet. [Läs mer](https://www.movere.io) om superkraft. Om du har frågor kan du skicka dem till: movereq@microsoft.com eller kontakta din Microsoft-representant.

Vi rekommenderar att du även tittar på Azure Migrate, vår inbyggda migration service. Azure Migrate tillhandahåller en central hubb för att förenkla migreringen till molnet. Hubben innehåller omfattande stöd för olika arbets belastningar, inklusive fysiska och virtuella servrar, databaser och program. Med en synlighet från slut punkt till slut punkt blir det enkelt att spåra framsteg i hela identifiering, utvärdering och migrering. Med både Azure-och partners inbyggda ISV-verktyg har Azure Migrate också en mängd olika funktioner, inklusive virtuell och fysisk server identifiering, prestanda-baserad höger storlek, kostnads planering, import-baserade utvärderingar och program utan agent beroende analys. Om du vill ha expert hjälp för att komma igång har Microsoft kvalificerad [Azure expert Managed Service Provider](https://azure.microsoft.com/partners) som vägleder dig längs resan. Kolla in [Azure Migrate webbplats](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Webbapp Migration Assistant

Azure Migrate integreras med Azure App Service-Migration Assistant. Från Azure Migrate Hub kan du utvärdera och migrera lokala webbappar till Azure med hjälp av-assistenten enligt följande:

- **Utvärdera Web Apps online**: använd Azure App Service Migration Assistant för att utvärdera lokala webbplatser för migrering till Azure App Service.
- **Migrera webbappar**: migrera .net-och php-webbappar till Azure med hjälp av Azure App Service Migration Assistant.

[Lär dig mer](https://appmigration.microsoft.com/) om assistenten.



## <a name="offline-data-migration"></a>Datamigrering offline

Du kan använda Azure Data Box produkter för att flytta stora mängder data offline till Azure. [Läs mer](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Nästa steg

- Prova våra självstudier för att utvärdera virtuella [VMware-datorer](tutorial-assess-vmware.md) och [virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md).
- [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
