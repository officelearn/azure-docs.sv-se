---
title: Azure Migrate – vanliga frågor och svar (FAQ) | Microsoft Docs
description: Vanliga frågor och svar om Azure Migrate adresser
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: snehaa
ms.openlocfilehash: 3f035f38b1ad68e9e39d151ffad3fc650a0a1d80
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952757"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate – och vanliga frågor svar (FAQ)

Den här artikeln innehåller vanliga frågor och svar om Azure Migrate. Om du har några ytterligare frågor när du har läst den här artikeln, publicera dem på den [Azure Migrate forum](http://aka.ms/AzureMigrateForum).

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

**Migrering från VMware till Azure**: Om du planerar att migrera dina lokala arbetsbelastningar till Azure, använda Azure Migrate för planering av migreringsaktiviteter. Azure Migrate utvärderar lokala arbetsbelastningar och tillhandahåller vägledning, insikter och mekanismer för att hjälpa dig att migrera till Azure. Du kan använda tjänster som Azure Site Recovery och Azure Database Migration Service för att migrera datorerna till Azure när du är klar med din migreringsplan.

**Migrering från Hyper-V till Azure**: Azure Migrate stöder för närvarande endast utvärdering av virtuella VMware-datorer för migrering till Azure. Stöd för Hyper-V är våra planer för Azure Migrate. Under tiden kan använda du Site Recovery Deployment Planner. När Hyper-V-stöd är aktiverat i Azure Migrate kan använda du Azure Migrate för att planera migrering av Hyper-V-arbetsbelastningar.

**Haveriberedskap från VMware/Hyper-V till Azure**: Om du vill göra katastrofåterställning (DR) på Azure med hjälp av Azure Site Recovery (Site Recovery) använda Site Recovery Deployment Planner för DR planera. Distributionshanteraren för site Recovery har en djupgående, ASR-specifika bedömning av din lokala miljö. Den innehåller rekommendationer som krävs av Site Recovery för lyckade DR-åtgärder, till exempel replikering, redundans för dina virtuella datorer.  

### <a name="which-azure-regions-are-supported-by-azure-migrate"></a>Vilka Azure-regioner stöds av Azure Migrate?

Azure Migrate stöder för närvarande USA, östra och USA, västra centrala som migrering projekt platser. Observera att även om du kan bara skapa migreringsprojekt i västra centrala USA och östra USA, du kan fortfarande utvärdera dina datorer för [flera målplatser](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). Projektplatsen används bara för att lagra de identifierade data.

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Hur ansluter en lokal plats till Azure Migrate?

Anslutningen kan vara via internet eller använda ExpressRoute med offentlig peering.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Kan jag skydda den virtuella datorn som konfigurerats med den. OVA mallen?

Ytterligare komponenter (till exempel ett virusskyddsprogram) kan läggas till i den. OVA mallen är så länge lämnas kommunikation och brandväggen regler som krävs för Azure Migrate-installation för att fungera som.   

## <a name="discovery-and-assessment"></a>Identifiering och utvärdering

### <a name="what-data-is-collected-by-azure-migrate"></a>Vilka data som samlas in av Azure Migrate?

Azure Migrate stöder två typer av identifieringen, installationen-baserad identifiering och agentbaserad identifiering.
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

Agentbaserad identifiering är ett alternativ som är tillgängliga ovanpå installation-baserade identifieringen och hjälper kunder att [visualisera beroenden](how-to-create-group-machine-dependencies.md) för de lokala virtuella datorerna. Beroendeagenter samla in information som FQDN, operativsystem, IP-adress, MAC-adress, processer som körs i den virtuella datorn och de inkommande/utgående TCP-anslutningarna från den virtuella datorn. Agentbaserad identifiering är valfritt och du kan välja att inte installera agenterna om du inte vill att visualisera beroenden för de virtuella datorerna.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Var finns insamlade data lagrade och hur länge?

Data som samlas in av insamlingsprogrammet lagras på Azure-plats som du anger när du skapar migration-projekt. Data lagras säkert i en Microsoft-prenumeration och tas bort när användaren tar bort Azure Migrate-projektet.

För visualisering av beroenden, om du installerar agenter på de virtuella datorerna, lagras data som samlas in av beroendeagenter i USA i en OMS-arbetsyta som skapats i användarens prenumeration. Dessa data tas bort när du tar bort OMS-arbetsytan i din prenumeration. [Läs mer](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Data krypteras i vila och under överföring?

Ja, insamlade data krypteras både i vila och under överföringen. De metadata som samlas in av installationen skickas på ett säkert sätt till Azure Migrate-tjänsten över internet via https. Insamlade metadata lagras i [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) och i [Azure blobblagring](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) i en Microsoft-prenumeration och krypteras i viloläge.

Data som samlas in av agenterna beroende är också krypterade i rörelse (säker https-kanal) och lagras i Log Analytics-arbetsytan i användarens prenumeration. Det är också krypterade i vila.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Hur insamlaren kommunicera med vCenter-servern och tjänsten Azure Migrate?

Insamlaren ansluter till vCenter-servern (port 443) med de autentiseringsuppgifter som anges av användaren i installationen. Det frågor till vCenter-servern med hjälp av VMware PowerCLI att samla in metadata om datorer som hanteras av vCenter-servern. Den samlar in båda konfigurationsdata om virtuella datorer (kärnor, minne, diskar, nätverkskort osv) samt prestandahistoriken för varje virtuell dator för den senaste månaden från vCenter-servern. Insamlade metadata skickas sedan till tjänsten Azure Migrate (över internet via https) för utvärdering. [Läs mer](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Kan jag ansluta samma insamlingsprogrammet till flera vCenter-servrar?

Ja, en enda insamlingsprogrammet kan användas för att identifiera flera vCenter-servrar, men inte samtidigt. Du behöver köra identifieringar efter varandra.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Är den. OVA-mallen som används av Site Recovery integrerade med den. OVA som används av Azure Migrate?

Det finns för närvarande ingen integrering. Den. OVA-mallen i Site Recovery används för att ställa in en konfigurationsserver för Site Recovery för replikering av virtuella VMware-datorer/fysiska server. Den. OVA som används av Azure Migrate används för att identifiera virtuella VMware-datorer hanteras av en vCenter-server för migreringsutvärdering.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Jag har ändrat storlek på min dator. Kan jag köra utvärderingen?

Om du ändrar inställningarna på en virtuell dator som du vill utvärdera, Upptäck utlösaren igen med insamlingsprogrammet. I installationen använder det **starta insamlingen igen** möjlighet att göra detta. När samlingen är klar väljer du den **beräkna om** alternativet för utvärdering i portalen för att hämta uppdaterade utvärderingsresultat.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Hur kan jag för att identifiera en miljö med flera organisationer i Azure Migrate?

Om du har en miljö som delas mellan klienter och du inte vill identifiera de virtuella datorerna i en klient i en annan klient prenumeration, kan du använda fältet omfång i insamlingsprogrammet att definiera omfattningen av identifieringen. Om klienterna delar värdar, skapar du autentiseringsuppgifter som har skrivskyddad åtkomst till endast de virtuella datorerna som hör till specifik klient och använda den här autentiseringsuppgiften i insamlingsprogrammet och ange omfång som värden för att göra identifieringen. Du kan också du kan också skapa mappar i vCenter Server (vi antar att Mapp1 för tenant1 och mapp2 för tenant2), under den dela värden, flytta de virtuella datorerna för tenant1 i Mapp1 och tenant2 till Mapp2 och omfång identifieringar i insamlaren i enlighet med detta genom att ange mappen.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Hur många virtuella datorer kan identifieras i en enda migration-projekt?

Du kan identifiera 1500 virtuella datorer i en enda migreringsprojekt. Om du har flera virtuella datorer i din lokala miljö, [mer](how-to-scale-assessment.md) om hur du kan identifiera en stor miljö i Azure Migrate.

## <a name="dependency-visualization"></a>Visualisering av beroenden

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Behöver jag betala för att använda funktionen beroendevisualisering?

Azure Migrate är tillgänglig utan extra kostnad. Mer information om priser för Azure Migrate finns [här](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan jag använda en befintlig arbetsyta för visualisering av beroenden?

Azure Migrate stöder inte användning av en befintlig arbetsyta för visualisering av beroenden, men Microsoft Monitoring Agent (MMA) har stöd för flera värdar och gör det möjligt att skicka data till flera arbetsytor. Så om du redan har agenter distribuerat och konfigurerat till en arbetsyta kan du utnyttja flera värdar i MMA-agenten och konfigurera den till arbetsytan Azure Migrate (utöver den befintliga arbetsytan) och gör att den fungerar. [Här](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) är en blogg på hur du kan aktivera flera värdar i ett MMA-agenten.

## <a name="next-steps"></a>Nästa steg

- Läs den [Azure Migrate översikt](migrate-overview.md)
- Lär dig hur du kan [identifiera och utvärdera](tutorial-assessment-vmware.md) en VMware-miljö
