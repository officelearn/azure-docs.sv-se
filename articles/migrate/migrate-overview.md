---
title: Om Azure Migrate | Microsoft Docs
description: Ger en översikt över tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 05/03/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 869d426bc76d725cead9bedc73800a20531bb9ed
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Azure Migrate-tjänsten utvärderar lokala arbetsbelastningar för migrering till Azure. Tjänsten utvärderar lokala datorers migreringslämplighet, prestandabaserad storleksanpassning och tillhandahåller uppskattningar av vad det kostar att köra dina lokala datorer i Azure. Om du överväger Lift and Shift-migreringar eller om du är i ett tidigt skede av migreringen är den här tjänsten något för dig. Efter utvärderingen kan du använda tjänster som [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) och [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) för att migrera datorerna till Azure.

## <a name="why-use-azure-migrate"></a>Varför ska jag använda Azure Migrate?

Med Azure Migrate får du hjälp med att:

- **Utvärdera Azure-beredskap**: Utvärdera om dina lokala datorer är lämpliga att köra i Azure.
- **Få storleksrekommendationer**: Få storleksrekommendationer för virtuella Azure-datorer baserat på lokala virtuella datorers prestandahistorik.
- **Uppskatta månadskostnader**: Få uppskattade kostnader för att köra lokala virtuella datorer i Azure.  
- **Migrera med hög exakthet**: Visualisera beroenden för lokala datorer för att skapa grupper av datorer som utvärderas och migreras tillsammans.

## <a name="current-limitations"></a>Aktuella begränsningar

