---
title: Vanliga frågor om Azure Migrate
description: Behandlar vanliga och vanliga frågor om Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: snehaa
ms.openlocfilehash: 1663e1d418610bcfa2ada28770944bfc8cbf908f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480062"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: vanliga frågor

I den här artikeln besvaras vanliga frågor om Azure Migrate. Om du har fler frågor efter att ha läst den här artikeln kan du publicera dem i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Allmänt

### <a name="which-azure-geographies-are-supported"></a>Vilka Azure-geografiska områden stöds?

Granska Azure Migrate stödda geografiska områden för [virtuella VMware-datorer](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) och för [virtuella Hyper-V-datorer](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Vad är skillnaden mellan Azure Migrate och Site Recovery?

Azure Migrate tillhandahåller en central hubb för att hantera och spåra identifiering, utvärdering och migrering av lokala datorer och arbets belastningar till Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) är en katastrof återställnings lösning. Azure Migrate: Migreringsverktyg för Server använder vissa Server dels Site Recovery funktioner för att lyfta och flytta migreringen av vissa lokala datorer.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Hur gör jag för att ta bort ett Azure Migrate-projekt?

Ta bort ett projekt genom att följa [dessa anvisningar](how-to-delete-project.md) . [Granska de resurser](how-to-delete-project.md#created-resources) som skapas när du identifierar, utvärderar och migrerar datorer och arbets belastningar i ett Azure Migrate projekt.


## <a name="azure-migrate-appliance"></a>Azure Migrate-installation

### <a name="how-does-the-appliance-connect-to-azure"></a>Hur ansluter-enheten till Azure?

Anslutningen kan vara över Internet eller använda Azure-ExpressRoute med offentlig/Microsoft-peering.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Vilken nätverks anslutning krävs för Azure Migrate Server bedömning och migrering?

Granska [VMware](migrate-support-matrix-vmware.md) -och [Hyper-V-](migrate-support-matrix-hyper-v.md) Supportens matriser för information om de URL: er och portar som krävs för att Azure Migrate ska kunna kommunicera med Azure.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>Kan jag skärpa den virtuella dator som har skapats med mallen?

Du kan lägga till komponenter (till exempel Antivirus) till mallen, förutsatt att du lämnar de kommunikations-och brand Väggs regler som krävs för Azure Migrate-installationen.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Vilka data samlas in av Azure Migrates apparaten?

Granska data som samlas in av apparaten enligt följande:
- [Prestanda data](migrate-appliance.md#collected-performance-data-vmware) och [metadata](migrate-appliance.md#collected-metadata-vmware) för virtuella VMware-datorer.
- [Prestanda data](migrate-appliance.md#collected-performance-data-hyper-v) och [metadata](migrate-appliance.md#collected-metadata-hyper-v) för virtuella Hyper-V-datorer.


### <a name="does-appliance-analysis-impact-performance"></a>Påverkar enhets analysen prestanda?

Azure Migrate utrustnings profiler lokalt för att mäta prestanda data för virtuella datorer kontinuerligt. Den här profileringen har nästan ingen prestanda påverkan på Hyper-V/ESXi-värdar eller på vCenter Server.

### <a name="where-and-how-long-is-collected-data-stored"></a>Var och hur länge samlar in lagrade data?

De data som samlas in av Azure Migrate-installationen lagras på den Azure-plats som du väljer när du skapar migreringsjobbet. Data lagras säkert i en Microsoft-prenumeration och tas bort när du tar bort Azure Migrate-projektet.

För beroende visualisering lagras data som samlas in i USA i en Log Analytics arbets yta som skapats i Azure-prenumerationen. Dessa data tas bort när du tar bort Log Analytics arbets ytan i din prenumeration. [Lär dig mer](concepts-dependency-visualization.md) om beroende visualisering.

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Vilken data volym överförs under kontinuerlig profilering?

Mängden data som skickas till Azure Migrate varierar beroende på flera parametrar. För att ge dig en uppfattning om volymen skickar ett Azure Migrate-projekt med 10 datorer (var och en med en disk och ett nätverkskort) cirka 50 MB per dag. Värdet är ungefärligt och ändras baserat på antalet data punkter för nätverkskorten och diskarna. Ökningen av data som skickas är icke-linjär om antalet datorer, nätverkskort eller diskar ökar.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Är data krypterade i vila och under överföring?

Ja, båda.
- Metadata skickas säkert till Azure Migrate tjänsten via Internet via HTTPS.
- Metadata lagras i en [Azure Cosmos Database](../cosmos-db/database-encryption-at-rest.md) -databas och i [Azure Blob Storage](../storage/common/storage-service-encryption.md) i en Microsoft-prenumeration. Metadata är krypterade i vila.
- Data för beroende analys krypteras också i överföring (säker HTTPS). Den lagras i en Log Analytics arbets yta i din prenumeration. Det är också krypterat i vila.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>Hur kommunicerar-enheten med vCenter Server och Azure Migrate?

1. Enheten ansluter till vCenter Server (port 443) med de autentiseringsuppgifter du angav när du konfigurerade installationen.
2. Enheten använder VMware PowerCLI för att fråga vCenter Server, för att samla in metadata om de virtuella datorer som hanteras av vCenter Server. Den samlar in konfigurations data om virtuella datorer (kärnor, minne, diskar, nätverkskort) och prestanda historiken för varje virtuell dator under den senaste månaden.
3. De insamlade metadata skickas sedan till Azure Migrate: Server utvärdering (via Internet via HTTPS) för utvärdering.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Kan jag ansluta samma installation till flera vCenter Server instanser?

Nej. Det finns en en-till-en-mappning mellan en installation och vCenter Server. Du måste distribuera flera enheter för att kunna identifiera virtuella datorer på flera vCenter Server-instanser.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>Datorns storlek har ändrats. Kan jag köra utvärderingen igen?

Azure Migrates enheten samlar kontinuerligt in information om den lokala miljön. Men en utvärdering är en tidpunkts ögonblicks bild av lokala virtuella datorer. Om du ändrar inställningarna för en virtuell dator som du vill utvärdera använder du alternativet beräkna om du vill uppdatera utvärderingen med de senaste ändringarna.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Hur kan jag utföra identifiering i en miljö med flera innehavare?

- Om din miljö delas mellan klienter för VMware och du inte vill identifiera de virtuella datorerna för en klient i en annan klient organisation, skapar du vCenter Server autentiseringsuppgifter som bara har åtkomst till de virtuella datorer som du vill identifiera. Använd sedan autentiseringsuppgifterna när du startar identifiering i Azure Migrate-installationen.
- För Hyper-V använder identifieringen autentiseringsuppgifter för Hyper-V-värden. Om virtuella datorer delar samma Hyper-V-värd finns det för närvarande inget sätt att separera identifieringen.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Hur många virtuella datorer kan jag identifiera med en enda apparat?

Du kan identifiera upp till 10 000 virtuella VMware-datorer och upp till 5 000 virtuella Hyper-V-datorer med en enda migreringstjänster. Om du har fler datorer i din lokala miljö kan du läsa om hur du skalar [Hyper-V-](scale-hyper-v-assessment.md) och [VMware](scale-vmware-assessment.md) -utvärderingen.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Kan jag ta bort Azure Migrate-apparaten från projektet?

Det finns inte stöd för att ta bort program från projektet.

- Det enda sättet att ta bort installationen är att ta bort resurs gruppen som innehåller det Azure Migrate projekt som är associerat med enheten.
- Men om du tar bort resurs gruppen raderas även andra registrerade enheter, den identifierade inventeringen, utvärderingen och alla andra Azure-komponenter som är kopplade till projektet i resurs gruppen.

## <a name="azure-migrate-server-assessment"></a>Utvärdering av Azure Migrate Server


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Behöver Azure Migrate vCenter Server för identifiering av virtuella VMWare-datorer?

Ja, Azure Migrate behöver vCenter Server för att utföra identifiering i en VMware-miljö. Den stöder inte identifiering av ESXi-värdar som inte hanteras av vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Vad är skillnaden mellan Azure Migrate Server utvärdering och kart verktygen?

Server utvärdering ger en utvärdering för att hjälpa till med migreringen och utvärdering av arbets belastningar för migrering till Azure. [Verktyget Microsoft Assessment and Planning (kart)](https://www.microsoft.com/download/details.aspx?id=7826) hjälper till med andra uppgifter, inklusive migrerings planering för nyare versioner av Windows-klient/server-operativsystem och spårning av program varu användning. Fortsätt att använda kart verktyget för dessa scenarier.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Vad är skillnaden mellan server utvärdering och Site Recovery distributions planeraren?

Server utvärdering är ett verktyg för migrerings planering. Site Recovery Deployment Planner är ett planerings verktyg för haveri beredskap. 

- **Planera lokal migrering till Azure**: om du planerar att migrera dina lokala servrar till Azure använder du Server bedömning för migrering. Den bedömer lokala arbets belastningar och ger vägledning och verktyg som hjälper dig att migrera. När migrerings planen är på plats kan du använda verktyg, inklusive Azure Migrate Server-migrering, för att migrera datorerna till Azure.
- **Planera haveri beredskap till Azure**: om du planerar att konfigurera haveri beredskap från en lokal plats till Azure med Site Recovery använder du Site Recovery Deployment Planner. Distributions planeraren ger en djup, Site Recovery-bestämd utvärdering av din lokala miljö för katastrof återställning. Den ger rekommendationer kring haveri beredskap, till exempel replikering och redundans.

### <a name="does-azure-migrate-estimate-costs-for-the-enterprise-agreement-ea-program"></a>Beräknar Azure Migrate kostnader för programmet Enterprise-avtal (EA)?

Azure Migrate Server utvärderingen stöder för närvarande inte kostnads uppskattningar för [Enterprise-avtal programmet](https://azure.microsoft.com/offers/enterprise-agreement-support/). Som en lösning kan du när du skapar en utvärdering ange **betala per** användning som **erbjudande**och manuellt lägga till rabatt procenten (som gäller för prenumerationen) som **rabatt** i bedömnings egenskaperna:

  ![Bedömnings egenskaper](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Vad är skillnaden mellan både lokal och Prestandaoptimerad storlek?

Vid samma storlek som lokal storlek kan Azure Migrate inte beakta prestanda data för virtuella datorer för utvärdering. Den utvärderar VM-storlekar baserat på den lokala konfigurationen. Med prestanda-baserad storleks ändring baseras storleken på användnings data.

- Om till exempel en lokal virtuell dator har 4 kärnor och 8 GB minne med 50% processor användning och 50% minnes användning, inträffar följande:
    - Som lokal storlek rekommenderar vi en Azure VM-SKU som har fyra kärnor och 8 GB minne.
    - Prestandabaserade storleks ändringar kommer att rekommendera en VM-SKU som har två kärnor och 4 GB minne, eftersom användnings procenten beaktas.

- På samma sätt beror disk storleken på två bedömnings egenskaper: storleks kriterier och lagrings typ.
    - Om storleks kriteriet är prestanda beroende och lagrings typen är automatisk, använder Azure Migrate IOPS-och data flödes värden för disken i kontot när den identifierar mål disk typ (standard eller Premium).
    - Om storleks kriteriet är prestanda beroende och lagrings typen är Premium rekommenderar Azure Migrate en Premium-disk-SKU, baserat på den lokala diskens storlek. Samma logik används för disk storlek, när storleken är lokalt och lagrings typen är standard eller Premium.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>Påverkar prestanda historiken och användningen av percentilen storleks rekommendationer?

Dessa egenskaper gäller endast för prestandabaserad storleksändring.

- Azure Migrate samlar in prestanda historiken för lokala datorer och använder den för att rekommendera den virtuella dator storleken och disk typen i Azure.
- Enheten registrerar kontinuerligt den lokala miljön för att samla in användnings data i real tid var 20: e sekund.
- Enheten samlar in de 20 sekunderna exemplen och skapar en enda data punkt var 15: e minut.
- För att skapa data punkten väljer installationen det högsta värdet från alla exempel på 20 sekunder.
- Enheten skickar data punkten till Azure.

När du skapar en utvärdering i Azure, baserat på varaktigheten prestanda och prestanda historik, beräknar Azure Migrate det effektiva användning svärdet och använder det för storleks ändring.

- Om du till exempel ställer in varaktigheten för varaktigheten på en dag och percentilvärdet till 95 percentilen, sorterar Azure Migrate de 15 minuters exempel punkter som skickats av insamlaren under den senaste dagen i stigande ordning, och plockar värdet 95 percentilvärdet som effektiv användning.
- Genom att använda 95 percentilvärdet ser du till att avvikande värden ignoreras. Avvikare kan inkluderas om du använder 99-percentilen. Om du vill välja högsta användning för perioden, utan saknade avvikande, väljer du 99 percentilen.

## <a name="server-assessment---dependency-visualization"></a>Server bedömning-beroende visualisering


### <a name="what-is-dependency-visualization"></a>Vad är beroende visualisering?

Använd beroende visualisering för att utvärdera grupper av virtuella datorer för migrering med större säkerhet. Beroende visualisering mellan kontroller av dator beroenden innan du kör en utvärdering. Det hjälper till att se till att inget är kvar bakom, och bidrar därmed till att undvika oväntade avbrott när du migrerar till Azure. Azure Migrate använder Tjänstkarta-lösningen i Azure Monitor för att aktivera beroende visualisering. [Läs mer](concepts-dependency-visualization.md).

> [!NOTE]
> Beroende visualisering är inte tillgänglig i Azure Government.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Måste jag betala för att använda beroende visualisering?
Nej. [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Behöver jag installera något för beroende visualisering?

Om du vill använda beroende visualisering måste du ladda ned och installera agenter på varje lokal dator som du vill utvärdera.

Du måste installera följande agenter på varje dator:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Beroende agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Om du har datorer som saknar Internet anslutning måste du ladda ned och installera Log Analytics gateway på dem.

Du behöver inte dessa agenter om du inte använder beroende visualisering.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan jag använda en befintlig arbets yta för beroende visualisering?

Ja, du kan koppla en befintlig arbets yta till migreringsjobbet och använda den för beroende visualisering. [Läs mer](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Kan jag exportera rapporten för beroende visualisering?

Nej, det går inte att exportera beroende visualiseringen. Azure Migrate använder dock Tjänstkarta och du kan använda [tjänstkarta-REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) för att hämta beroenden i JSON-format.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Hur kan jag automatisera installationen av Microsoft Monitoring Agent (MMA) och beroende agenten?

Använd det här [skriptet för att installera beroende agenten](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Följ de här [anvisningarna för att installera MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) med hjälp av kommando raden eller Automation. Använd [det här skriptet](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)för MMA.

Förutom skript kan du också använda distributions verktyg som System Center Configuration Manager och [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) för att distribuera agenterna.


### <a name="what-operating-systems-are-supported-by-mma"></a>Vilka operativ system stöds av MMA?

- Visa listan över [Windows-operativsystem som stöds av MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Visa listan över [Linux-operativsystem som stöds av MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Kan jag visualisera beroenden i mer än en timme?
Nej. Du kan visualisera beroenden i upp till en timme. Du kan gå tillbaka till ett visst datum i historiken, så långt tillbaka som en månad, men den maximala varaktigheten för visualiseringen är en timme. Du kan till exempel använda tids längden i beroende kartan för att Visa beroenden för igår, men du kan bara Visa beroenden för ett enda timme-fönster. Du kan dock använda Azure Monitor loggar för att [fråga beroende data](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre tid.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>Kan jag använda beroende visualisering för grupper med fler än 10 virtuella datorer?
Du kan [visualisera beroenden](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) för grupper som innehåller upp till 10 virtuella datorer. Om du har en grupp med fler än 10 virtuella datorer rekommenderar vi att du delar upp gruppen i mindre grupper och sedan visualiserar beroendena.

## <a name="azure-migrate-server-migration"></a>Migrering av Azure Migrate Server

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Vad är skillnaden mellan migrering av Azure Migrate Server och Site Recovery?

- Migrering utan agent för lokala virtuella VMware-datorer är inbyggt i Azure Migrate Server-migreringen.
- För migrering av virtuella Hyper-V-datorer, fysiska servrar och agentbaserade virtuella VMware-datorer använder Azure Migrate Server-migreringen Azure Site Recovery-replikerings motor.


## <a name="next-steps"></a>Nästa steg
Läs [Azure Migrate översikt](migrate-services-overview.md).
