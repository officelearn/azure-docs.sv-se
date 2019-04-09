---
title: Om Azure Migrate | Microsoft Docs
description: Ger en översikt över tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 04/04/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e0249535813c6b8d652775f68a696d8c25ead5a1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275461"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Azure Migrate-tjänsten utvärderar lokala arbetsbelastningar för migrering till Azure. Tjänsten utvärderar lokala datorers migreringslämplighet, utför prestandabaserad storleksanpassning och tillhandahåller uppskattningar av vad det kostar att köra lokala datorer i Azure. Om du överväger Lift and Shift-migreringar eller om du är i ett tidigt skede av migreringen är den här tjänsten något för dig. Efter utvärderingen kan du använda tjänster som [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) och [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) för att migrera datorerna till Azure.

## <a name="why-use-azure-migrate"></a>Varför ska jag använda Azure Migrate?

Med Azure Migrate får du hjälp med att:

- **Utvärdera Azure-beredskap**: Utvärdera om dina lokala datorer är lämpliga att köras i Azure.
- **Få storleksrekommendationer**: Få storleksrekommendationer för virtuella Azure-datorer, baserat på lokala virtuella datorers prestandahistorik.
- **Uppskatta månadskostnader**: Få uppskattade kostnader för att köra lokala virtuella datorer i Azure.  
- **Migrera med hög exakthet**: Visualisera beroenden i lokala datorer för att skapa grupper av datorer som utvärderas och migreras tillsammans.

## <a name="current-limitations"></a>Aktuella begränsningar

