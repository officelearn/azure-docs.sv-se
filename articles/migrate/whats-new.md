---
title: Vad är nytt i Azure Migrate | Microsoft Docs
description: Ger en översikt över tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809997"
---
# <a name="whats-new-in-azure-migrate"></a>Vad är nytt i Azure Migrate

[Azure Migrate](migrate-services-overview.md) hjälper dig att identifiera, utvärdera och migrera servrar, appar och data till Microsoft Azure-molnet. Den här artikeln beskriver nya funktioner i Azure Migrate.



## <a name="azure-migrate-new-version"></a>Azure Migrate ny version

En ny version av Azure Migrate gavs ut i juli 2019. 

- **(Ny) nuvarande**: Använd den här versionen för att skapa Azure Migrate-projekt, upptäcka lokala datorer och dirigera utvärderingar och migreringar. 
- **Tidigare version**: För kunder som använder den tidigare versionen av Azure Migrate (endast bedömning av lokala virtuella VMware-datorer har stöds), bör du nu använda den aktuella versionen. I den tidigare versionen, kan du inte längre skapa nya Azure Migrate-projekt eller utföra nya identifieringar. Du kan fortfarande komma åt befintliga projekt. Du gör detta i Azure portal > alla tjänster, Sök efter Azure Migrate. Det finns en länk till gamla Azure Migrate-projekt i Azure Migrate-meddelanden.


## <a name="azure-migrate-features"></a>Funktioner i Azure Migrate

Den nya versionen av Azure Migrate innehåller ett antal nya funktioner:


- **Enhetlig migreringsplattform**: Azure Migrate innehåller nu en enda portal för att centralisera, hantera och spåra migreringen till Azure, med en förbättrad distributionsflödet och -portalen.
- **Verktyg för bedömning och migrering**: Azure Migrate innehåller inbyggda verktyg och integreras med andra Azure-tjänster, samt med verktyg för oberoende programvaruleverantörer (ISV). [Läs mer](migrate-services-overview.md#isv-integration) om ISV-integration.
- **Azure Migrate-utvärdering**: Med verktyget Azure Migrate Server-utvärdering kan utvärdera du virtuella VMware-datorer och Hyper-V-datorer för migrering till Azure. Du kan också utvärdera för migrering med andra Azure-tjänster och ISV-verktyg.
- **Azure Migrate-migrering**: Med verktyget Azure Migrate servermigrering kan du migrera lokala virtuella VMware-datorer och Hyper-V-datorer till Azure, samt fysiska servrar, andra virtualiserade servrar och privata/offentliga molnbaserade virtuella datorer. Dessutom kan du migrera till Azure med ISV-verktyg.
- **Azure Migrate-installation**: Azure Migrate distribuerar en enkel installation för identifiering och utvärdering av lokala virtuella VMware-datorer och Hyper-V-datorer.
    - Den här installationen används av Azure Migrate Server-utvärdering och Azure Migrate servermigrering för migrering utan agenter.
    - Installationen identifierar kontinuerligt metadata och prestanda serverdata, för bedömning och migrering.  
- **VMware-VM-migrering**:  Azure Migrate-servermigrering innehåller flera olika metoder för att migrera lokala virtuella VMware-datorer till Azure.  En agent med hjälp av Azure Migrate-installation och en agentbaserad migrering som använder en replikering-installation, och distribuerar en agent på varje virtuell dator som du vill migrera. [Läs mer](server-migrate-overview.md)
 - **Databasen bedömning och migrering**: Du kan utvärdera lokala databaser för migrering till Azure med hjälp av Azure Database Migration Assistant från Azure Migrate. Du kan migrera databaser med hjälp av Azure Database Migration Service.
- **Web app-migrering**: Du kan utvärdera web apps med en offentlig slutpunkt-URL med Azure App Service. Du kan ladda ned och köra App Service Migration Assistant för migrering av interna .NET-appar. 
- **Data Box**: Importera stora mängder offline data till Azure med hjälp av Azure Data Box i Azure Migrate.


## <a name="next-steps"></a>Nästa steg

- [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
- Prova våra självstudier för att utvärdera [virtuella VMware-datorer](tutorial-assess-vmware.md) och [Hyper-V-datorer](tutorial-assess-hyper-v.md).
