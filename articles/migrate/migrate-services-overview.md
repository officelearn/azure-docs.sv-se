---
title: Om Azure Migrate
description: Läs mer om Azure Migrate-tjänsten.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: a9723f15d496393d27bdd227ec1121a7878b37e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127733"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Den här artikeln innehåller en snabb översikt över Azure Migrate-tjänsten.

Azure Migrate tillhandahåller en centraliserad hubb för att bedöma och migrera lokala servrar, infrastruktur, program och data till Azure. Azure Migrate innehåller följande funktioner:

- **Enhetlig migreringsplattform:** En enda portal för att starta, köra och spåra din migreringsresa till Azure.
- **Utbud av verktyg**: En rad verktyg för bedömning och migration. Verktygen inkluderar Azure Migrate: Server Assessment och Azure Migrate: Server Migration. Azure Migrate integreras med andra Azure-tjänster och med andra verktyg och oberoende programleverantörer (ISV) erbjudanden.
- **Bedömning och migrering**: I Azure Migrate-hubben kan du bedöma och migrera:
    - **Servrar**: Utvärdera och migrera lokala servrar till virtuella Azure-datorer.
    - **Databaser**: Utvärdera och migrera lokala databaser till Azure SQL DB eller till Azure SQL Managed Instance.
    - **Webbprogram**: Utvärdera och migrera lokala webbprogram till Azure App Service med hjälp av Azure App Service Assistant.
    - Virtuella skrivbord : Utvärdera och migrera din lokala virtuella **skrivbordsinfrastruktur**(VDI), till Windows Virtual Desktop i Azure.
    - **Data**: Migrera stora mängder data till Azure snabbt och kostnadseffektivt med hjälp av Azure Data Box-produkter. 


## <a name="integrated-tools"></a>Integrerade verktyg

Azure Migrate-hubben innehåller dessa verktyg.

