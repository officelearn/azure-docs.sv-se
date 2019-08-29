---
title: Azure Migrate – vanliga frågor och svar (FAQ) | Microsoft Docs
description: Korrigerar vanliga frågor om Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: af95ad892b62cb5d8bece554d6026525d9279777
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102877"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate: Vanliga frågor och svar (FAQ)

I den här artikeln får du svar på vanliga frågor om Azure Migrate. Om du har fler frågor efter att ha läst den här artikeln kan du publicera dem i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Allmänt

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Vilka Azure-geografiska områden Azure Migrate stöd för?

Se [listan för VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) och [listan för Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Vad är skillnaden mellan Azure Migrate och Azure Site Recovery?

Azure Migrate tillhandahåller en central hubb för att starta migreringen, köra och spåra identifiering och utvärdering av datorer och arbets belastningar, samt utföra och spåra migrering av datorer och arbets belastningar till Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) är en katastrof återställnings lösning. Azure Migrate Server-migreringen använder Azure Site Recovery på Server delen för att aktivera migrering för att migrera lokala datorer.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrates utrustning (VMware/fysiska servrar)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Hur ansluter Azure Migrate-enheten till Azure?

Anslutningen kan vara över Internet, eller så kan du använda Azure ExpressRoute med offentlig/Microsoft-peering.

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Vilka är kraven på nätverks anslutning för utvärdering och migrering av Azure Migrate Server?

Se matriser för [VMware](migrate-support-matrix-vmware.md) [-och Hyper-V-](migrate-support-matrix-hyper-v.md) support för information om de URL: er och portar som behövs för att aktivera Azure Migrate att kommunicera med Azure.

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>Kan jag skärpa den virtuella dator som jag har skapat med mallen?

Du kan lägga till komponenter (till exempel Antivirus) till mallen, förutsatt att du lämnar de kommunikations-och brand Väggs regler som krävs för Azure Migrate-installationen.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Vilka data samlas in av Azure Migrates apparaten?

