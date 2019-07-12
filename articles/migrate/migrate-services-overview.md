---
title: Om Azure Migrate | Microsoft Docs
description: Ger en översikt över tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 083eaaa5be35d515a477ce6286f226b267569e1e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840350"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Den här artikeln innehåller en snabb översikt över Azure Migrate.

Azure Migrate hjälper dig att migrera till Azure. Azure Migrate erbjuder en centraliserad hubb för att spåra identifiering, bedömning och migrering av lokala infrastruktur, program och data till Azure. Hubben tillhandahåller Azure-verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden. Den tillhandahåller:

- **Enhetlig migreringsplattform**: Använda en enda portal för att starta, körs, och spåra migreringen till Azure.
- **Antal verktyg**: Azure Migrate innehåller inbyggda verktyg och integreras med andra Azure-tjänster, samt med ISV-verktyg. Välj rätt bedömning och migrering verktyg baserat på din organisations krav. 
- **Azure Migrate Server-utvärdering**: Använd verktyget Server-utvärdering för att utvärdera lokala virtuella VMware-datorer och Hyper-V-datorer för migrering till Azure.
- **Azure Migrate servermigrering**: Använd Migreringsverktyget för Server för att migrera lokala virtuella VMware-datorer, Hyper-V-datorer, virtuella datorer och fysiska servrar till Azure.
- **Azure Migrate Databasutvärderingen**: Utvärdera lokala databaser för migrering till Azure.
- **Azure Migrate Databasmigrering**: Migrera lokala databaser till Azure.


## <a name="azure-migrate-versions"></a>Azure Migrate-versioner

Det finns två versioner av Azure Migrate-tjänsten:

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate-projekt, upptäcka lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om nyheterna i den här versionen.
- **Tidigare version**: Om du använde den tidigare versionen av Azure Migrate (endast bedömning av lokala virtuella VMware-datorer har stöds), bör du nu använda den aktuella versionen. Du kan inte längre skapa Azure Migrate-projekt med hjälp av den tidigare versionen eller utföra nya identifieringar. Du kan emellertid fortfarande komma åt befintliga projekt. Att göra detta i Azure portal > **alla tjänster**, Sök efter **Azure Migrate**. På instrumentpanelen för Azure Migrate finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.

## <a name="isv-integration"></a>ISV-integrering

Förutom inbyggda Azure-verktyg integreras Azure Migrate med ett antal ISV-erbjudanden. Du kan identifiera de verktyg du behöver och lägga till den i ett Azure Migrate-projekt. Du kan centralt spåra migreringen från inom Azure Migrate-projekt i Azure och ISV-verktyg.

**ISV** | **Funktion**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Utvärdera
[Enheten 42](https://docs.device42.com/) | Utvärdera
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Utvärdera
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Utvärdera
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Utvärdera och migrera
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrera

### <a name="selecting-an-isv-tool"></a>Att välja ett ISV-verktyg

När du lägger till ett ISV-verktyg i ett Azure Migrate-projekt, Kom igång med verktyget genom att hämta en licens eller registrera dig för en kostnadsfri utvärderingsversion, i enlighet med ISV-princip. Det finns ett alternativ för att ansluta till Azure Migrate i varje verktyg. Följ anvisningarna för verktyg och dokumentation, för att ansluta verktyget med Azure Migrate.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate Server-utvärdering

Azure Migrate Server-utvärdering identifierar och utvärderar lokala virtuella VMware-datorer och Hyper-V-datorer för migrering till Azure. Det hjälper dig att identifiera följande:

- **Azure-beredskap:** Bedöma om lokala datorer är klara för migrering till Azure.
- **Azure storlek:** Beräkna storleken på virtuella Azure-datorer efter migreringen.
- **Azure kostnadsuppskattning:** Beräknar kostnaderna för att köra lokala servrar i Azure.
- **Visualisering av beroenden:** Identifiera beroenden mellan servrar och det bästa sättet att flytta beroende servrar till Azure. 

Server-utvärdering använder en enkel installation som du distribuerar både lokalt och registrera med Server-utvärdering.

- Installationen identifierar lokala datorer, ansluter till Server-utvärdering och kontinuerligt skickar metadata och prestandarelaterade data till Azure Migrate.
- Identifiering är utan Agent. Inget behöver installeras på identifierade virtuella datorer.
- När datorerna är identifierade du samla in dem i grupper som vanligtvis består av virtuella datorer du vill migrera tillsammans.
- Du skapar en utvärdering för en grupp. Du kan sedan analysera utvärderingen, att ta reda på migreringsstrategin.

## <a name="azure-migrate-server-migration"></a>Azure Migrate-servermigrering

Azure Migrate servermigrering kan du migrera lokala virtuella VMware-datorer, Hyper-V-datorer, fysiska servrar, andra virtualiserade datorer och offentliga molnbaserade virtuella datorer till Azure. Du kan migrera datorer efter en bedömning av dem, eller utan en utvärdering. 

## <a name="azure-migrate-database-assessment"></a>Azure Migrate Databasutvärderingen

Azure Migrate kan integreras med Data Migration Assistant (DMA) att utvärdera den lokala SQL Server-databaser för migrering till Azure SQL DB, Azure SQL Managed Instance eller virtuella Azure-datorer som kör SQL Server. DMA innehåller information om potentiella blockerande problem för migrering. Den identifierar funktioner som inte stöds, samt nya funktioner som du kan dra nytta av efter migreringen och hjälper dig att identifiera rätt väg för Databasmigrering. [Läs mer](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Azure Migrate Databasmigrering

Azure Migrate kan integreras med den Azure Database Migration Service (DMS), att migrera lokala databaser till Azure. Använd DMS för att migrera lokala databaser till Azure virtuella datorer som kör SQL, Azure SQL DB och Azure SQL-hanterade instanser. [Läs mer](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Web app bedömning och migrering

Du kan utvärdera och migrera lokala webbappar till Azure från Azure Migrate-hubben.

- **Utvärdera webbappar online**: Använda Azure App Service Migration Assistant för att bedöma lokala webbplatser för migrering till Azure App Service.
- **Migrera webbprogram**: Migrera .NET- och PHP-webbappar till Azure med Azure App Service Migration Assistant.

[Läs mer.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Migrering av data offline

Du kan använda Data Box offline produktfamiljen för att flytta stora mängder data till Azure. [Läs mer](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Nästa steg

- Prova våra självstudier för att utvärdera [virtuella VMware-datorer](tutorial-assess-vmware.md) och [Hyper-V-datorer](tutorial-assess-hyper-v.md).
- [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
