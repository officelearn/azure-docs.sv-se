---
title: Om Azure Migrate | Microsoft Docs
description: Ger en översikt över tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: def0832898170e0a278c403349eab1bd89050a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498741"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Den här artikeln ger en snabb översikt över Azure Migrate.

Azure Migrate hjälper dig att migrera från en lokal plats till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben tillhandahåller Azure-verktyg och-tjänster för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV). Azure Migrate tillhandahåller:

- **Plattform för enhetlig migrering**: en enda portal för att starta, köra och spåra migrerings resan till Azure.
- **Olika verktyg**: inbyggda verktyg och integrering med andra Azure-tjänster, samt med ISV-verktyg. Välj rätt verktyg för bedömning och migrering baserat på organisationens krav.
- **Arbets belastningar**: Azure Migrate tillhandahåller verktyg för bedömning och migrering för:
    - **Servrar**: Använd Microsoft-verktyg eller ISV-verktyg för utvärdering och migrering av servrar till virtuella Azure-datorer.
    - **Databaser**: utnyttja Microsoft-och ISV-verktyg för utvärdering och migrering av lokala databaser till Azure SQL DB eller Azure SQL Managed instance.
    - **Webb program**: Använd Azure App Service-assistenten för att utvärdera och migrera lokala webb program till Azure App Service.
    - **Virtuella skriv bord**: Använd ISV-verktyg för att utvärdera och migrera lokala virtuella Skriv bords infrastrukturer (VDI) till virtuella Windows-datorer i Azure.
    - **Data**: Använd Azure Data Box serie produkter för att snabbt och kostnads effektivt migrera data till Azure.

## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns för närvarande två versioner av tjänsten Azure Migrate:

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: om du använde den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöds) bör du nu använda den aktuella versionen. Du kan inte längre skapa Azure Migrate projekt med hjälp av den tidigare versionen och vi rekommenderar att du inte gör nya identifieringar. Om du vill komma åt befintliga projekt går du till Azure Portal > **alla tjänster**och söker efter **Azure Migrate**. På Azure Migrate-instrumentpanelen finns ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.

## <a name="isv-integration"></a>ISV-integrering

Förutom inbyggda Microsoft-verktyg kan Azure Migrate integreras med ett antal ISV-erbjudanden. 

**ISV** | **Funktion**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Utvärdera
[Enhet 42](https://docs.device42.com/) | Utvärdera
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Utvärdera
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Utvärdera
[Samhyres teknik](https://www.corenttech.com/AzureMigrate/) | Utvärdera och migrera
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrera
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Utvärdera



### <a name="selecting-an-isv-tool"></a>Välja ett ISV-verktyg

Du identifierar det verktyg som du behöver och lägger till det i ett Azure Migrate-projekt.

- När du har lagt till ett ISV-verktyg kommer du igång genom att skaffa en licens eller registrera dig för en kostnads fri utvärderings version, i enlighet med ISV-principen. Licensiering för ISV-verktyg är i enlighet med ISV-licensierings modellen.
- I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Följ anvisningarna och dokumentationen för verktyget för att ansluta verktyget med Azure Migrate. L
- Du kan centralt spåra migreringen från Azure Migrate-projektet i Azure-och ISV-verktyg.


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


## <a name="database-assessment"></a>Databas utvärdering

Azure Migrate integreras med Microsoft Data Migration Assistant (DMA) för att utvärdera lokala SQL Server-databaser för migrering till Azure SQL DB, Azure SQL-hanterad instans eller virtuella Azure-datorer som kör SQL Server. DMA tillhandahåller information om möjliga spärrnings problem för migrering. Den identifierar funktioner som inte stöds, samt nya funktioner som du kan dra nytta av efter migreringen, och hjälper dig att identifiera rätt sökväg för migrering av databasen. [Läs mer](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration"></a>Databasmigrering

Azure Migrate integreras med Azure Database Migration Service (DMS) för att migrera lokala databaser till Azure. Använd DMS för att migrera lokala databaser till virtuella Azure-datorer som kör SQL, Azure SQL DB och Azure SQL Managed instances. [Läs mer](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-migration"></a>Migrering av webbappar

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
