---
title: Vad är nytt i Azure Migrate
description: Lär dig mer om nyheter och nya uppdateringar i tjänsten Azure Migrate.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: db52c8ce5e1b7e25d4af0c570189597e05b86588
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331873"
---
# <a name="whats-new-in-azure-migrate"></a>Vad är nytt i Azure Migrate

[Azure Migrate](migrate-services-overview.md) hjälper dig att identifiera, utvärdera och migrera lokala servrar, appar och data till Microsoft Azure molnet. I den här artikeln sammanfattas nya versioner och funktioner i Azure Migrate.

## <a name="update-june-2020"></a>Uppdatering (2020 juni)

Nya versioner av Azure Migrate-versioner.

### <a name="azure-public-cloud"></a>Offentligt Azure-moln

**Situationen*** | **Ladda ned** | **SHA256**
--- | --- | ---
Hyper-V (8,93 MB) | [Senaste version](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1
VMware (10,9 GB) | [Senaste version](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd
Fysisk (63,1 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0


### <a name="azure-government"></a>Azure Government

**Situationen*** | **Ladda ned** | **SHA256**
--- | --- | ---
Hyper-V (63,1 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3
VMware (63,1 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de
Fysisk (63,1 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1



## <a name="update-april-2020"></a>Uppdatering (april 2020)

Azure Migrate stöder distributioner i Azure Government. 

- Du kan identifiera och utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar.
- Du kan migrera virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar till Azure.
- För VMware-migrering kan du använda agent utan agent eller agent-baserad migrering. [Läs mer](server-migrate-overview.md).
- [Granska](migrate-support-matrix.md#supported-geographies-azure-government) de geografiska och regioner som stöds för Azure Government.
- [Agent-baserad beroende analys](concepts-dependency-visualization.md#agent-based-analysis) stöds inte i Azure Government.
- Funktioner som finns i för hands versionen stöds i Azure Government, särskilt för [agentbaserade beroende analyser](concepts-dependency-visualization.md#agentless-analysis)och [program identifiering](how-to-discover-applications.md).


## <a name="update-march-2020"></a>Uppdatering (mars 2020)

Det finns nu en skript-baserad installation för att konfigurera [Azure Migrate-enheten](migrate-appliance.md):

- Den skriptbaserade installationen är ett alternativ till. Installation av enheten för ägg (VMware)/VHD (Hyper-V).
- Det innehåller ett PowerShell-skript som kan användas för att konfigurera installationen av VMware/Hyper-V på en befintlig dator som kör Windows Server 2016.

## <a name="update-november-2019"></a>Uppdatering (november 2019)

Ett antal nya funktioner har lagts till i Azure Migrate:

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

## <a name="azure-migrate-current-version"></a>Azure Migrate aktuell version

Den aktuella versionen av Azure Migrate (lanserades i juli 2019) innehåller ett antal nya funktioner:

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

## <a name="azure-migrate-previous-version"></a>Azure Migrate föregående version

Om du använder den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöds) bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte längre skapa nya Azure Migrate projekt eller utföra nya identifieringar. Du kan fortfarande komma åt befintliga projekt. Om du vill göra detta i Azure Portal > **alla tjänster**söker du efter **Azure Migrate**. I Azure Migrate-meddelanden finns det en länk för att komma åt gamla Azure Migrate-projekt.



## <a name="next-steps"></a>Nästa steg

- [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
- Prova våra självstudier för att utvärdera virtuella [VMware-datorer](tutorial-assess-vmware.md) och [virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md).
