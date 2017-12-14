---
title: Om Azure Migrate | Microsoft Docs
description: "Ger en översikt över tjänsten Azure Migrate."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b313bb4-c8f4-43ad-883c-789824add3288
ms.service: migrate
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: raynew
ms.openlocfilehash: 5c78f68c481b68cff31bdc5fd410549c2d44ba5a
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Azure Migrate-tjänsten utvärderar lokala arbetsbelastningar för migrering till Azure. Tjänsten utvärderar migreringslämplighet och prestandabaserad storleksanpassning, och tillhandahåller kostnadsuppskattningar för att köra dina lokala datorer i Azure. Om du överväger Lift and Shift-migreringar eller om du är i ett tidigt skede av migreringen är den här tjänsten något för dig. Efter utvärderingen kan du använda tjänster som Azure Site Recovery och Azure Database Migration för att migrera datorerna till Azure.

> [!NOTE]
> Azure Migrate finns för närvarande som förhandsversion och har stöd för produktionsarbetsbelastningar.

## <a name="why-use-azure-migrate"></a>Varför ska jag använda Azure Migrate?

Med Azure Migrate får du hjälp med att:

- **Utvärdera Azure-beredskap**: Utvärdera om dina lokala datorer är lämpliga att köra i Azure. 
- **Få storleksrekommendationer**: Få storleksrekommendationer för virtuella Azure-datorer baserat på lokala virtuella datorers prestandahistorik. 
- **Uppskatta månadskostnader**: Få uppskattade kostnader för att köra lokala virtuella datorer i Azure.  
- **Migrera med hög exakthet**: Visualisera beroenden för lokala datorer för att skapa grupper av datorer som utvärderas och migreras tillsammans. Du kan för närvarande visa beroenden för en specifik dator eller för alla datorer i en grupp.

## <a name="current-limitations"></a>Aktuella begränsningar

- För närvarande kan du utvärdera lokala virtuella VMware-datorer (VM) för migrering till virtuella Azure-datorer.

> [!NOTE]
> Stöd för Hyper-V finns med i planeringen och kommer att aktiveras om några månader. Under tiden kan rekommenderar vi att du använder Distributionshanteraren för Azure Site Recovery för att planera migrering av Hyper-V-arbetsbelastningar. 

