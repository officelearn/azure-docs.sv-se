---
title: Azure Migrate – vanliga frågor och svar (FAQ) | Microsoft Docs
description: Vanliga frågor och svar om Azure Migrate adresser
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: snehaa
ms.openlocfilehash: ebc4393341341b3b73165a166a650ae1a6f431ff
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257802"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate – och vanliga frågor svar (FAQ)

Den här artikeln innehåller vanliga frågor och svar om Azure Migrate. Om du har några ytterligare frågor när du har läst den här artikeln, publicera dem på den [Azure Migrate forum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Allmänt

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Stöder Azure Migrate bedömning av VMware-arbetsbelastningar?

Ja, Azure Migrate stöder för närvarande endast bedömning av VMware-arbetsbelastningar. Stöd för Hyper-V och fysiska servrar kommer att aktiveras i framtiden.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Behöver Azure Migrate vCenter Server för att identifiera en VMware-miljö?

Ja, Azure Migrate kräver vCenter Server för att identifiera en VMware-miljö. Det stöder inte identifiering av ESXi-värdar som inte hanteras av en vCenter Server.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Hur skiljer sig Azure Migrate från Azure Site Recovery?

Azure Migrate är en utvärdering av tjänst som hjälper dig att identifiera dina lokala arbetsbelastningar och planera din migrering till Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), tillsammans med som en lösning för haveriberedskap, hjälper dig att migrera lokala arbetsbelastningar till virtuella IaaS-datorer i Azure.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Vad är skillnaden mellan att använda Azure Migrate för utvärderingar och Map Toolkit?

[Azure Migrate](migrate-overview.md) ger migreringsutvärdering specifikt för att hjälpa till med beredskapen för migrering och utvärdering av lokala arbetsbelastningar till Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) har andra funktioner. Till exempel migreringsplanering för nyare versioner av Windows klient- och operativsystem, programvara användning spåra osv. Dessa scenarier kan du fortsätta att använda MAP Toolkit.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Hur skiljer sig Azure Migrate från Distributionshanteraren för Azure Site Recovery?

Azure Migrate är ett verktyg för migreringsplanering och Distributionshanteraren för Azure Site Recovery är en katastrofåterställning (DR) kapacitetsplaneringsverktyget.

**Migrering från VMware till Azure**: Om du planerar att migrera dina lokala arbetsbelastningar till Azure, kan du använda Azure Migrate för för planering av migreringsaktiviteter. Azure Migrate utvärderar lokala arbetsbelastningar och tillhandahåller vägledning, insikter och mekanismer för att hjälpa dig att migrera till Azure. Du kan använda tjänster som Azure Site Recovery och Azure Database Migration Service för att migrera datorerna till Azure när du är klar med din migreringsplan.

**Migrering från Hyper-V till Azure**: Azure Migrate stöder för närvarande endast utvärdering av virtuella VMware-datorer för migrering till Azure. Stöd för Hyper-V är våra planer för Azure Migrate. Under tiden kan använda du Site Recovery Deployment Planner. När Hyper-V-stöd är aktiverat i Azure Migrate kan använda du Azure Migrate för att planera migrering av Hyper-V-arbetsbelastningar.

**Haveriberedskap från VMware/Hyper-V till Azure**: Om du vill göra katastrofåterställning (DR) på Azure med hjälp av Azure Site Recovery (Site Recovery), kan du använda Site Recovery Deployment Planner för DR planera. Distributionshanteraren för site Recovery har en djupgående, ASR-specifika bedömning av din lokala miljö. Den innehåller rekommendationer som krävs av Site Recovery för lyckade DR-åtgärder, till exempel replikering, redundans för dina virtuella datorer.  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Vilka geografiska Azure-områden som stöds av Azure Migrate?

Azure Migrate stöder för närvarande USA och Azure Government som projektet geografiska områden. Trots att du kan bara skapa migreringsprojekt i dessa geografiska områden, du kan fortfarande utvärdera dina datorer för [flera målplatser](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). Projekt-geografi används endast för att lagra de identifierade metadata.