- Du kan endast utvärdera lokala virtuella VMware-datorer (VM) för migrering till virtuella Azure-datorer. De virtuella VMware-datorerna måste hanteras av en vCenter Server (version 5.5, 6.0, 6.5 eller 6.7).
- Stöd för Hyper-V är för närvarande i förhandsversion med produktionsstöd, om du vill försöka ut, måste du registrera dig [här.](https://aka.ms/migratefuture)
- För bedömning av fysiska servrar, kan du utnyttja våra [partner verktyg](https://azure.microsoft.com/migration/partners/).
- Du kan identifiera upp till 1 500 virtuella datorer i en enda identifiering och i ett enda projekt. Vi har en preview-versionen som gör att identifiering av upp till 10 000 virtuella VMware-datorer i ett enda projekt med hjälp av en enda enhet, om du är intresserad av att testar lösningen kan du registrera dig [här.](https://aka.ms/migratefuture)
- Om du vill identifiera en större miljö kan du dela identifieringen och skapa flera projekt. [Läs mer](how-to-scale-assessment.md). Azure Migrate stöder upp till 20 projekt per prenumeration.
- Azure Migrate stöder endast hanterade diskar för migreringsutvärdering.
-  Du kan endast skapa ett Azure Migrate-projekt i nedanstående områden. Men det begränsar inte din möjlighet att skapa utvärderingar för andra Azure-målplatser.

    **Geografi** | **Lagringsplats**
    --- | ---
    Azure Government | Virginia (USA-förvaltad region)
    Asien | Sydostasien eller Östasien
    Europa | Europa, norra eller Europa, västra
    USA | USA, östra eller USA, västra centrala

    Platsen som är associerad med migreringsprojektet används för att lagra metadata som identifieras från den lokala miljön. Metadata lagras i en av regionerna utifrån den geografiska plats som har angetts för migreringsprojektet. Om du använder beroendevisualisering genom att skapa en ny Log Analytics-arbetsyta skapas arbetsytan i samma region som projektet.
- Funktionen för beroendevisualisering är inte tillgänglig i Azure Government.


## <a name="what-do-i-need-to-pay-for"></a>Vad måste jag betala för?

[Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.


## <a name="whats-in-an-assessment"></a>Vad ingår i en utvärdering?

Utvärderingsinställningarna kan anpassas efter dina behov. Utvärderingsegenskaperna sammanfattas i tabellen nedan.

**Egenskap ** | **Information**
--- | ---
**Målplats** | Azure-platsen du vill migrera till.<br/><br/>Azure Migrate stöder för närvarande 33 regioner som målplatser för migrering. [Se regioner](https://azure.microsoft.com/global-infrastructure/services/). Målregionen är som standard angiven som USA, östra.
**Lagringstyp** | Typen av de hanterade diskar som du vill allokera för alla virtuella datorer som ingår i utvärderingen. Om storlekskriteriet är *som lokal storleksbestämning* kan du ange måldisktypen som antingen premium-diskar (standard), standard-SSD-diskar eller standardhårddiskar. För *prestandabaserad storleksbestämning* har du utöver alternativen ovan även möjligheten att välja Automatiskt, vilket ser till att rekommendationen för storleksbestämning sker automatiskt baserat på prestandadata för de virtuella datorerna. Om du till exempel vill uppnå ett [serviceavtal för enskild virtuell datorinstans på 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) kan du ange lagringstypen som hanterade Premium-diskar, vilket säkerställer att alla diskar i utvärderingen rekommenderas som hanterade Premium-diskar. Observera att Azure Migrate endast stöder hanterade diskar för migreringsutvärdering.
**Reserverade instanser** |  Om du har [reserverade instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Azure. Azure Migrate beräknar kostnaden enligt detta.
**Kriterium för storleksändring** | Storleken kan baseras på **prestandahistorik** för lokala virtuella datorer (standard) eller **som lokalt**, utan att ta hänsyn till prestandahistorik.
**Prestandahistorik** | Som standard utvärderar Azure Migrate prestanda för lokala datorer med prestandahistoriken för den sista dagen, med ett percentilvärde på 95 %.
**Komfortfaktor** | Azure Migrate överväger en buffert (komfortfaktor) under utvärderingen. Bufferten tillämpas utöver datorns användningsdata för virtuella datorer (CPU, minne, disk och nätverk). Komfortfaktorn väger in problem som säsongsbaserad användning, kort prestandahistorik och troliga ökningar i kommande användning.<br/><br/> Till exempel resulterar en virtuell dator med 10 kärnor med 20 % användning vanligen i en virtuell dator med 2 kärnor. Med en komfortfaktor på 2.0x blir resultatet istället en virtuell dator med 4 kärnor. Standardkomfortinställningen är 1,3x.
**Virtuell datorserie** | Den virtuell dator-serie som används för storleksuppskattningar. Om du till exempel har en produktionsmiljö som du inte planerar att migrera till A-seriens virtuella datorer i Azure kan du utesluta A-serien från listan eller serien. Storleken baseras bara på den valda serien.   
**Valuta** | Faktureringsvalutan. Standardvärdet är USD.
**Rabatt (%)** | Prenumerationsspecifika rabatter som du får utöver Azure-erbjudandet. Standardinställningen är 0%.
**VM-drifttid** | Om dina virtuella datorer inte längre kommer att köra dygnet runt i Azure kan du ange hur länge (antalet dagar per månad och antalet timmar per dag) de kommer att köras så sker kostnadsuppskattningen i enlighet med detta. Standardvärdet är 31 dagar per månad och 24 timmar per dag.
**Azure-erbjudande** | Det [Azure-erbjudande](https://azure.microsoft.com/support/legal/offer-details/) du har registrerat dig för. Azure Migrate beräknar kostnaden enligt detta.
**Azure Hybrid-förmån** | Om du har Software Assurance och är berättigad till [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-use-benefit/) med rabatterade kostnader.

## <a name="how-does-azure-migrate-work"></a>Hur fungerar Azure Migrate?

1. Du skapar ett Azure Migrate-projekt.
2. Azure Migrate använder en lokal virtuell dator som kallas för insamlingsprogram för att upptäcka information om dina lokala datorer. För att skapa programmet laddar du ned en installationsfil i formatet Open Virtualization Appliance (.ova) och importerar den som en virtuell dator på din lokala vCenter-server.
3. Du ansluter till den virtuella datorn via vCenter Server och anger ett nytt lösenord för den vid anslutning.
4. Du kör insamlaren på den virtuella datorn för att inleda identifieringen.
5. Insamlaren samlar in VM-metadata med cmdletar för VMware PowerCLI. Identifierng är agentfri och installerar inte något på VMware-värdar eller virtuella datorer. Insamlade metadata innefattar VM-information (kärnor, minne, diskar, diskstorlekar och nätverksadaptrar). De samlar även in prestandadata för virtuella datorer, däribland CPU- och minnesanvändning, disk-IOPS, diskgenomflöde (Mbit/s) och nätverksutdata (Mbit/s).
5. Metadata skickas till Azure Migrate-projektet. Du kan visa dem i Azure Portal.
6. I utvärderingssyfte samlar du de identifierade virtuella datorerna i olika grupper. Du kan exempelvis gruppera virtuella datorer som kör samma program. För mer exakt gruppering kan du använda visualisering av beroenden för att se beroenden för en specifik dator eller för alla datorer i en grupp och sedan ändra gruppen.
7. När en grupp har definierats kan skapa du en utvärdering för den.
8. När utvärderingen är klar kan du visa den i portalen eller ladda ned den i Excel-format.

   ![Azure Migrate-arkitektur](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Vilka är portkraven?

I tabellen sammanfattas de portar som behövs för Azure Migrate-kommunikation.

| Komponent | Kommunicerar med |  Information |
| --- | --- |--- |
|Insamlare  | Tjänsten Azure Migrate | Insamlaren ansluter till tjänsten via SSL-port 443.|
|Insamlare | vCenter Server | Som standard ansluter insamlaren till vCenter-servern på port 443. Om servrarna lyssnar på en annan port konfigurerar du den som en utgående port på VM-insamlaren.|
|Lokala virtuella datorer | Log Analytics Workspace | [Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) använder TCP-port 443 för att ansluta till Azure Monitor-loggar. Du behöver bara den här porten om du använder beroendevisualisering, som kräver MMA-agenten.|


## <a name="what-happens-after-assessment"></a>Vad händer efter utvärderingen?

När du har utvärderat lokala datorer kan du använda några olika verktyg för att utföra migreringen:

- **Azure Site Recovery**: Du kan använda Azure Site Recovery till att migrera till Azure. Det kan du göra genom att [förbereda Azure-komponenterna](../site-recovery/tutorial-prepare-azure.md) du behöver, bland annat ett lagringskonto och ett virtuellt nätverk. Lokalt [förbereder du VMware-miljön](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md). När allt är förberett konfigurerar och aktiverar du replikering till Azure och migrerar de virtuella datorerna. [Läs mer](../site-recovery/vmware-azure-tutorial.md).
- **Azure Database Migration**: Om de lokala datorerna använder en databas som SQL Server, MySQL eller Oracle, kan du använda [Azure Database Migration Service](../dms/dms-overview.md) till att migrera dem till Azure.

## <a name="want-to-learn-more-from-community-experts"></a>Om du vill veta mer från experter?
Gå till den [Azure Migrate MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) eller [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.  
Om du har frågor eller behöver hjälp med att skapa en [supportförfrågan](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Om din supportbegäran kräver djupgående teknisk vägledning kan du besöka [supportavtal för Azure](https://azure.microsoft.com/support/plans/)     


## <a name="next-steps"></a>Nästa steg

- [Följ självstudien](tutorial-assessment-vmware.md) för att skapa en utvärdering för en lokal VMware VM.
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
