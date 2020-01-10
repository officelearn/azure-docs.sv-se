---
title: Om Azure Migrate
description: Läs mer om tjänsten Azure Migrate.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: c336b2f95a17896edcc7eaad2b2586c6859804f9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772201"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Den här artikeln ger en snabb översikt över Azure Migrates tjänsten.

Använd Azure Migrate för att migrera till Azure. Azure Migrate tillhandahåller en central hubb för att utvärdera och migrera lokal infrastruktur, program och data till Azure, med följande funktioner:

- **Plattform för enhetlig migrering**: en enda portal för att starta, köra och spåra migrerings resan till Azure.
- **Olika verktyg**: en uppsättning verktyg för utvärdering och migrering. Hubben omfattar Azure Migrate: Server utvärdering och Azure Migrate: Server-migrering. Den integreras med andra Azure-tjänster och med andra verktyg och oberoende program varu leverantörer (ISV)-erbjudanden.
- **Utvärdering och migrering**: i Azure Migrate hubb kan du utvärdera och migrera:
    - **Servrar**: utvärdera och migrera lokala servrar till virtuella Azure-datorer.
    - **Databaser**: utvärdera och migrera lokala databaser till Azure SQL DB eller till Azure SQL-hanterad instans.
    - **Webb program**: utvärdera och migrera lokala webb program till Azure App Service med hjälp av Azure App Service-assistenten.
    - **Virtuella skriv bord**: utvärdera och migrera din lokala virtuella Skriv bords infrastruktur (VDI) till Windows Virtual Desktop i Azure.
    - **Data**: Migrera stora mängder data till Azure snabbt och kostnads effektivt med Azure Data Box produkter. 


## <a name="integrated-tools"></a>Integrerade verktyg

Azure Migrate Hub tillhandahåller följande verktyg.