- Du kan utvärdera upp till 1000 virtuella datorer med en enda utvärdering, och upp till 1500 datorer i ett enda Azure Migrate-projekt. Om du behöver utvärdera mer kan du öka antalet projekt eller utvärderingar. [Läs mer](how-to-scale-assessment.md).
- Virtuella datorer som du vill utvärdera måste hanteras av en vCenter Server, version 5.5, 6.0 eller 6.5.
- Du kan endast skapa ett Azure Migrate-projekt i regionen västra centrala USA. Men detta påverkar inte din möjlighet att planera migrering för en annan Azure-plats. Platsen för ett migreringsprojekt används endast för att lagra metadata som identifieras från den lokala miljön.
- Azure Migrate-portalen är för närvarande endast tillgänglig på engelska. 
- Azure Migrate stöder för närvarande endast replikering av [Lokalt redundant lagring (LRS)](../storage/common/storage-introduction.md#replication).

## <a name="what-do-i-need-to-pay-for"></a>Vad måste jag betala för?

Azure Migrate är tillgänglig utan extra kostnad. Under den allmänt tillgängliga förhandsversionen tillkommer avgifter för användning av funktioner för beroendevisualisering. För att stödja [beroendevisualisering](concepts-dependency-visualization.md) skapar Azure Migrate en Log Analytics-arbetsyta som standard. Om du använder beroendevisualisering, eller arbetsytan utanför Azure Migrate, debiteras du för användningen av arbetsytan. [Läs mer](https://azure.microsoft.com/en-us/pricing/details/insight-analytics/) om avgifterna. När tjänsten blir allmänt tillgänglig tillämpas inga avgifter för användning av beroendevisualisering.


## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

En utvärdering hjälper dig att identifiera Azure-lämpligheten för lokala virtuella datorer och få rätt storleksrekommendationer och kostnadsbedömningar för de virtuella datorer som körs i Azure. Utvärderingar baseras på egenskaperna som sammanfattas i tabellen nedan. Du kan ändra de här egenskaperna på Azure Migrate-portalen. 

**Egenskap** | **Detaljer**
--- | ---
**Målplats** | Azure-platsen du vill migrera till. Målplatsen är som standard angiven som USA, västra 2. 
**Lagringsredundans** | Den typ av lagring som de virtuella Azure-datorerna kommer att använda efter migreringen. LRS är standard.
**Prisavtal** | Utvärderingen tittar på om du har registrerats i Software Assurance och kan använda [Azure Hybrid-användningsförmånen](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Den tittar också på Azure-erbjudanden som bör tillämpas, och låter dig ange prenumerationsspecifika rabatter (%) som du får utöver erbjudandet. 
**prisnivå** | Du kan ange [prisnivå (basic/standard)](../virtual-machines/windows/sizes-general.md) för virtuella Azure-datorer. Detta hjälper dig att migrera till en lämplig Azure-familj med virtuella datorer, baserat på om du är i en produktionsmiljö eller inte. Som standard används [standardnivån](../virtual-machines/windows/sizes-general.md).
**Prestandahistorik** | Som standard utvärderar Azure Migrate prestanda för lokala datorer med en månadshistorik, med ett percentilvärde på 95 %. Du kan ändra den här inställningen.
**Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. Standardkomfortinställningen är 1,3x.


## <a name="how-does-azure-migrate-work"></a>Hur fungerar Azure Migrate?

1.  Du skapar ett Azure Migrate-projekt.
2.  Azure Migrate använder en lokal virtuell dator som kallas för insamlingsprogram för att upptäcka information om dina lokala datorer. För att skapa programmet laddar du ned en installationsfil i formatet Open Virtualization Appliance (.ova) och importerar den som en virtuell dator på din lokala vCenter-server.
3.  Du ansluter till den virtuella datorn med hjälp av konsolanslutningen i vCenter Server. Sedan anger du ett nytt lösenord för den virtuella datorn när du ansluter och kör insamlingsprogrammet i den virtuella datorn för att inleda identifieringen.
4.  Insamlaren samlar in VM-metadata med cmdletar för VMware PowerCLI. Identifierng är agentfri och installerar inte något på VMware-värdar eller virtuella datorer. Insamlade metadata innefattar VM-information (kärnor, minne, diskar, diskstorlekar och nätverksadaptrar). De samlar även in prestandadata för virtuella datorer, däribland CPU- och minnesanvändning, disk-IOPS, diskgenomflöde (Mbit/s) och nätverksutdata (Mbit/s).
5.  Metadata skickas till Azure Migrate-projektet. Du kan visa dem i Azure Portal.
6.  I utvärderingssyfte samlar du de identifierade virtuella datorerna i olika grupper. Du kan exempelvis gruppera virtuella datorer som kör samma program. Du kan gruppera virtuella datorer på Azure Migrate-portalen eller använda märkning i vCenter Server. Du kan dessutom använda visualisering av beroenden för att se beroenden för en specifik dator eller för alla datorer i en grupp och sedan ändra gruppen.
7.  När gruppen har bildats så skapar du en utvärdering för gruppen. 
8.  När utvärderingen är klar kan du visa den i portalen eller ladda ned den i Excel-format.



  ![Azure Planner-arkitektur](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Vilka är portkraven?

I tabellen sammanfattas de portar som behövs för Azure Migrate-kommunikation.

|Komponent          |För att kommunicera med     |Port som krävs  |Orsak   |
|-------------------|------------------------|---------------|---------|
|Insamlare          |Tjänsten Azure Migrate   |TCP 443        |Insamlaren ansluter till tjänsten via SSL-port 443|
|Insamlare          |vCenter Server          |Standard 9443   | Som standard ansluter insamlaren till vCenter-servern på port 9443. Om servrarna lyssnar på en annan port ska den konfigureras som en utgående port på VM-insamlaren. |
|Lokala virtuella datorer     | Operations Management Suite (OMS)-arbetsyta          |[TCP 443](../log-analytics/log-analytics-windows-agents.md#system-requirements-and-required-configuration) |MMA-agenten använder TCP 443 för att ansluta till Log Analytics. Du behöver bara den här porten om du använder funktionen beroendevisualisering och installerar MMA (Microsoft Monitoring Agent). |


  
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