**Geografi** | **Lagringsplats för metadata**
--- | ---
Unites tillstånd | USA, västra centrala eller USA, östra
Azure Government | Virginia (USA-förvaltad region)

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Hur ansluter en lokal plats till Azure Migrate?

Anslutningen kan vara via internet eller använda ExpressRoute med offentlig peering.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Kan jag skydda den virtuella datorn som konfigurerats med OVA-mallen?

Ytterligare komponenter (till exempel ett virusskyddsprogram) kan läggas till i OVA-mallen som kommunikation och brandväggen regler som krävs för Azure Migrate-installation för att arbeta lämnas skick.   

## <a name="discovery"></a>Identifiering

### <a name="what-data-is-collected-by-azure-migrate"></a>Vilka data som samlas in av Azure Migrate?

Azure Migrate stöder två typer av identifiering: installationsbaserad identifiering och agentbaserad identifiering.
Installationen-baserade identifieringen samlar in metadata om lokala virtuella datorer, den fullständiga listan med metadata som samlas in av installationen som listas nedan:

**Konfigurationsdata för den virtuella datorn**
- Visningsnamn för virtuell dator (på vCenter)
- VM inventering-sökväg (värd/kluster/mapp i vCenter)
- IP-adress
- MAC-adress
- Operativsystem
- Antal kärnor, diskar, nätverkskort
- Minnesstorlek, diskstorlekar

**Prestandadata för den virtuella datorn**
- Processoranvändning
- Minnesanvändning
- För varje disk som är ansluten till den virtuella datorn:
  - Disk-lästa dataflöde
  - Dataflöde per disk för skrivningar
  - Diskläsningar-åtgärder per sekund
  - Disk skriver-åtgärder per sekund
- För varje nätverkskort som är anslutet till den virtuella datorn:
  - Nätverk in
  - Nätverk ut

Agentbaserad identifiering är ett alternativ som är tillgängligt utöver installationsbaserad identifiering och hjälper kunder att [visualisera beroenden](how-to-create-group-machine-dependencies.md) för deras lokala virtuella datorer. Beroendeagenterna samlar in information som FQDN, operativsystem, IP-adress, MAC-adress, processer som körs på den virtuella datorn och de inkommande/utgående TCP-anslutningarna från den virtuella datorn. Agentbaserad identifiering är valfritt och du kan välja att inte installera agenterna om du inte vill att visualisera beroenden för de virtuella datorerna.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Skulle det finnas en prestandaförsämring på analyserade ESXi värdmiljön?

I fall med den [en metod för identifiering av tid](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods), för att samla in prestandadata, statistiknivån i vCenter-servern måste vara inställd på 3. Ange värdet till den här nivån skulle samla in ett stort antal felsökning av data som lagras i vCenter Server-databas. Det kan därför orsaka vissa prestandaproblem på vCenter-servern. Det skulle vara ska påverkas minimalt på ESXi-värden.

Vi har introducerat kontinuerlig profilering av prestandadata (som finns i förhandsversion). Med kontinuerlig profilering finns inte längre behöver ändra vCenter Server statistiknivån att göra en utvärdering för prestandabaserad. Insamlingsprogrammet kommer nu att profilera de lokala datorerna för att mäta prestandadata för de virtuella datorerna. Detta skulle ha nästan noll prestandapåverkan på ESXi-värdar och på vCenter-servern.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Var finns insamlade data lagrade och hur länge?

Data som samlas in av insamlingsprogrammet lagras på Azure-plats som du anger när du skapar migration-projekt. Data lagras säkert i en Microsoft-prenumeration och tas bort när användaren tar bort Azure Migrate-projektet.