Du kan visa information om de data som samlats in av Azure Migrate-installationen [i Azure Migrate-dokumentationen](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Påverkas prestandan i den analyserade VMware-eller Hyper-V-miljön?

Azure Migrate utrustnings profiler lokalt för att mäta prestanda data för virtuella datorer kontinuerligt. Den här profileringen har nästan ingen prestanda påverkan på Hyper-V/ESXi-värdar eller på vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Var lagras insamlade data och hur länge?

De data som samlas in av Azure Migrate-installationen lagras på den Azure-plats som du väljer när du skapar migreringsjobbet. Data lagras säkert i en Microsoft-prenumeration och tas bort när du tar bort Azure Migrate-projektet.

Om du installerar agenter på de virtuella datorerna lagras data som samlas in av beroende agenter i USA, i en Log Analytics arbets yta som skapats i Azure-prenumerationen. Dessa data tas bort när du tar bort Log Analytics arbets ytan i din prenumeration. Mer information finns i [beroende visualisering](concepts-dependency-visualization.md).

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>Vilken data volym överförs av Azure Migrates apparaten under kontinuerlig profilering?

Mängden data som skickas till Azure Migrate varierar beroende på flera parametrar. För att ge dig en uppfattning av volymen: ett Azure Migrate projekt med 10 datorer (var och en med en disk och ett nätverkskort) skickar cirka 50 MB per dag. Det här värdet är en uppskattning. Den ändras baserat på antalet data punkter för nätverkskorten och diskarna. (Ökningen av data som skickas är inte linjära om antalet datorer, nätverkskort eller diskar ökar.)

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Är data krypterade i vila och under överföring?

Ja, båda. Metadata skickas säkert till Azure Migrate tjänsten via Internet via HTTPS. Metadata lagras i en [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) -databas och i [Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) i en Microsoft-prenumeration. Metadata är krypterade i vila.

Data som samlas in av beroende agenter krypteras också vid överföring (säker HTTPS). Den lagras i en Log Analytics arbets yta i din prenumeration. Det är också krypterat i vila.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Hur kommunicerar Azure Migrate-enheten med vCenter Server och Azure Migrate tjänsten?

Enheten ansluter till vCenter Server (port 443) genom att använda de autentiseringsuppgifter du angav när du konfigurerade installationen. Den använder VMware PowerCLI för att fråga vCenter Server för att samla in metadata om de virtuella datorer som hanteras av vCenter Server. Den samlar in konfigurations data om virtuella datorer (kärnor, minne, diskar, nätverkskort osv.) och även prestanda historiken för varje virtuell dator under den senaste månaden. De insamlade metadata skickas sedan till Azure Migrate Server utvärdering (via Internet via HTTPS) för utvärdering.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Kan jag ansluta samma installation till flera vCenter Server instanser?

Nej. Det finns en en-till-en-mappning mellan en installation och vCenter Server. Om du måste identifiera virtuella datorer på flera vCenter Server-instanser måste du distribuera flera enheter.


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>Jag har ändrat min dator storlek. Kan jag köra utvärderingen igen?

Azure Migrates enheten samlar kontinuerligt in information om den lokala miljön. Men en utvärdering är en tidpunkts ögonblicks bild av lokala virtuella datorer. Om du ändrar inställningarna för en virtuell dator som du vill utvärdera använder du alternativet beräkna om du vill uppdatera utvärderingen med de senaste ändringarna.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>Hur kan jag utföra identifiering i en miljö med flera innehavare i Azure Migrate Server utvärdering?

Om du har en miljö som delas mellan klienter för VMware och du inte vill identifiera de virtuella datorerna för en klient i en annan klient organisations prenumeration skapar du vCenter Server autentiseringsuppgifter som bara kan komma åt de virtuella datorer som du vill identifiera. Använd sedan de här autentiseringsuppgifterna när du startar identifiering i Azure Migrates enheten.

För Hyper-V använder identifieringen autentiseringsuppgifter för Hyper-V-värden. Om virtuella datorer delar samma Hyper-V-värd finns det för närvarande inget sätt att separera identifieringen.  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>Hur många virtuella datorer kan jag identifiera med en enda migrations enhet?

Du kan identifiera upp till 10 000 virtuella VMware-datorer och upp till 5 000 virtuella Hyper-V-datorer med en enda migreringstjänster. Om du har fler datorer i din lokala miljö kan du läsa om hur du skalar [Hyper-V-](scale-hyper-v-assessment.md) och [VMware](scale-vmware-assessment.md) -utvärderingen.

## <a name="azure-migrate-server-assessment"></a>Utvärdering av Azure Migrate Server

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Stöder Azure Migrate Server utvärdering en utvärdering av fysiska servrar?

Nej, Azure Migrate stöder för närvarande inte utvärdering av fysiska servrar.

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Behöver Azure Migrate vCenter Server för att kunna identifiera i en VMware-miljö?

Ja, Azure Migrate behöver vCenter Server för att utföra identifiering i en VMware-miljö. Den stöder inte identifiering av ESXi-värdar som inte hanteras av vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Vad är skillnaden mellan Azure Migrate Server utvärdering och kart verktygen?

Azure Migrate Server Assessment tillhandahåller migrerings bedömning för att hjälpa till med migreringen och utvärdering av arbets belastningar för migrering till Azure. [Verktyget Microsoft Assessment and Planning (kart)](https://www.microsoft.com/download/details.aspx?id=7826) hjälper till med andra uppgifter, t. ex. migrerings planering för nyare versioner av Windows-klient-och serveroperativ system och program varu användnings spårning. Fortsätt att använda kart verktygen för dessa scenarier.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Vad är skillnaden mellan Azure Migrate Server bedömning och Distributionshanteraren för Azure Site Recovery?

Azure Migrate Server-utvärdering är ett verktyg för migrerings planering. Distributionshanteraren för Azure Site Recovery är ett planerings verktyg för haveri beredskap.

**Migrering från VMware/Hyper-V till Azure**: Om du planerar att migrera dina lokala servrar till Azure kan du använda Azure Migrate Server-utvärdering för planering av migrering. Den bedömer lokala arbets belastningar och ger vägledning, insikter och verktyg som hjälper dig att migrera dina servrar till Azure. När du är klar med din migrering kan du använda verktyg som Azure Migrate Server-migrering för att migrera datorerna till Azure.

**Haveri beredskap från VMware/Hyper-V till Azure**: För haveri beredskap till Azure via Site Recovery använder du Site Recovery Deployment Planner för planering. Site Recovery Deployment Planner gör en djup, Site Recovery-begränsad utvärdering av din lokala miljö. Den ger rekommendationer som krävs av Site Recovery för lyckade haveri åtgärder som replikering och redundans av virtuella datorer.

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Stöder Azure Migrate kostnads uppskattning för programmet Enterprise-avtal (EA)?

Azure Migrate för närvarande inte stöd för kostnads uppskattning för [Enterprise-avtal programmet](https://azure.microsoft.com/offers/enterprise-agreement-support/). Lösningen är att ange att **betala per** användning som **erbjudande** och manuellt ange rabatt procenten (gäller för prenumerationen) i **rabatt** rutan för bedömnings egenskaperna:

  ![Utvärderingsegenskaper](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Vad är skillnaden mellan lokal storlek och prestandabaserade storleks ändringar?

Vid samma storlek som lokal storlek kan Azure Migrate inte beakta prestanda data för virtuella datorer. De virtuella datorerna anpassas efter den lokala konfigurationen. Med prestanda-baserad storleks ändring baseras storleken på användnings data.

Överväg till exempel en lokal virtuell dator som har 4 kärnor och 8 GB minne med 50% processor användning och 50% minnes användning. För den här virtuella datorn rekommenderar den lokala storleken på en Azure VM-SKU som har fyra kärnor och 8 GB minne. Prestanda-baserad storlek rekommenderar en VM-SKU som har två kärnor och 4 GB minne, eftersom användnings procenten beaktas.

På samma sätt beror disk storleken på två bedömnings egenskaper: storleks kriterier och lagrings typ. Om storleks kriteriet är prestanda beroende och lagrings typen är automatisk, tar Azure Migrate ta hänsyn till diskens IOPS-och data flödes värden när den identifierar mål disk typ (standard eller Premium).

Om storleks kriteriet är prestanda beroende och lagrings typen är Premium rekommenderar Azure Migrate en Premium-disk. Premium disk-SKU: n väljs utifrån storleken på den lokala disken. Samma logik används för att göra disk storlek när storleks kriteriet är i lokal storlek och lagrings typen är standard eller Premium.

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>Vilken effekt påverkar prestanda historiken och användningen av percentilen storleks rekommendationer?

Dessa egenskaper gäller endast för prestandabaserad storleksändring.

Azure Migrate samlar in prestanda historiken för lokala datorer och använder den för att rekommendera den virtuella dator storleken och disk typen i Azure.

Enheten registrerar kontinuerligt den lokala miljön för att samla in användnings data i real tid var 20: e sekund. Enheten samlar in de 20 sekunderna exemplen och skapar en enda data punkt för var 15: e minut. För att skapa data punkten väljer installationen det högsta värdet från alla exempel på 20 sekunder. Enheten skickar data punkten till Azure.

När du skapar en utvärdering i Azure (baserat på percentilvärdet för prestanda tid och prestanda historik) beräknar Azure Migrate det effektiva användning svärdet och använder det för storleks ändring.

Anta till exempel att du ställer in varaktigheten för prestanda på en dag och percentilvärdet till 95 percentilen. Azure Migrate sorterar de 15 minuters exempel punkter som skickats av insamlaren under den senaste dagen i stigande ordning och väljer det 95: e percentil svärdet som effektiv användning.

95 percentilvärdet ser till att du ignorerar eventuella avvikande Mät värden. Avvikare kan inkluderas om du använder 99-percentilen. Om du vill välja högsta användnings nivå för perioden och inte vill missa avvikande värden väljer du 99 percentilen.

### <a name="what-is-dependency-visualization"></a>Vad är beroende visualisering?

Beroende visualisering gör det möjligt att utvärdera grupper av virtuella datorer för migrering med större tillförlitlighet. Den kontrollerar dator beroenden innan du kör en utvärdering. Beroende visualisering hjälper till att se till att ingenting är kvar bakom för att undvika oväntade avbrott när du migrerar till Azure. Azure Migrate använder Tjänstkarta-lösningen i Azure Monitor loggar för att aktivera beroende visualisering.

> [!NOTE]
> Beroende visualiseringen är inte tillgänglig i Azure Government.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Måste jag betala för att använda beroende visualisering?

Nej. Mer information finns i [Azure Migrate prissättning](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Behöver jag installera något för beroende visualisering?

Om du vill använda beroende visualisering måste du ladda ned och installera agenter på varje lokal dator som du vill utvärdera.

Du måste installera följande agenter på varje dator:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Beroende agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Om du har datorer som saknar Internet anslutning måste du ladda ned och installera Log Analytics gateway på dem.

Du behöver inte dessa agenter om du inte använder beroende visualisering.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan jag använda en befintlig arbets yta för beroende visualisering?

Ja, du kan koppla en befintlig arbets yta till migreringsjobbet och använda den för beroende visualisering. Mer information finns i "Hur fungerar det" i artikeln om [beroende visualisering](concepts-dependency-visualization.md#how-does-it-work) .

### <a name="can-i-export-the-dependency-visualization-report"></a>Kan jag exportera rapporten för beroende visualisering?

Nej, det går inte att exportera beroende visualiseringen. Men eftersom Azure Migrate använder Tjänstkarta för visualisering av beroenden kan du använda [tjänstkarta-REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) för att hämta beroenden i JSON-format.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Hur kan jag automatisera installationen av Microsoft Monitoring Agent (MMA) och beroende agenten?

Använd det här [skriptet för att installera agenterna](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples). Följ de här [anvisningarna för att installera MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) med hjälp av kommando raden eller Automation. Använd [det här skriptet](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)för MMA.

Förutom skript kan du också använda distributions verktyg som System Center Configuration Manager och [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) för att distribuera agenterna.

### <a name="what-operating-systems-are-supported-by-mma"></a>Vilka operativ system stöds av MMA?

- Visa listan över [Windows-operativsystem som stöds av MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Visa listan över [Linux-operativsystem som stöds av MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>Vilka operativ system stöds av beroende agenten?

- Visa listan över [Windows-operativsystem som stöds av beroende agenten](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
- Visa listan över [Linux-operativsystem som stöds av beroende agenten](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Kan jag visualisera beroenden i Azure Migrate i mer än en timme?
Nej. Du kan visualisera beroenden i upp till en timme. Du kan gå tillbaka till ett visst datum i historiken, så långt tillbaka som en månad, men den maximala varaktigheten för visualiseringen är en timme. Du kan till exempel använda varaktigheten för tiden i beroende kartan för att Visa beroenden för igår, men du kan bara visa det för ett Tim fönster. Du kan dock använda Azure Monitor loggar för att [fråga beroende data](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre tid.

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>Kan jag använda beroende visualisering för grupper som innehåller fler än 10 virtuella datorer?
Du kan [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) som innehåller upp till 10 virtuella datorer. Om du har en grupp med fler än 10 virtuella datorer rekommenderar vi att du delar upp gruppen i mindre grupper och sedan visualiserar beroendena.

## <a name="azure-migrate-server-migration"></a>Migrering av Azure Migrate Server

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Vad är skillnaden mellan migrering av Azure Migrate Server och Azure Site Recovery?

Azure Migrate Server-migrering använder Site Recovery-replikeringstjänsten för att migrera virtuella VMware-datorer, migrering av virtuella Hyper-V-datorer och migrering av fysiska servrar till Azure. Alternativet utan agent för migrering av virtuella VMware-datorer är inbyggt i Server migrering.

## <a name="next-steps"></a>Nästa steg
Läs [Azure Migrate översikt](migrate-services-overview.md).