**Verktyg** | **Bedöma/migrera** | **Detaljer**
--- | --- | ---
**Azure Migrera:Server-utvärdering** | Utvärdera servrar. | Upptäck och utvärdera lokala virtuella datorer med VMware, virtuella hyper-virtuella datorer och fysiska servrar som förberedelse för migrering till Azure.
**Azure Migrera:Servermigrering** | Migrera servrar. | Migrera virtuella datorer med VMware, virtuella hyper-virtuella datorer, fysiska servrar, andra virtualiserade datorer och offentliga virtuella datorer i molnet till Azure. 
**Databasmigreringsassistent (DMA)** | Utvärdera lokala SQL Server-databaser för migrering till Azure SQL DB, Azure SQL Managed Instance eller till Virtuella Azure-datorer som kör SQL Server. | DMA hjälper till att identifiera potentiella blockeringsproblem för migrering. Den identifierar funktioner som inte stöds, nya funktioner som du kan dra nytta av efter migreringen och hjälper dig att identifiera rätt sökväg för databasmigrering. [Läs mer](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Databasmigreringstjänst (DMS)** | Migrera lokala databaser till Virtuella Azure-datorer som kör SQL, Azure SQL DB och Azure SQL Managed Instances. | [Läs mer](https://docs.microsoft.com/azure/dms/dms-overview) om DMS.
**Movere** | Utvärdera servrar. | [Läs mer](#movere) om Movere.
**Migreringsassistent för webbapp** | Utvärdera och migrera lokala webbappar till Azure. |  Använd Migreringsassistenten för Azure App Service för att bedöma lokala webbplatser för migrering till Azure App Service.<br/><br/> Använd assistenten för att migrera .NET- och PHP-webbappar till Azure. [Läs mer](https://appmigration.microsoft.com/) om Migreringsassistenten för Azure App Service.
**Azure Data Box** | Offline-datamigrering. | Använd Azure Data Box-produkter för att flytta stora mängder data offline till Azure. [Läs mer](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>ISV-integration

Azure Migrate integreras med ett antal ISV-erbjudanden. 

**Isv**    | **Funktion**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrera servrar
[Cloudamize](https://www.cloudamize.com/platform) | Utvärdera servrar
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Bedöma och migrera servrar
[Enhet 42](https://docs.device42.com/) | Utvärdera servrar
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Bedöma VDI
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrera servrar
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Utvärdera servrar
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Utvärdera servrar och databaser


## <a name="azure-migrate-server-assessment-tool"></a>Utvärderingsverktyg för Azure Migrate Server

Azure Migrate:Server Assessment-verktyget identifierar och utvärderar lokala virtuella datorer med VMware, virtuella hyper-virtuella datorer och fysiska servrar för migrering till Azure. Så här gör verktyget:

- **Azure-beredskap:** Utvärderar om lokala datorer är redo för migrering till Azure.
- **Azure storlek:** Uppskattar storleken på virtuella Azure-datorer efter migreringen.
- **Azure kostnadsuppskattning:** Beräknade kostnader för att köra lokala servrar i Azure.
- **Beroendeanalys:** Om du använder Serverutvärdering med [beroendeanalys](concepts-dependency-visualization.md)kan du effektivt identifiera beroenden mellan servrar och optimera strategier för att flytta mellanberoende servrar till Azure.


Serverutvärdering använder en lätt [Azure Migrate-installation](migrate-appliance.md) som du distribuerar lokalt.

- Installationen körs på en virtuell dator eller en fysisk server. Du kan enkelt installera den med hjälp av en nedladdad mall.
- Installationen identifierar lokala datorer och skickar kontinuerligt datormetadata och prestandadata till Azure Migrate.
- Identifiering av apparater är agentless. Ingenting är installerat på upptäckta maskiner.
- När du har upptäckt installationen samlar du in identifierade datorer i grupper och kör utvärderingar för en grupp.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate-servermigreringsverktyg

Azure Migrate:Server Migration verktyget hjälper dig att migrera lokala virtuella datorer, virtuella hyper-virtuella datorer, fysiska servrar, andra virtualiserade datorer och offentliga virtuella moln-datorer, till Azure. Du kan migrera datorer när du har bedömt dem eller migrera dem utan en bedömning. 

För agentlös migrering av virtuella datorer med VMware och migrering av virtuella hyper-virtuella datorer använder servermigrering en Azure Migrate-apparat som du distribuerar lokalt. Apparaten används också om du ställer in serverutvärdering och beskrivs i föregående avsnitt.


## <a name="selecting-assessmentmigration-tools"></a>Välja verktyg för bedömning/migrering

I hubben Azure Migrate väljer du det verktyg som du vill använda för utvärdering eller migrering och lägger till det i ett Azure Migrate-projekt. Om du lägger till ett ISV-verktyg eller Movere:

- För att komma igång får du en licens, eller registrera dig för en gratis provperiod, i enlighet med verktyget instruktioner. Verktygslicensiering bestäms av ISV eller verktyget. 
- I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Följ verktygsanvisningarna för att ansluta.
- Du spårar din migreringsresa inifrån Azure Migrate-projektet, i alla verktyg.


## <a name="movere"></a>Movere

Movere är en SaaS-plattform som ökar business intelligence genom att korrekt presentera hela IT-miljöer inom en enda dag. När organisationer växer, förändras och optimeras digitalt ger lösningen företagen det förtroende de behöver för att ha synlighet och kontroll över sina miljöer oavsett plattform, program eller geografi. Movere [förvärvades](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) av Microsoft och säljs inte längre som ett fristående erbjudande.  Movere är tillgängligt via Microsoft Solution Assessment och Cloud Economics Programs. [Läs mer](https://www.movere.io) om Movere. 

Vi rekommenderar att du även tittar på Azure Migrate, vår inbyggda migreringstjänst. Azure Migrate är ett centralt nav för att förenkla migreringen till molnet. Navet har omfattande stöd för olika arbetsbelastningar, inklusive fysiska och virtuella servrar, databaser och program. Fullständig synlighet gör det enkelt att spåra förloppet under hela identifiering, utvärdering och migrering. Med inbyggda Azure- och partner-ISV-verktyg har Azure Migrate också ett omfattande utbud av funktioner, inklusive identifiering av virtuella och fysiska servrar, prestandabaserad rättsstorlek, kostnadsplanering, importbaserade utvärderingar och agentlösa program beroendeanalys. Om du letar efter experthjälp för att komma igång har Microsoft en skicklig [Azure Expert Managed Service Provider](https://azure.microsoft.com/partners) som guidar dig längs resan. Kolla in [webbplatsen Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Azure Migrera versioner

Det finns två versioner av Azure Migrate-tjänsten:

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate-projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: Om du använde den tidigare versionen av Azure Migrate (endast bedömning av lokala virtuella virtuella datorer med vmware stöddes), bör du nu använda den aktuella versionen. Du kan inte längre skapa Azure Migrate-projekt med den tidigare versionen, och vi rekommenderar att du inte utför nya identifieringar. Om du vill komma åt befintliga projekt söker du efter och väljer Azure Migrate i **Azure-portalen**. På instrumentpanelen **för Azure Migrate** finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.



## <a name="next-steps"></a>Nästa steg

- Prova våra tutorials för att bedöma [virtuella datorer,](tutorial-prepare-vmware.md)virtuella datorer med [hyper-v eller](tutorial-prepare-hyper-v.md) [fysiska servrar.](tutorial-prepare-physical.md)
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
