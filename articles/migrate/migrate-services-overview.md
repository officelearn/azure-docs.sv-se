---
title: Om Azure Migrate
description: Läs mer om Azure Migrate-tjänsten.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fe6386346282cf182397f6420c541d629ba0aab3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768394"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Den här artikeln innehåller en snabb översikt över Azure Migrate-tjänsten.

Azure Migrate tillhandahåller en centraliserad hubb för att bedöma och migrera till Lokala Azure-servrar, infrastruktur, program och data.

Azure Migrate innehåller följande funktioner:

- **Enhetlig migreringsplattform:** En enda portal för att starta, köra och spåra din migrering till Azure.
- **Utbud av verktyg**: En rad verktyg för bedömning och migration. Verktygen inkluderar Azure Migrate: Serverutvärdering och Azure Migrera: Servermigrering. Azure Migrate integreras med andra Azure-tjänster och med andra verktyg och oberoende programleverantörer (ISV) erbjudanden.
- **Bedömning och migrering**: I hubben Azure Migrate kan du bedöma och migrera:
    - **Servrar**: Utvärdera lokala servrar och migrera dem till virtuella Azure-datorer.
    - **Databaser**: Utvärdera lokala databaser och migrera dem till Azure SQL Database eller till en hanterad Azure SQL-databas.Databases : Assess on-premises databases and migrate them to Azure SQL Database or to an Azure SQL Database managed instance.
    - **Webbprogram**: Utvärdera lokala webbprogram och migrera dem till Azure App Service med hjälp av Migreringsassistenten för Azure App Service.
    - Virtuella skrivbord : Utvärdera din lokala virtuella **skrivbordsinfrastruktur**(VDI) och migrera den till Windows Virtual Desktop i Azure.
    - **Data**: Migrera stora mängder data till Azure snabbt och kostnadseffektivt med hjälp av Azure Data Box-produkter.

## <a name="integrated-tools"></a>Integrerade verktyg

Azure Migrate-hubben innehåller följande verktyg:

