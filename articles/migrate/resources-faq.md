---
title: Azure Migrate – vanliga frågor och svar (FAQ) | Microsoft Docs
description: Korrigerar vanliga frågor om Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: bf591306220b2c8c1e6c8166686836d96432fc7d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856266"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Migrate

Den här artikeln innehåller vanliga frågor och svar om Azure Migrate. Om du har fler frågor när du har läst den här artikeln kan du publicera dem i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Allmänt

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Vilka Azure-geografiska diagram stöds av Azure Migrate?

Du kan hitta listan för [VMware här](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) och för [Hyper-V här](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Hur skiljer sig Azure Migrate från Azure Site Recovery?

Azure Migrate tillhandahåller en central hubb för att starta, köra och spåra identifiering, utvärdering och migrering av datorer och arbets belastningar till Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) är en katastrof återställnings lösning. Azure Migrate Server-migreringen utnyttjar Azure Site Recovery i Server delen för att aktivera migreringsåtgärder för migrering av lokala datorer.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrates utrustning (VMware/fysiska servrar)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Hur ansluter Azure Migrate-enheten till Azure?

Anslutningen kan vara över Internet eller så kan du använda ExpressRoute med offentlig/Microsoft-peering.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Vilka krav för nätverks anslutning som krävs för Azure Migrate Server bedömning och migrering

För de URL: er och portar som krävs för att Azure Migrate ska kunna kommunicera med Azure, granska [VMware](migrate-support-matrix-vmware.md) [-och Hyper-V-](migrate-support-matrix-hyper-v.md) Supportens matriser.

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>Kan jag skärpa den virtuella dator som jag har skapat med mallen?

Ytterligare komponenter (till exempel Antivirus) kan läggas till i mallen, så länge som de kommunikations-och brand Väggs regler som krävs för Azure Migrate-installationen är kvar.   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Vilka data samlas in av Azure Migrate utrustning?

Du kan information om de data som samlas in av Azure Migrate-installationen [här](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware).

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>Påverkas prestandan i den analyserade VMware-eller Hyper-V-miljön?

Med kontinuerlig profilering av prestanda data kan Azure Migrates utrustnings profiler lokala datorer för att mäta prestanda data för virtuella datorer. Detta har nästan noll prestanda påverkan på Hyper-V/ESXi-värdar, samt på vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Var lagras insamlade data och hur länge?

De data som samlas in av Azure Migrate-installationen lagras på den Azure-plats som du anger när du skapar migreringsjobbet. Data lagras säkert i en Microsoft-prenumeration och tas bort när du tar bort Azure Migrate-projektet.