- För närvarande kan du endast utvärdera lokala virtuella VMware-datorer (VM) för migrering till virtuella Azure-datorer. De virtuella VMware-datorerna måste hanteras av en vCenter Server (version 5.5, 6.0 eller 6.5).
- Stöd för Hyper-V finns i vår översikt. Under tiden kan rekommendera att du planerar att migrera Hyper-V-arbetsbelastningar med [Distributionshanteraren för Azure Site Recovery](http://aka.ms/asr-dp-hyperv-doc).
- Du kan identifiera upp till 1 500 virtuella datorer i en enda identifiering och upp till 1 500 virtuella datorer i ett enda projekt. Dessutom kan du utvärdera upp till 1 500 virtuella datorer i en enda utvärdering.
- Du kan endast skapa ett Azure Migrate-projekt i regionen USA, västra eller USA, östra. Men detta påverkar inte din möjlighet att planera migrering för en annan Azure-plats. Platsen för ett migreringsprojekt används endast för att lagra metadata som identifieras från den lokala miljön.
- Azure Migrate stöder endast hanterade diskar för migreringsutvärdering.


## <a name="what-do-i-need-to-pay-for"></a>Vad måste jag betala för?

Mer information om priser för Azure Migrate finns [här](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/).


## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

En utvärdering hjälper dig att identifiera Azure-lämpligheten för lokala virtuella datorer och få rätt storleksrekommendationer och kostnadsbedömningar för de virtuella datorer som körs i Azure. Du kan anpassa utvärderingar utifrån dina behov genom att ändra egenskaperna för utvärderingen. Nedan visas de egenskaper som vägs in när du skapar en utvärdering.

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Azure-platsen du vill migrera till.<br/><br/>För närvarande stöder Azure Migrate 30 regioner, bland andra: Australien – östra, Australien – sydöstra, Brasilien – södra, Kanada – centrala, Kanada – östra, Indien – centrala, USA – centrala, Kina – östra, Kina – norra, Asien – östra (Asien och stillahavsområdet), USA – östra, Tyskland – centrala, Tyskland – nordöstra, USA – östra 2, Japan – östra, Japan – västra, Korea – centrala, Korea – södra, USA – norra centrala, Europa – norra, USA – södra centrala, Asien – sydost, Indien – södra, Storbritannien – södra, Storbritannien – västra, USA – US Gov Arizona – US Gov Texas – US Gov Virginia – västra centrala, USA – västra centrala, Europa – västra, Indien – västra, USA – västra och USA – västra 2. Målplatsen är som standard angiven som USA, västra 2.
**Lagringsredundans** | Den typ av [lagringsredundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy) som de virtuella Azure-datorerna kommer att använda efter migreringen. Standardvärdet är lokalt redundant lagring (LRS). Lägg märke till att Azure Migrate enbart stöder utvärderingar som baseras på hanterade diskar och att hanterade diskar endast stöder LRS. Därför finns för närvarande bara LRS-alternativet.
**Ändra storlek på kriterium** | Kriteriet som ska användas av Azure Migrate för att ställa in rätt storlek på virtuella datorer för Azure. Du kan storleksanpassa antingen baserat på *prestandahistorik* för de lokala virtuella datorerna eller storleksanpassa de virtuella datorerna *som lokala* för Azure utan att räkna in prestandahistorik. Standardvärdet är prestandabaserad storlek.
**Prisavtal** | För kostnadsberäkningar överväger en utvärdering om du har Software Assurance och om du är berättigad för [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Den tittar också på [Azure-erbjudanden](https://azure.microsoft.com/support/legal/offer-details/) som du är registrerad för, och låter dig ange prenumerationsspecifika rabatter (%) som du kan få utöver erbjudandet.
**prisnivå** | Du kan ange [prisnivå (Basic/Standard)](../virtual-machines/windows/sizes-general.md) för Azure-måldatorerna. Om du exempelvis planerar att migrera en produktionsmiljö bör du överväga standardnivån, som tillhandahåller virtuella datorer med låg svarstid men kan kosta mer. Om du å andra sidan har en miljö för utveckling och testning kanske du vill överväga Basic-nivån som har virtuella datorer med högre svarstider och lägre kostnader. Som standard används [standardnivån](../virtual-machines/windows/sizes-general.md).
**Prestandahistorik** | Som standard utvärderar Azure Migrate prestanda för lokala datorer med prestandahistoriken för den sista dagen, med ett percentilvärde på 95 %. Du kan ändra dessa värden i egenskaperna för utvärdering.
**VM-serie** | Du kan ange den VM-serie som du vill överväga vid storleksberäkningen. Om du till exempel har en produktionsmiljö som du inte planerar att migrera till A-serien av virtuella datorer i Azure, kan du utesluta A-serien från listan eller serien, så fastställs storleken endast baserat på de valda serierna.  
**Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. Standardkomfortinställningen är 1,3x.


## <a name="how-does-azure-migrate-work"></a>Hur fungerar Azure Migrate?

1.  Du skapar ett Azure Migrate-projekt.
2.  Azure Migrate använder en lokal virtuell dator som kallas för insamlingsprogram för att upptäcka information om dina lokala datorer. För att skapa programmet laddar du ned en installationsfil i formatet Open Virtualization Appliance (.ova) och importerar den som en virtuell dator på din lokala vCenter-server.
3.  Du ansluter till den virtuella datorn med hjälp av konsolanslutningen i vCenter Server. Sedan anger du ett nytt lösenord för den virtuella datorn när du ansluter och kör insamlingsprogrammet i den virtuella datorn för att inleda identifieringen.
4.  Insamlaren samlar in VM-metadata med cmdletar för VMware PowerCLI. Identifierng är agentfri och installerar inte något på VMware-värdar eller virtuella datorer. Insamlade metadata innefattar VM-information (kärnor, minne, diskar, diskstorlekar och nätverksadaptrar). De samlar även in prestandadata för virtuella datorer, däribland CPU- och minnesanvändning, disk-IOPS, diskgenomflöde (Mbit/s) och nätverksutdata (Mbit/s).
5.  Metadata skickas till Azure Migrate-projektet. Du kan visa dem i Azure Portal.
6.  I utvärderingssyfte samlar du de identifierade virtuella datorerna i olika grupper. Du kan exempelvis gruppera virtuella datorer som kör samma program. För mer exakt gruppering kan du använda visualisering av beroenden för att se beroenden för en specifik dator eller för alla datorer i en grupp och sedan ändra gruppen.
7.  När gruppen har bildats så skapar du en utvärdering för gruppen.
8.  När utvärderingen är klar kan du visa den i portalen eller ladda ned den i Excel-format.



  ![Azure Planner-arkitektur](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Vilka är portkraven?

I tabellen sammanfattas de portar som behövs för Azure Migrate-kommunikation.

|Komponent          |För att kommunicera med     |Port som krävs  |Orsak   |
|-------------------|------------------------|---------------|---------|
|Insamlare          |Tjänsten Azure Migrate   |TCP 443        |Insamlaren ansluter till tjänsten via SSL-port 443|
|Insamlare          |vCenter Server          |Standard 9443   | Som standard ansluter insamlaren till vCenter-servern på port 9443. Om servrarna lyssnar på en annan port ska den konfigureras som en utgående port på VM-insamlaren. |
|Lokala virtuella datorer     | Log Analytics Workspace          |[TCP 443](../log-analytics/log-analytics-windows-agent.md) |MMA-agenten använder TCP 443 för att ansluta till Log Analytics. Du behöver bara den här porten om du använder funktionen beroendevisualisering och installerar MMA (Microsoft Monitoring Agent). |



## <a name="what-happens-after-assessment"></a>Vad händer efter utvärderingen?

När du har utvärderat lokala datorer för migrering med tjänsten Azure Migrate kan du använda ett par verktyg för att utföra migreringen:

- **Azure Site Recovery**: Du kan använda Azure Site Recovery för att migrera till Azure enligt följande:
  - Förbered Azure-resurser, inklusive en Azure-prenumeration, Azure-nätverk och ett lagringskonto.
  - Förbered dina lokala VMware-servrar för migrering. Du ska kontrollera kraven för VMware-stöd för Site Recovery, förbereda VMware-servrarna för identifiering och förbereda för att installera Site Recovery-mobilitetstjänsten på virtuella datorer du vill migrera.
  - Ställ in migreringen. Du konfigurerar ett Recovery Services-valv, konfigurerar migreringsinställningar för källa och mål, konfigurerar en replikeringsprincip och aktiverar replikering. Du kan köra ett programåterställningstest för att kontrollera att migreringen av den virtuella datorn till Azure fungerar som den ska.
  - Köra en redundans för att migrera lokala datorer till Azure.
  - [Läs mer](../site-recovery/tutorial-migrate-on-premises-to-azure.md) i självstudierna för Site Recovery-migrering.

- **Azure Database Migration**: Om dina lokala datorer använder en databas som SQL Server, MySQL eller Oracle kan du använda Azure Database Migration Service för att migrera dem till Azure. [Läs mer](https://azure.microsoft.com/campaigns/database-migration/).



## <a name="next-steps"></a>Nästa steg
[Följ en självstudie](tutorial-assessment-vmware.md) för att skapa en utvärdering för en lokal VMware VM.
