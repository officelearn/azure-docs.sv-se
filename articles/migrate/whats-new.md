---
title: Nyheter i Azure Migrate
description: Läs mer om nyheter och senaste uppdateringar i Azure Migrate-tjänsten.
ms.topic: overview
ms.date: 03/22/2020
ms.custom: mvc
ms.openlocfilehash: 9767f3ea31b57d23c8a6772ff5eb6500f7550802
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80127596"
---
# <a name="whats-new-in-azure-migrate"></a>Nyheter i Azure Migrate

[Azure Migrate](migrate-services-overview.md) hjälper dig att identifiera, bedöma och migrera lokala servrar, appar och data till Microsoft Azure-molnet. Den här artikeln sammanfattar nya versioner och funktioner i Azure Migrate.

## <a name="update-march-2020"></a>Uppdatering (mars 2020)

En skriptbaserad installation är nu tillgänglig för att konfigurera [Azure Migrate-enheten:](migrate-appliance.md)

- Den skriptbaserade installationen är ett alternativ till . OVA (VMware)/VHD (Hyper-V) installation av apparaten.
- Det ger ett PowerShell-installationsskript som kan användas för att konfigurera installationen av installationen av datorn för VMware/Hyper-V på en befintlig dator som kör Windows Server 2016.

## <a name="update-november-2019"></a>Uppdatering (november 2019)

Ett antal nya funktioner har lagts till i Azure Migrate:

- **Fysisk serverbedömning**. Utvärdering av lokala fysiska servrar stöds nu, förutom fysisk servermigrering som redan stöds.
- **Importbaserad bedömning**. Utvärdering av datorer med hjälp av metadata och prestandadata som tillhandahålls i en CSV-fil stöds nu.
- **Programidentifiering:** Azure Migrate stöder nu identifiering på programnivå av appar, roller och funktioner med azure migrate-installationen. Detta stöds för närvarande endast för virtuella datorer med VMware och är begränsad till identifiering (utvärdering stöds för närvarande inte). [Läs mer](how-to-discover-applications.md)
- **Agentless beroende visualisering**: Du behöver inte längre uttryckligen installera agenter för beroendevisualisering. Både agentless och agent-baserade stöds nu.
- **Virtuellt skrivbord:** Använd ISV-verktyg för att bedöma och migrera lokal virtuell skrivbordsinfrastruktur (VDI) till Windows Virtual Desktop i Azure.
- **Webbapp**: Azure App Service Migration Assistant, som används för att bedöma och migrera webbappar, är nu integrerad i Azure Migrate.

Nya utvärderings- och migreringsverktyg har lagts till i Azure Migrate:

- **Rackware**: Erbjuder molnmigrering.
- **Movere**: Erbjuder bedömning.

[Läs mer](migrate-services-overview.md) om hur du använder verktyg och ISV-erbjudanden för utvärdering och migrering i Azure Migrate.

## <a name="azure-migrate-current-version"></a>Azure Migrera aktuell version

Den aktuella versionen av Azure Migrate (släpptes i juli 2019) innehåller ett antal nya funktioner:

- **Enhetlig migreringsplattform:** Azure Migrate tillhandahåller nu en enda portal för att centralisera, hantera och spåra din migreringsresa till Azure, med ett förbättrat distributionsflöde och portalupplevelse.
- **Bedömnings- och migreringsverktyg:** Azure Migrate tillhandahåller inbyggda verktyg och integrerar med andra Azure-tjänster samt med oberoende ISV-verktyg (Software Vendor). [Läs mer](migrate-services-overview.md#isv-integration) om ISV-integrering.
- **Azure Migrate-utvärdering:** Med verktyget Utvärdering av Azure Migrate Server kan du bedöma virtuella datorer med VMware och virtuella virtuella datorer med hyper-V för migrering till Azure. Du kan också bedöma för migrering med andra Azure-tjänster och ISV-verktyg.
- **Azure Migrera migrering:** Med verktyget Azure Migrate Server Migration kan du migrera lokala virtuella datorer och virtuella virtuella hyper-virtuella datorer till Azure, samt fysiska servrar, andra virtualiserade servrar och privata/offentliga virtuella moln-datorer. Dessutom kan du migrera till Azure med ISV-verktyg.
- **Azure Migrate-installation:** Azure Migrate distribuerar en lättinstallation för identifiering och utvärdering av lokala virtuella datorer med VMware och virtuella hyper-virtuella datorer.
    - Den här installationen används av Azure Migrate Server Assessment och Azure Migrate Server Migration for agentless migration.
    - Apparaten identifierar kontinuerligt serverns metadata och prestandadata för bedömning och migrering.  
- **VMware VM-migrering:** Azure Migrera servermigrering innehåller ett par metoder för att migrera lokala virtuella virtuella datorer med VMware till Azure.  En agentlös migrering med Azure Migrate-enheten och en agentbaserad migrering som använder en replikeringsinstallation och distribuerar en agent på varje virtuell dator som du vill migrera. [Läs mer](server-migrate-overview.md)
 - **Databasutvärdering och migrering**: Från Azure Migrate kan du bedöma lokala databaser för migrering till Azure med hjälp av Azure Database Migration Assistant. Du kan migrera databaser med hjälp av Migreringstjänsten för Azure Database.
- **Webbappmigrering**: Du kan bedöma webbappar med hjälp av en offentlig slutpunkts-URL med Azure App Service. För migrering av interna .NET-appar kan du hämta och köra Migreringsassistenten för App Service.
- **Data box:** Importera stora mängder offlinedata till Azure med Hjälp av Azure Data Box i Azure Migrera.

## <a name="azure-migrate-previous-version"></a>Azure Migrera tidigare version

Om du använde den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella datorer med VMware stöddes), bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte längre skapa nya Azure Migrate-projekt eller utföra nya identifieringar. Du kan fortfarande komma åt befintliga projekt. Om du vill göra detta i Azure-portalen > **Alla tjänster**söker du efter **Azure Migrate**. I Azure Migrate-meddelanden finns det en länk för att komma åt gamla Azure Migrate-projekt.



## <a name="next-steps"></a>Nästa steg

- [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
- Prova våra tutorials för att bedöma virtuella datorer med [VMware](tutorial-assess-vmware.md) och [virtuella virtuella datorer med hyper-v.](tutorial-assess-hyper-v.md)