Om du installerar agenter på de virtuella datorerna lagras data som samlas in av beroende agenter i USA, i en Log Analytics arbets yta som skapats i Azure-prenumerationen. Dessa data tas bort när du tar bort Log Analytics arbets ytan i din prenumeration. [Läs mer](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>Vilken volym av data laddas upp av Azure Migrates utrustning under kontinuerlig profilering?

Mängden data som skickas till Azure Migrate varierar beroende på flera parametrar. Ett Azure Migrate projekt med 10 datorer (var och en med en disk och ett nätverkskort) skickas runt 50 MB per dag för att ge ett preliminärt nummer. Detta är ett ungefärligt värde som ändras baserat på antalet data punkter för nätverkskorten och diskarna (data som skickas är icke-linjära om antalet datorer, nätverkskort eller diskar ökar).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Är data krypterade i vila och under överföring?

Ja för båda. Metadata skickas säkert till Azure Migrate tjänsten via Internet via https. Metadata lagras i ett [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)och i [Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) i en Microsoft-prenumeration och är krypterat i vila.

Data som samlas in av beroende agenter krypteras också vid överföring (säker HTTPS) och lagras i en Log Analytics arbets yta i användar prenumerationen. Det är också krypterat i vila.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Hur kommunicerar Azure Migrate-utrustningen med vCenter Server och Azure Migrate tjänsten?

Enheten ansluter till vCenter Server (port 443) med hjälp av de autentiseringsuppgifter som angavs när du konfigurerade enheten. Den frågar vCenter Server som använder VMware PowerCLI för att samla in metadata om de virtuella datorer som hanteras av vCenter Server. Den samlar in både konfigurations data om virtuella datorer (kärnor, minne, diskar, NIC osv.), samt prestanda historiken för varje virtuell dator under den senaste månaden. De insamlade metadata skickas sedan till den Azure Migrate Server utvärderingen (via Internet via HTTPS) för utvärdering.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Kan jag ansluta samma installation till flera vCenter-servrar?

Nej. Det finns en en-till-en-mappning mellan en installation och vCenter Server. Om du måste identifiera virtuella datorer på flera vCenter-servrar måste du distribuera flera enheter. 


### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Jag har ändrat min dator storlek. Kan jag köra en utvärdering igen?

Azure Migrates enheten samlar kontinuerligt in information om den lokala miljön. En utvärdering är dock en tidpunkts ögonblicks bild av lokala virtuella datorer. Om du ändrar inställningarna för en virtuell dator som du vill utvärdera använder du alternativet beräkna om för att uppdatera utvärderingen med de senaste ändringarna.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>Hur kan jag identifiera en miljö för flera innehavare i Azure Migrate Server-utvärdering?

Om du har en miljö som delas mellan klienter för VMware och du inte vill identifiera de virtuella datorerna för en klient i en annan klient organisations prenumeration skapar du vCenter Server autentiseringsuppgifter med enbart åtkomst till de virtuella datorer som du vill identifiera. Använd sedan autentiseringsuppgifterna för att starta identifieringen i Azure Migrates enheten.

För Hyper-V använder identifieringen autentiseringsuppgifter för Hyper-V-värden, om de virtuella datorerna delar samma Hyper-V-värd, finns det för närvarande inget sätt att separera identifieringen.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Hur många virtuella datorer kan identifieras med hjälp av en enda Migreringsverktyg?

Du kan identifiera upp till 10 000 virtuella VMware-datorer och upp till 5 000 virtuella Hyper-V-datorer med en enda migreringstjänster.  Om du har fler datorer i din lokala miljö kan du läsa om hur du skalar [Hyper-V-](scale-hyper-v-assessment.md) och [VMware](scale-vmware-assessment.md) -utvärderingen.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: Stöd för utvärdering av Server utvärdering av fysiska servrar?

Nej, Azure Migrate för närvarande inte stöder utvärdering av fysiska servrar.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Behöver Azure Migrate vCenter Server för att identifiera en VMware-miljö?

Ja, Azure Migrate behöver vCenter Server för att identifiera en VMware-miljö. Den stöder inte identifiering av ESXi-värdar som inte hanteras av vCenter Server.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Vad är skillnaden mellan att använda Azure Migrate: Server utvärdering och kart verktyg?

Azure Migrate: Server utvärderingen tillhandahåller migrerings bedömning för att hjälpa till med migreringen och utvärdering av arbets belastningar för migrering till Azure. [Microsoft Assessment and Planning Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) har andra funktioner, t. ex. migrerings planering för nyare versioner av Windows-klient-och serveroperativ system, samt spårning av program varu användning. Fortsätt att använda kart verktygen för dessa scenarier.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Hur Azure Migrate: Server utvärdering skiljer sig från Distributionshanteraren för Azure Site Recovery?

Azure Migrate: Server utvärdering är ett verktyg för migrerings planering. Distributionshanteraren för Azure Site Recovery är ett planerings verktyg för haveri beredskap.

- **Migrering från VMware/Hyper-V till Azure**: Om du vill migrera dina lokala servrar till Azure använder du Azure Migrate: Verktyg för Server utvärdering för migrering av migrering. Verktyget bedömer lokala arbets belastningar och ger vägledning, insikter och mekanismer som hjälper dig att migrera till Azure. När du är klar med din migrering kan du använda verktyg som Azure Migrate: Server-migrering för att migrera datorerna till Azure.
- **Haveri beredskap från VMware/Hyper-V till Azure**: För haveri beredskap till Azure med hjälp av Site Recovery använder du planeringen för Site Recovery distribution för haveri beredskap. Site Recovery Deployment Planner gör en djup, Site Recovery-begränsad utvärdering av din lokala miljö. Den ger rekommendationer som krävs av Site Recovery för lyckade haveri åtgärder, till exempel replikering och redundansväxling av virtuella datorer.

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Stöder Azure Migrate Enterprise-avtal (EA)-baserad kostnads uppskattning?

Azure Migrate för närvarande inte har stöd för kostnads uppskattning för [Enterprise-avtal erbjudande](https://azure.microsoft.com/offers/enterprise-agreement-support/). Lösningen är att ange att betala per användning som erbjudande och manuellt ange rabatt procenten (gäller för prenumerationen) i fältet rabatt i utvärderings egenskaperna.

  ![Rabatt](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Vad är skillnaden mellan lokal storlek och prestandabaserade storleks ändringar?

- I som lokal storlek kan Azure Migrate inte beakta prestanda data för virtuella datorer. De virtuella datorerna anpassas efter den lokala konfigurationen. I prestandabaserade storleks ändringar baseras storleken på användnings data.
- Om till exempel en lokal virtuell dator har 4 kärnor och 8 GB minne med 50% processor användning och 50% minnes användning, som lokal storlek, rekommenderar en virtuell Azure VM-SKU med fyra kärnor och 8 GB minne. Prestandabaserade storlekar rekommenderar dock en VM-SKU på två kärnor och 4 GB, eftersom användnings procenten beaktas.
- På samma sätt är disk storleken beroende av två bedömnings egenskaper – storleks ändrings kriterium och lagrings typ.
= Om storleks kriteriet är prestanda-baserat och lagrings typen är automatisk beaktas diskens IOPS-och data flödes värden när mål disk typen identifieras (standard eller Premium).
- Om storleks kriteriet är prestanda baserat och lagrings typen är Premium rekommenderas en Premium disk. Premium disk-SKU: n väljs utifrån storleken på den lokala disken. Samma logik används för att göra disk storlekarna när storleks kriteriet är i storlek, och lagrings typen är standard eller Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Vilken effekt påverkar prestanda historik och percentils användning på storleks rekommendationerna?

Dessa egenskaper gäller endast för prestandabaserad storleksändring.

- Azure Migrate samlar in prestanda historik för lokala datorer och använder den för att rekommendera den virtuella dator storleken och disk typen i Azure.
- Enheten registrerar kontinuerligt den lokala miljön för att samla in användnings data i real tid var 20: e sekund. Programmet samlar in stickprov var 20:e sekund och skapar en enskild datapunkt för varje kvart. För att skapa den enskilda datapunkten väljer programmet det högsta värdet från alla 20-sekundersstickprov och skickar det till Azure.
- När du skapar en utvärdering i Azure (baserat på percentilvärdet för prestanda tid och prestanda historik), beräknar Azure Migrate det effektiva användning svärdet och använder det för storleks ändring.
- Om du till exempel ställer in varaktigheten för varaktigheten på en dag och percentilvärdet till 95 percentilen använder Azure Migrate 15 minuters exempel punkter som skickats av insamlaren för den sista dagen, sorterar dem i stigande ordning och plockar värdet 95 percentilvärdet som effektiv användning.
- 95 percentil-värdet garanterar att du ignorerar eventuella avvikande värden som kan uppstå om du använder 99-percentilen. Om du vill välja den högsta användningen för perioden och inte vill missa några avvikande värden bör du välja den 99:e percentilen.

### <a name="what-is-dependency-visualization"></a>Vad är beroende visualisering?

Beroende visualisering gör det möjligt att utvärdera grupper av virtuella datorer för migrering med större tillförlitlighet. Den kontrollerar dator beroenden innan du kör en utvärdering. Beroende visualisering hjälper till att se till att ingenting är kvar bakom och Undvik oväntade avbrott när du migrerar till Azure. Azure Migrate utnyttjar Tjänstkarta-lösningen i Azure Monitor loggar för att aktivera beroende visualisering.

> [!NOTE]
> Funktionen för beroendevisualisering är inte tillgänglig i Azure Government.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Måste jag betala för att använda funktionen för beroende visualisering?

Nej. [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Behöver jag installera något för beroende visualisering?

Om du vill använda beroende visualisering måste du ladda ned och installera agenter på varje lokal dator som du vill utvärdera.

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) måste installeras på varje dator.
- [Beroende agenten](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) måste installeras på varje dator.
- Om du dessutom har datorer som saknar Internet anslutning måste du ladda ned och installera Log Analytics gateway på dem.

Du behöver inte dessa agenter om du inte använder beroende visualisering.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan jag använda en befintlig arbets yta för beroende visualisering?

Ja, du kan koppla en befintlig arbets yta till migreringsjobbet och utnyttja den för beroende visualisering. [Läs mer](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Kan jag exportera rapporten för beroende visualisering?

Nej, det går inte att exportera beroende visualiseringen. Men eftersom Azure Migrate använder Tjänstkarta för visualisering av beroenden kan du använda [TJÄNSTKARTA REST-API: er](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) för att hämta beroenden i ett JSON-format.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Hur kan jag automatisera installationen av Microsoft Monitoring Agent (MMA) och beroende agent?

[Använd det här skriptet](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) för att installera agenterna. [Följ de här anvisningarna](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) för att installera MMA med hjälp av kommando raden eller Automation. Använd [det här skriptet](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)för MMA.

Förutom skript kan du använda distributions verktyg som System Center Configuration Manager, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) osv. för att distribuera agenterna.

### <a name="what-operating-systems-are-supported-by-mma"></a>Vilka operativ system stöds av MMA?

- [Granska](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) listan över Windows-operativsystem som stöds av MMA.
- [Granska](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) listan över Linux-operativsystem som stöds av MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Vilka operativ system stöds av beroende agenten?

[Granska](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) de Windows-operativsystem som stöds av beroende agenten.
[Granska](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) listan över Linux-operativsystem som stöds av beroende agenten.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Kan jag visualisera beroenden i Azure Migrate i mer än en timme?
Nej, du kan visualisera beroenden i upp till en timme. Du kan gå tillbaka till ett visst datum i historiken, fram till den senaste månaden, men den maximala varaktigheten för visualiseringen är en timme. Du kan till exempel använda varaktigheten för tiden i beroende kartan för att Visa beroenden för igår, men kan bara visa det för ett Tim fönster. Du kan dock använda Azure Monitor loggar för att [fråga beroende data](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre tid.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Stöds visualisering av beroenden för grupper med fler än 10 virtuella datorer?
Du kan [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) med upp till 10 virtuella datorer. Om du har en grupp med fler än 10 virtuella datorer rekommenderar vi att du delar upp gruppen i mindre grupper och sedan visualiserar beroendena.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Server Migration

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Hur Azure Migrate: Server-migrering skiljer sig från Azure Site Recovery?

Azure Migrate: Server migreringen utnyttjar Site Recoveryens replikeringstopologi för att utföra en agent-baserad migrering av virtuella VMware-datorer, migrering av virtuella Hyper-V-datorer och migrering av fysiska servrar till Azure. Alternativet utan agent för migrering av virtuella VMware-datorer är inbyggt i Server migreringen.

## <a name="next-steps"></a>Nästa steg
Läs [Azure Migrate översikt](migrate-services-overview.md)
