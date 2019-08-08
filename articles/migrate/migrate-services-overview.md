---
title: Om Azure Migrate | Microsoft Docs
description: Ger en översikt över tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 03918976935f9071ba2a7951e29195118943a8ef
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845829"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Den här artikeln ger en snabb översikt över Azure Migrate.

Azure Migrate hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben ger Azure-verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV). Den tillhandahåller:

- **Plattform för enhetlig migrering**: Använd en enda portal för att starta, köra och spåra migrerings resan till Azure.
- **Verktygs intervall**: Azure Migrate tillhandahåller inbyggda verktyg och integreras med andra Azure-tjänster, samt med ISV-verktyg. Välj rätt verktyg för bedömning och migrering baserat på organisationens krav. 
- **Azure Migrate Server utvärdering**: Använd verktyget för Server utvärdering för att utvärdera lokala virtuella VMware-datorer och virtuella Hyper-V-datorer för migrering till Azure.
- **Migrering av Azure Migrate Server**: Använd Migreringsverktyg för Server för att migrera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer, virtuella datorer i molnet och fysiska servrar till Azure.
- **Utvärdering av Azure Migrates databas**: Utvärdera lokala databaser för migrering till Azure.
- **Migrering av Azure Migrate Database**: Migrera lokala databaser till Azure.


## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns två versioner av tjänsten Azure Migrate:

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: Om du använde den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöds) bör du nu använda den aktuella versionen. Du kan inte längre skapa Azure Migrate projekt med hjälp av den tidigare versionen och vi rekommenderar att du inte gör nya identifieringar. Om du vill komma åt befintliga projekt går du till Azure Portal > **alla tjänster**och söker efter **Azure Migrate**. På Azure Migrate-instrumentpanelen finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.

## <a name="isv-integration"></a>ISV-integrering

Förutom interna Azure-verktyg kan Azure Migrate integreras med ett antal ISV-erbjudanden. Du identifierar det verktyg som du behöver och lägger till det i ett Azure Migrate-projekt. Du kan centralt spåra migrerings resan från Azure Migrate-projektet, via Azure och ISV-verktyg.

**ISV** | **Funktion**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Utvärdera
[Enhet 42](https://docs.device42.com/) | Utvärdera
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Utvärdera
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Utvärdera
[Samhyres teknik](https://www.corenttech.com/AzureMigrate/) | Utvärdera och migrera
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrera

### <a name="selecting-an-isv-tool"></a>Välja ett ISV-verktyg

När du har lagt till ett ISV-verktyg till ett Azure Migrate-projekt, kom igång med verktyget genom att skaffa en licens eller registrera dig för en kostnads fri utvärderings version, i enlighet med ISV-principen. I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Följ anvisningarna och dokumentationen för verktyget för att ansluta verktyget med Azure Migrate. Licensiering för ISV-verktyg är i enlighet med ISV-licensierings modellen.

## <a name="azure-migrate-server-assessment"></a>Utvärdering av Azure Migrate Server

Azure Migrate Server utvärderingen identifierar och utvärderar lokala virtuella VMware-datorer och virtuella Hyper-V-datorer för migrering till Azure. Det hjälper dig att identifiera följande:

- **Azure-beredskap:** Utvärdera om lokala datorer är klara för migrering till Azure.
- **Azure-storlek:** Beräkna storleken på virtuella Azure-datorer efter migreringen.
- **Kostnads uppskattning i Azure:** Uppskattar kostnader för att köra lokala servrar i Azure.
- **Beroende visualisering:** Identifiera beroenden mellan servrar och det bästa sättet att flytta beroende servrar till Azure. 

Server utvärderingen använder en förenklad installation som du distribuerar lokalt och registrerar dig för Server utvärdering.

- Enheten identifierar lokala datorer, ansluter till Server utvärdering och skickar kontinuerligt data om metadata och prestanda till Azure Migrate.
- Identifieringen är agent utan agent. Inget måste installeras på identifierade virtuella datorer.
- När datorerna har identifierats samlar du in dem i grupper som vanligt vis består av virtuella datorer som du vill migrera tillsammans.
- Du skapar en utvärdering för en grupp. Sedan kan du analysera utvärderingen för att ta reda på din strategi för migrering.

## <a name="azure-migrate-server-migration"></a>Migrering av Azure Migrate Server

Med Azure Migrate Server-migrering kan du migrera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, andra virtualiserade datorer och virtuella datorer i offentliga moln till Azure. Du kan migrera datorer efter att ha bedömt dem, eller utan en utvärdering. 

## <a name="azure-migrate-database-assessment"></a>Utvärdering av Azure Migrate databasen

Azure Migrate integreras med Data Migration Assistant (DMA) för att utvärdera lokala SQL Server-databaser för migrering till Azure SQL DB, Azure SQL-hanterad instans eller virtuella Azure-datorer som kör SQL Server. DMA tillhandahåller information om möjliga spärrnings problem för migrering. Den identifierar funktioner som inte stöds, samt nya funktioner som du kan dra nytta av efter migreringen, och hjälper dig att identifiera rätt sökväg för migrering av databasen. [Läs mer](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Migrering av Azure Migrate Database

Azure Migrate integreras med Azure Database Migration Service (DMS) för att migrera lokala databaser till Azure. Använd DMS för att migrera lokala databaser till virtuella Azure-datorer som kör SQL, Azure SQL DB och Azure SQL Managed instances. [Läs mer](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Webb programs bedömning och migrering

Från Azure Migrate Hub kan du utvärdera och migrera lokala webbappar till Azure.

- **Utvärdera Web Apps online**: Använd Azure App Service Migration Assistant för att utvärdera lokala webbplatser för migrering till Azure App Service.
- **Migrera webbappar**: Migrera .NET-och PHP-webbappar till Azure med hjälp av Azure App Service Migration Assistant.

[Läs mer.](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Offline-datamigrering

Du kan använda Data Box-enhet offline-serien med produkter för att flytta stora mängder data till Azure. [Läs mer](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Nästa steg

- Prova våra självstudier för att utvärdera virtuella [VMware-datorer](tutorial-assess-vmware.md) och [virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md).
- [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