**Verktyg** | **Bedöma och migrera** | **Detaljer**
--- | --- | ---
**Azure Migrera: Serverutvärdering** | Utvärdera servrar. | Upptäck och utvärdera lokala virtuella datorer med VMware, virtuella hyper-virtuella datorer och fysiska servrar som förberedelse för migrering till Azure.
**Azure Migrera: Servermigrering** | Migrera servrar. | Migrera virtuella datorer med VMware, virtuella hyper-virtuella datorer, fysiska servrar, andra virtualiserade datorer och offentliga virtuella datorer i molnet till Azure.
**Data Migration Assistant** | Utvärdera lokala SQL Server-databaser för migrering till Azure SQL Database, en Azure SQL Database-hanterad instans eller Virtuella Azure-datorer som kör SQL Server. | Datamigreringsassistenten hjälper till att identifiera potentiella problem som blockerar migreringen. Den identifierar funktioner som inte stöds, nya funktioner som kan gynna dig efter migreringen och rätt sökväg för databasmigrering. [Läs mer](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Migrera lokala databaser till Azure-virtuella datorer som kör SQL Server, Azure SQL Database eller Azure SQL Database hanterade instanser. | [Läs mer](https://docs.microsoft.com/azure/dms/dms-overview) om tjänsten för databasmigrering.
**Movere** | Utvärdera servrar. | [Läs mer](#movere) om Movere.
**Migreringsassistent för webbapp** | Utvärdera lokala webbappar och migrera dem till Azure. |  Använd Azure App Service Migration Assistant för att bedöma lokala webbplatser för migrering till Azure App Service.<br/><br/> Använd Migreringsassistenten för att migrera .NET- och PHP-webbappar till Azure. [Läs mer](https://appmigration.microsoft.com/) om Migreringsassistenten för Azure App Service.
**Azure Data Box** | Migrera offlinedata. | Använd Azure Data Box-produkter för att flytta stora mängder offlinedata till Azure. [Läs mer](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Om du är i Azure Government kan externa integrerade verktyg och ISV-erbjudanden inte skicka data till Azure Migrate-projekt. Du kan använda verktyg oberoende av dem.

## <a name="isv-integration"></a>ISV-integration

Azure Migrate integreras med flera ISV-erbjudanden. 

**Isv**    | **Funktion**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrera servrar.
[Cloudamize](https://www.cloudamize.com/platform) | Utvärdera servrar.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Utvärdera och migrera servrar.
[Device42](https://docs.device42.com/) | Utvärdera servrar.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Bedöm VDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrera servrar.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Utvärdera servrar.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Utvärdera servrar och databaser.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrera: Serverutvärderingsverktyg

Azure Migrate: Server Assessment tool identifierar och utvärderar lokala virtuella datorer med VMware, virtuella hyper-virtuella datorer och fysiska servrar för migrering till Azure.

Så här gör verktyget:

- **Azure-beredskap**: Utvärderar om lokala datorer är redo för migrering till Azure.
- **Azure storlek:** Uppskattar storleken på Azure virtuella datorer efter migreringen.
- **Azure kostnadsuppskattning**: Beräknar kostnader för att köra lokala servrar i Azure.
- **Beroendeanalys**: Identifierar beroenden och optimeringsstrategier för att flytta beroendeservrar som är beroende av varandra till Azure. Läs mer om serverutvärdering med [beroendeanalys](concepts-dependency-visualization.md).

Serverutvärdering använder en lätt [Azure Migrate-installation](migrate-appliance.md) som du distribuerar lokalt.

- Installationen körs på en virtuell dator eller en fysisk server. Du kan enkelt installera den med hjälp av en nedladdad mall.
- Apparaten upptäcker lokala maskiner. Det skickar också kontinuerligt datormetadata och prestandadata till Azure Migrate.
- Identifiering av apparater är agentless. Ingenting är installerat på upptäckta maskiner.
- Efter identifiering av apparater kan du samla in identifierade datorer i grupper och köra utvärderingar för varje grupp.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrera: Verktyget Servermigrering

Azure Migrate: Server Migration verktyget hjälper dig att migrera till Azure:

- Lokala virtuella VMware-datorer
- Hyper-V:s virtuella datorer
- Fysiska servrar
- Andra virtualiserade datorer
- Offentliga virtuella moln-datorer

Du kan migrera datorer när du har bedömt dem eller migrerat dem utan en bedömning.

För agentlös migrering av virtuella datorer med VMware och migrering av virtuella hyper-virtuella datorer använder servermigrering en Azure Migrate-apparat som du distribuerar lokalt. Apparaten används också om du ställer in serverutvärdering. Det beskrivs i föregående avsnitt.

## <a name="selecting-assessment-and-migration-tools"></a>Välja bedömnings- och migreringsverktyg

I hubben Azure Migrate väljer du det verktyg som du vill använda för utvärdering eller migrering och lägger till det i ett Azure Migrate-projekt. Om du lägger till ett ISV-verktyg eller Movere:

- För att komma igång, skaffa en licens eller registrera dig för en kostnadsfri utvärderingsversion genom att följa verktygsinstruktionerna. Varje ISV eller verktyg anger verktygslicensiering.
- Varje verktyg har ett alternativ för att ansluta till Azure Migrate. Följ verktygsanvisningarna för att ansluta.
- Spåra migreringen i alla verktyg från Azure Migrate-projektet.

## <a name="movere"></a>Movere

Movere är en saaS-plattform (Software as a service). Det ökar business intelligence genom att korrekt presentera hela IT-miljöer inom en enda dag. Organisationer och företag växer, förändras och optimeras digitalt. När de gör det ger Movere dem det förtroende som behövs för att se och kontrollera sina miljöer, oavsett plattform, program eller geografi.

Microsoft [förvärvade](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) Movere och säljs inte längre som ett fristående erbjudande. Movere är tillgängligt via Microsoft Solution Assessment och Microsoft Cloud Economics Program. [Läs mer](https://www.movere.io) om Movere.

Vi rekommenderar att du även tittar på Azure Migrate, vår inbyggda migreringstjänst. Azure Migrate är ett centralt nav för att förenkla molnmigrering. Navet stöder utförligt arbetsbelastningar som fysiska och virtuella servrar, databaser och program. Med fullständig synlighet kan du enkelt spåra förloppet under identifiering, utvärdering och migrering.

Med både Azure- och partner-ISV-verktyg inbyggda har Azure Migrate ett omfattande utbud av funktioner, inklusive:

- Identifiering av virtuella och fysiska servrar.
- Prestationsbaserad rightsizing.
- Kostnadsplanering.
- Importbaserade bedömningar.
- Beroendeanalys av agentlösa program.

Om du letar efter experthjälp för att komma igång har Microsoft skickliga [Azure Expert Managed Service Providers](https://azure.microsoft.com/partners) som kan vägleda dig. Kolla in [webbplatsen Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Azure Migrera versioner

Det finns två versioner av Azure Migrate-tjänsten.

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate-projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: Den tidigare versionen av Azure Migrate stöder endast utvärdering av lokala virtuella datorer med VMware. Om du använde den tidigare versionen bör du nu använda den aktuella versionen. Du kan inte längre skapa Azure Migrate-projekt med den tidigare versionen. Och vi rekommenderar att du inte gör nya upptäckter med den.

    Om du vill komma åt befintliga projekt i Azure-portalen söker du efter och väljer **Azure Migrate**. **Instrumentpanelen Azure Migrate** har ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.

## <a name="next-steps"></a>Nästa steg

- Prova våra tutorials för att bedöma [virtuella datorer,](tutorial-prepare-vmware.md)virtuella datorer med [hyper-v eller](tutorial-prepare-hyper-v.md) [fysiska servrar.](tutorial-prepare-physical.md)
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