För visualisering av beroenden, om du installerar agenter på de virtuella datorerna, lagras data som samlas in av agenterna beroende i USA i en Log Analytics-arbetsyta som skapades i användarens prenumeration. Dessa data tas bort när du tar bort logganalys-arbetsytan i din prenumeration. [Läs mer](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Data krypteras i vila och under överföring?

Ja, insamlade data krypteras både i vila och under överföringen. De metadata som samlas in av installationen skickas på ett säkert sätt till Azure Migrate-tjänsten över internet via https. Insamlade metadata lagras i [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) och i [Azure blobblagring](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) i en Microsoft-prenumeration och krypteras i viloläge.

Data som samlas in av agenterna beroende är också krypterade i rörelse (säker https-kanal) och lagras i Log Analytics-arbetsytan i användarens prenumeration. Det är också krypterade i vila.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Hur insamlaren kommunicera med vCenter-servern och tjänsten Azure Migrate?

Insamlaren ansluter till vCenter-servern (port 443) med de autentiseringsuppgifter som anges av användaren i installationen. Det frågor till vCenter-servern med hjälp av VMware PowerCLI att samla in metadata om datorer som hanteras av vCenter-servern. Den samlar in båda konfigurationsdata om virtuella datorer (kärnor, minne, diskar, nätverkskort osv) samt prestandahistoriken för varje virtuell dator för den senaste månaden från vCenter-servern. Insamlade metadata skickas sedan till tjänsten Azure Migrate (över internet via https) för utvärdering. [Läs mer](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Kan jag ansluta samma insamlingsprogrammet till flera vCenter-servrar?

Ja, en enda insamlingsprogrammet kan användas för att identifiera flera vCenter-servrar, men inte samtidigt. Du behöver köra identifieringar efter varandra.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>OVA-mallen som används av Site Recovery är integrerad med ova-filen som används av Azure Migrate?

Det finns för närvarande ingen integrering. Den. OVA-mallen i Site Recovery används för att ställa in en konfigurationsserver för Site Recovery för replikering av virtuella VMware-datorer/fysiska server. Den. OVA som används av Azure Migrate används för att identifiera virtuella VMware-datorer hanteras av en vCenter-server för migreringsutvärdering.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Jag har ändrat storlek på min dator. Kan jag köra utvärderingen?

Om du ändrar inställningarna på en virtuell dator som du vill utvärdera, Upptäck utlösaren igen med insamlingsprogrammet. I installationen använder det **starta insamlingen igen** möjlighet att göra detta. När insamlingen är klar väljer du alternativet **Beräkna om** för utvärderingen på portalen för att hämta uppdaterade utvärderingsresultat.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Hur kan jag för att identifiera en miljö med flera organisationer i Azure Migrate?

Om du har en miljö som delas mellan klienter och du inte vill identifiera de virtuella datorerna i en klient i en annan klient prenumeration, kan du använda fältet omfång i insamlingsprogrammet att definiera omfattningen av identifieringen. Om klienterna delar värdar, skapar du autentiseringsuppgifter som har skrivskyddad åtkomst till endast de virtuella datorerna som hör till specifik klient och använda den här autentiseringsuppgiften i insamlingsprogrammet och ange omfång som värden för att göra identifieringen. Du kan också du kan också skapa mappar i vCenter Server (vi antar att Mapp1 för tenant1 och mapp2 för tenant2), under den dela värden, flytta de virtuella datorerna för tenant1 i Mapp1 och tenant2 till Mapp2 och omfång identifieringar i insamlaren i enlighet med detta genom att ange mappen.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Hur många virtuella datorer kan identifieras i en enda migration-projekt?

Du kan identifiera 1500 virtuella datorer i en enda migreringsprojekt. Om du har flera virtuella datorer i din lokala miljö, [mer](how-to-scale-assessment.md) om hur du kan identifiera en stor miljö i Azure Migrate.

## <a name="assessment"></a>Utvärdering

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Kostar Azure Migrate support för Enterprise Agreement (EA) baserade uppskattning?

Azure Migrate stöder för närvarande inte kostnadsuppskattning för [erbjudande för Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). Lösningen är att ange att betala per användning erbjudandet och manuellt ange procentvärdet (gäller för prenumerationen) i fältet ”Rabatt” i egenskaperna för utvärdering.

  ![Rabatt](./media/resources-faq/discount.png)


## <a name="dependency-visualization"></a>Visualisering av beroenden

> [!NOTE]
> Beroendevisualiseringsfunktionen är inte tillgänglig i Azure Government.

### <a name="what-is-dependency-visualization"></a>Vad är beroendevisualisering?

Beroendevisualisering gör det möjligt för dig att utvärdera grupper med virtuella datorer för migrering med större tillförlitlighet genom att dubbelkontrollera datorberoenden innan du kör en utvärdering. Beroendevisualisering hjälper dig att se till att inget kvar, undvika oväntade avbrott när du migrerar till Azure. Azure Migrate använder Tjänstkarta-lösningen i Log Analytics för att aktivera beroendevisualisering.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Behöver jag betala för att använda funktionen beroendevisualisering?

Nej. Mer information om priser för Azure Migrate finns [här](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Måste jag installera något för visualisering av beroenden?

Om du vill använda visualisering av beroenden som du behöver hämta och installera agenter på varje lokal dator som du vill utvärdera.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) måste installeras på varje dator.
- Den [beroendeagenten](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) måste installeras på varje dator.
- Dessutom om du har datorer som saknar Internetanslutning kan behöva du hämta och installera Log Analytics-gatewayen på dem.

Du behöver inte dessa agenter på datorer som du vill utvärdera om du inte använder beroendevisualisering.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan jag använda en befintlig arbetsyta för visualisering av beroenden?

Ja, Azure Migrate nu kan du bifoga en befintlig arbetsyta i migreringsprojektet och utnyttja dessa för visualisering av beroenden. [Läs mer](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Kan jag exportera visualisering beroenderapport?

Nej, beroendevisualiseringen kan inte exporteras. Men eftersom Azure Migrate använder Tjänstkarta för visualisering av beroenden, du kan använda den [Service Map REST API: er](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) att hämta beroendena i json-format.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Hur kan jag automatisera installationen av Microsoft Monitoring Agent (MMA) och beroendeagenten?

[Här](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) är ett skript som du kan använda för installation av beroendeagenten. För MMA, [här](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) är ett skript som är tillgängliga på TechNet som du kan använda.

Förutom skript, kan du även använda distributionsverktyg som System Center Configuration Manager (SCCM), [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) osv. för att distribuera agenter.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Vilka är de operativsystem som stöds av MMA?

Lista över Windows-operativsystem som stöds av MMA är [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Listan med Linux-operativsystem som stöds av MMA är [här](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Vilka är de operativsystem som stöds av beroendeagenten?

Lista över Windows-operativsystem som stöds av beroendeagenten är [här](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Listan med Linux-operativsystem som stöds av beroendeagenten är [här](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Kan jag visualisera beroenden i Azure Migrate för mer än en timme?
Nej, Azure Migrate kan du visualisera beroenden för varaktighet för upp till en timme. Azure Migrate kan du gå tillbaka till ett visst datum i historiken för upp till senaste månaden, men maximal varaktighet för vilken du kan visualisera beroenden är upp till 1 timme. Exempel: du kan använda funktionen tid varaktighet på beroendekartan visar beroenden för igår, men kan bara visa för ett fönster med en timme.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Stöds visualisering av beroenden för grupper med fler än 10 virtuella datorer?
Du kan [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) som har upp till 10 virtuella datorer, om du har en grupp med fler än 10 virtuella datorer, rekommenderar vi att dela upp gruppen i mindre grupper och visualisera beroenden.


## <a name="next-steps"></a>Nästa steg

- Läs den [Azure Migrate översikt](migrate-overview.md)
- Lär dig hur du kan [identifiera och utvärdera](tutorial-assessment-vmware.md) en VMware-miljö
