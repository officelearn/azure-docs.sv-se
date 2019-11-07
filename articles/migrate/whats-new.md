---
title: Vad är nytt i Azure Migrate | Microsoft Docs
description: Ger en översikt över tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/06/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: af9305aafc3a77df9d7c4cffa65f6c61c53ad8a4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715389"
---
# <a name="whats-new-in-azure-migrate"></a>Vad är nytt i Azure Migrate

[Azure Migrate](migrate-services-overview.md) hjälper dig att identifiera, utvärdera och migrera lokala servrar, appar och data till Microsoft Azure molnet. I den här artikeln sammanfattas nya funktioner i Azure Migrate.



## <a name="update-november-2019"></a>Uppdatering (november 2019)



Ett antal nya funktioner har lagts till i Azure Migrate:

> [!NOTE]
> Om du ännu inte ser några av de här funktionerna i Azure Migrate-portalen låser du dig. De kommer att visas under nästa vecka eller så.

- **Utvärdering av fysisk server**. Utvärdering av lokala fysiska servrar stöds nu, utöver migrering av fysiska servrar som redan stöds.
- **Importera-baserad utvärdering**. Utvärdering av datorer som använder metadata-och prestanda data från en CSV-fil stöds nu.
- **Program identifiering**: Azure Migrate nu stöder identifiering av program nivå för appar, roller och funktioner med hjälp av Azure Migrate-enheten. Detta stöds för närvarande endast för virtuella VMware-datorer och är begränsat till enbart identifiering (utvärderingen stöds inte för närvarande). [Läs mer](how-to-discover-applications.md)
- **Beroende visualisering för agenter**: du behöver inte längre installera agenter för beroende visualisering. Både agent-och agentbaserade stöds nu.
- **Virtuellt skriv bord**: Använd ISV-verktyg för att utvärdera och migrera lokala virtuella Skriv bords infrastrukturer (VDI) till virtuella Windows-datorer i Azure.
- **Webbapp**: Azure App Service Migration Assistant som används för att utvärdera och migrera webbappar är nu integrerat i Azure Migrate.

Nya verktyg för utvärdering och migrering har lagts till i Azure Migrate:

- **Rackprogram**: erbjuder molnbaserad migrering.
- Driv **kraft**: erbjudande utvärdering.

[Lär dig mer](migrate-services-overview.md) om att använda verktyg och ISV-erbjudanden för utvärdering och migrering i Azure Migrate.

## <a name="release-version-july-2019"></a>Versions version (2019 juli)

Den aktuella versionen av Azure Migrate släpptes i juli 2019. 

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. 
- **Tidigare version**: för kund som använder den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöddes) bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte längre skapa nya Azure Migrate projekt eller utföra nya identifieringar. Du kan fortfarande komma åt befintliga projekt. Om du vill göra detta i Azure Portal > **alla tjänster**söker du efter **Azure Migrate**. I Azure Migrate-meddelanden finns det en länk för att komma åt gamla Azure Migrate-projekt.


### <a name="azure-migrate-features"></a>Azure Migrate funktioner

Den aktuella versionen av Azure Migrate innehåller ett antal nya funktioner:


- **Plattform för enhetlig migrering**: Azure Migrate nu tillhandahåller en enda portal som centraliserar, hanterar och spårar din migrerings resa till Azure, med ett förbättrat distributions flöde och en portal upplevelse.
- **Verktyg för utvärdering och migrering**: Azure Migrate tillhandahåller inbyggda verktyg och integreras med andra Azure-tjänster, samt med oberoende program varu leverantörs verktyg (ISV). [Läs mer](migrate-services-overview.md#isv-integration) om ISV-integrering.
- **Azure Migrate utvärdering**: om du använder verktyget för att utvärdera Azure Migrate Server kan du utvärdera virtuella VMware-datorer och virtuella Hyper-V-datorer för migrering till Azure. Du kan också utvärdera för migrering med andra Azure-tjänster och ISV-verktyg.
- **Azure Migrate migrering**: med hjälp av verktyget för migrering av Azure Migrate Server kan du migrera lokala virtuella VMware-datorer och virtuella Hyper-V-datorer till Azure, samt fysiska servrar, andra virtualiserade servrar och privata/offentliga virtuella moln. Dessutom kan du migrera till Azure med hjälp av ISV-verktyg.
- **Azure Migrate-utrustning**: Azure Migrate distribuerar en förenklad installation för identifiering och utvärdering av lokala virtuella VMware-datorer och virtuella Hyper-V-datorer.
    - Den här enheten används av Azure Migrate Server-utvärdering och migrering av Azure Migrate Server för migrering utan agent.
    - Installations programmet identifierar kontinuerligt serverns metadata och prestanda data, i syfte att utvärdera och migrera.  
- **VMware VM-migrering**: Azure Migrate Server-migrering innehåller flera metoder för att migrera lokala virtuella VMware-datorer till Azure.  En agent lös migrering med hjälp av Azure Migrate-installationen och en agent-baserad migrering som använder en replikeringsprincip och distribuerar en agent på varje virtuell dator som du vill migrera. [Läs mer](server-migrate-overview.md)
 - **Databas utvärdering och migrering**: från Azure Migrate kan du utvärdera lokala databaser för migrering till Azure med hjälp av azure Database Migration Assistant. Du kan migrera databaser med hjälp av Azure Database Migration Service.
- **Migrering**av webbappar: du kan utvärdera webbappar med hjälp av en offentlig slut punkts-URL med Azure App Service. För migrering av interna .NET-appar kan du hämta och köra App Service Migration Assistant. 
- **Data Box-enhet**: importera stora mängder offline-data till Azure med Azure Data Box i Azure Migrate.


## <a name="next-steps"></a>Nästa steg

- [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
- Prova våra självstudier för att utvärdera virtuella [VMware-datorer](tutorial-assess-vmware.md) och [virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md).