**Verktyg** | **Utvärdera/migrera** | **Detaljer**
--- | --- | ---
**Azure Migrate: Server utvärdering** | Utvärdera servrar | Upptäck och utvärdera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar, för att ta reda på om de är klara för migrering till Azure.
**Azure Migrate: Server-migrering** | Migrera servrar | Migrera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar samt andra virtualiserade datorer och virtuella datorer i offentliga moln till Azure. 
**Databas Migration Assistant (DMA)** | Utvärdera lokala SQL Server databaser för migrering till Azure SQL DB, Azure SQL-hanterad instans eller virtuella Azure-datorer som kör SQL Server. | DMA tillhandahåller information om möjliga spärrnings problem för migrering. Den identifierar funktioner som inte stöds, samt nya funktioner som du kan dra nytta av efter migreringen, och hjälper dig att identifiera rätt sökväg för migrering av databasen. [Läs mer](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Database Migration Service (DMS)** | Migrera lokala databaser till virtuella Azure-datorer som kör SQL, Azure SQL DB och Azure SQL Managed instances. | [Läs mer](https://docs.microsoft.com/azure/dms/dms-overview) om DMS.
**Förflyttat** | Utvärdera servrar | [Läs mer](#movere) om superkraft.
**Webbapp Migration Assistant** | Utvärdera och migrera lokala webbappar till Azure. |  Använd Azure App Service Migration Assistant för att utvärdera lokala webbplatser för migrering till Azure App Service.<br/><br/> Migrera .NET-och PHP-webbappar till Azure med hjälp av Azure App Service Migration Assistant. [Lär dig mer](https://appmigration.microsoft.com/) om assistenten.
**Azure Data Box** | Migrering av data offline. | Använd Azure Data Box produkter för att flytta stora mängder data offline till Azure. [Läs mer](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>ISV-integrering

Azure Migrate integreras med ett antal ISV-erbjudanden. 

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


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Server bedömnings verktyg

Verktyget Azure Migrate: Server bedömning identifierar och utvärderar lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar för migrering till Azure. Det hjälper dig att identifiera följande:

- **Azure-beredskap:** Utvärdera om lokala datorer är klara för migrering till Azure.
- **Azure-storlek:** Den uppskattade storleken på virtuella Azure-datorer efter migrering.
- **Kostnads uppskattning i Azure:** Beräknade kostnader för att köra lokala servrar i Azure.
- **Beroende visualisering:** Om du använder Server utvärdering med visualisering av beroenden kan du effektivt identifiera beroenden mellan servrar och det bästa sättet att flytta beroende servrar till Azure.

Server utvärderingen använder en förenklad installation som du distribuerar lokalt och registrerar dig för Server utvärdering.

- Installationen körs på en fysisk server eller virtuell dator och installeras enkelt med en nedladdad mall.
- Enheten identifierar lokala datorer och skickar kontinuerligt metadata för datorer och prestanda data till Azure Migrate.
- Identifiering av enheter är agenten. Inget måste installeras på identifierade datorer.
- Efter identifieringen samlar du in identifierade datorer i grupper och utvärderar grupper för migrering.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Migreringsverktyg för Server

Med verktyget Azure Migrate: Migreringsverktyg kan du migrera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, andra virtualiserade datorer och virtuella datorer i offentliga moln till Azure. Du kan migrera datorer efter att du har bedömt dem, eller migrera dem utan en utvärdering.


## <a name="select-a-tool"></a>Välj ett verktyg

I Azure Migrate hubb väljer du det verktyg som du vill använda för utvärdering och lägger till det i ett Azure Migrate-projekt. Om du lägger till ett ISV-verktyg eller en driv kraft:

- Kom igång genom att skaffa en licens eller registrera dig för en kostnads fri utvärderings version i enlighet med verktygs anvisningarna. Verktyg-licensieringen bestäms av ISV/-verktyget. 
- I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Följ anvisningarna för att ansluta.
- Spåra din migrerings resa inifrån Azure Migrate-projektet, i alla verktyg.


## <a name="movere"></a>Movere

Superkraft är en SaaS-plattform som ökar Business Intelligence genom att noggrant presentera hela IT-miljöer inom en enda dag. När organisationer växer, förändras och digitalt optimeras, ger lösningen företag den säkerhet de behöver för att få insyn och kontroll över sina miljöer oavsett plattform, program eller geografi. Den här förflyttningen [förvärvades](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) av Microsoft och säljs inte längre som ett fristående erbjudande.  Kraftning är tillgängligt via Microsofts program för lösnings bedömning och molnet. [Läs mer](https://www.movere.io) om superkraft. Om du har frågor kan du skicka dem till: movereq@microsoft.com eller kontakta din Microsoft-representant.

Vi rekommenderar att du även tittar på Azure Migrate, vår inbyggda migration service. Azure Migrate tillhandahåller en central hubb för att förenkla migreringen till molnet. Hubben innehåller omfattande stöd för olika arbets belastningar, inklusive fysiska och virtuella servrar, databaser och program. Med en synlighet från slut punkt till slut punkt blir det enkelt att spåra framsteg i hela identifiering, utvärdering och migrering. Med både Azure-och partners inbyggda ISV-verktyg har Azure Migrate också en mängd olika funktioner, inklusive virtuell och fysisk server identifiering, prestanda-baserad höger storlek, kostnads planering, import-baserade utvärderingar och program utan agent beroende analys. Om du vill ha expert hjälp för att komma igång har Microsoft kvalificerad [Azure expert Managed Service Provider](https://azure.microsoft.com/partners) som vägleder dig längs resan. Kolla in [Azure Migrate webbplats](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns två versioner av tjänsten Azure Migrate:

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: om du använde den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöds) bör du nu använda den aktuella versionen. Du kan inte längre skapa Azure Migrate projekt med hjälp av den tidigare versionen och vi rekommenderar att du inte gör nya identifieringar. Om du vill komma åt befintliga projekt går du till Azure Portal och söker efter och väljer **Azure Migrate**. På **Azure Migrate** -instrumentpanelen finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.



## <a name="next-steps"></a>Nästa steg

- Prova våra självstudier för att utvärdera virtuella [VMware-datorer](tutorial-prepare-vmware.md), [virtuella Hyper-V-datorer](tutorial-prepare-hyper-v.md)och [fysiska servrar](tutorial-prepare-physical.md).
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
