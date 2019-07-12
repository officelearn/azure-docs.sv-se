---
title: Azure Migrate – vanliga frågor och svar (FAQ) | Microsoft Docs
description: Vanliga frågor och svar om Azure Migrate adresser
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807327"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate – och vanliga frågor svar (FAQ)

Den här artikeln innehåller vanliga frågor och svar om Azure Migrate. Om du har några ytterligare frågor när du har läst den här artikeln, publicera dem på den [Azure Migrate forum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Allmänt

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Vilka geografiska Azure-områden som stöds av Azure Migrate?
Azure Migrate stöder för närvarande ett antal olika geografiska områden där du kan skapa ett Azure Migrate-projekt. Trots att du kan bara skapa projekt i dessa geografiska områden, kan du fortfarande utvärdera dina datorer för andra platser för återställningsmålet. Projekt-geografi används endast för att lagra de identifierade metadata.

**Geografi** | **Metadata lagringsplats** Azure Government | USA Gov Virginia Asien | Sydostasien eller Asien, östra Europa | Södra Europa eller Europa, västra Storbritannien | Storbritannien, södra eller Storbritannien, västra USA | USA, centrala eller USA, västra 2

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Hur skiljer sig Azure Migrate från Azure Site Recovery?

Azure Migrate tillhandahåller verktyg som hjälper dig att identifiera, utvärdera och migrera datorer och arbetsbelastningar till Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) är en lösning för haveriberedskap. Båda tjänsterna delar vissa komponenter.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate-installation (VMware/fysiska servrar)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Hur ansluter Azure Migrate-installation till Azure?

Anslutningen kan vara via internet eller du kan använda ExpressRoute med offentlig peering.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Vilka anslutningskrav krävs för Azure Migrate Server bedömning och migrering

För URL: er och portar som krävs för Azure Migrate att kommunicera med Azure kan du granska den [VMWare](migrate-support-matrix-vmware.md) och [Hyper-V](migrate-support-matrix-hyper-v.md) stöd för matriser.

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>Kan jag skydda installationen VMware VM ställer jag in med OVA-mallen?

Ytterligare komponenter (till exempel ett virusskyddsprogram) kan läggas till i OVA-mall så länge som kommunikation och brandväggen regler som krävs för den Azure Migrate-installationen på nytt vänster som är.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Om du vill skydda Azure Migrate-installation, vilka är de rekommendera Antivirus (AV)-undantag?

Du behöver följande mappar undantas från genomsökning av installationen:

- Mappen som innehåller binärfiler för tjänsten Azure Migrate. Undanta alla undermappar.
- %ProgramFiles%\ProfilerService  
- Azure Migrate webbprogram. Undanta alla undermappar.
- %SystemDrive%\inetpub\wwwroot
- Det lokala cacheminnet för databasen och loggfiler. Azure Migrate-tjänsten måste läs/skrivbehörighet till den här mappen.
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Vilka data som samlas in av Azure Migrate?

Azure Migrate-installationen samlar in metadata för lokala virtuella datorer, inklusive:

**Konfigurationsdata för den virtuella datorn**
- Visningsnamn för virtuell dator (på vCenter)
- VM inventering-sökväg (värd/kluster/mapp i vCenter)
- IP-adress
- MAC-adress
- Operativsystem
- Antal kärnor, diskar, nätverkskort
- Minnesstorlek, diskstorlekar

**Prestandadata för den virtuella datorn**
- CPU-användning
- Minnesanvändning
- För varje disk som är ansluten till den virtuella datorn:
  - Disk-lästa dataflöde
  - Dataflöde per disk för skrivningar
  - Diskläsningar-åtgärder per sekund
  - Disk skriver-åtgärder per sekund
- För varje nätverkskort som är anslutet till den virtuella datorn:
  - Nätverk in
  - Nätverk ut

TN dessutom om du distribuerar beroendemappning mappning beroendeagenter samlar in information som innehåller datorn FQDN, operativsystem, IP-adress och MAC-adress, processer som körs i den virtuella datorn och inkommande/utgående TCP-anslutningar för den virtuella datorn. Den här identifieringen är valfritt endast användas om du aktiverar beroendemappning för identifiering.

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Finns det en prestandaförsämring på analyserade ESXi värdmiljön?

Med kontinuerlig profilering av prestandadata, profiler för Azure Migrate installation lokala datorer för att mäta VM prestandadata. Detta påverkar nästan noll prestanda på ESXi-värdar, samt på vCenter-servern.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Var finns insamlade data lagrade och hur länge?

Data som samlas in av Azure Migrate-installationen lagras på Azure-plats som du anger när du skapar migration-projekt. Data lagras säkert i en Microsoft-prenumeration och tas bort när du tar bort Azure Migrate-projektet.

För visualisering av beroenden, om du installerar agenter på de virtuella datorerna, lagras data som samlas in av beroendeagenter i USA, en Log Analytics-arbetsyta som skapats i Azure-prenumeration. Dessa data tas bort när du tar bort logganalys-arbetsytan i din prenumeration. [Läs mer](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>Vad är mängden data som överförs från Azure Migrate under kontinuerlig Profileringen?

Mängden data som skickas till Azure Migrate varierar beroende på flera parametrar. För att ge ett vägledande nummer, skickar ett Azure Migrate-projekt med 10 virtuella datorer (var och en med en disk och ett nätverkskort), ungefär 50 MB per dag. Detta är en ungefärlig värde som ändras, baserat på antalet datapunkter för nätverkskorten och diskar (data som skickas är icke-linjära om antalet datorer, nätverkskort eller diskar ökar).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Är krypterade data i vila och under överföring?

Ja för båda. Metadata skickas på ett säkert sätt till Azure Migrate-tjänsten via internet, via https. Metadata som lagras i en [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest), och i [Azure blobblagring](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) i en Microsoft-prenumeration, och är krypterade i vila.

Data som samlas in av agenterna beroende är också krypterade i rörelse (säker HTTPS) och lagras i Log Analytics-arbetsytan i användarprenumerationen. Det är också krypterade i vila.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Hur Azure Migrate-installationen ska kommunicera med vCenter Server och tjänsten Azure Migrate?

Enheten som ansluter till vCenter-servern (port 443) med hjälp av autentiseringsuppgifterna som du angav när du ställer in installationen. Det frågor till vCenter-servern med hjälp av VMware PowerCLI att samla in metadata om datorer som hanteras av vCenter-servern. Den samlar in båda konfigurationsdata om virtuella datorer (kärnor, minne, diskar, nätverkskort osv), samt prestandahistoriken för varje virtuell dator för den senaste månaden. Insamlade metadata skickas sedan till den Azure Migrate Server-utvärdering (över internet via HTTPS) för utvärdering. 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Kan jag ansluta den samma installationen till flera vCenter-servrar?

Ja, en enda Azure Migrate-installation kan användas för att identifiera flera vCenter-servrar, men inte samtidigt. Du behöver köra identifieringar efter varandra.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>OVA-mallen som används av Site Recovery är integrerad med ova-filen som används av Azure Migrate?

Det finns för närvarande ingen integrering. Den. OVA-mallen i Site Recovery används för att ställa in en konfigurationsserver för Site Recovery för replikering av virtuella VMware-datorer/fysiska server. Den. OVA som används av Azure Migrate används för att identifiera virtuella VMware-datorer hanteras av en vCenter-server för bedömning och migrering.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Jag har ändrat storlek på min dator. Kan jag köra en utvärdering?
Azure Migrate-installation är kontinuerligt samlar in information om den lokala miljön. En utvärdering är dock en point-in-time-ögonblicksbild av lokala virtuella datorer. Om du ändrar inställningarna på en virtuell dator som du vill utvärdera, kan du använda alternativet ”Beräkna om” för att uppdatera utvärderingen med de senaste ändringarna.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Hur kan jag för att identifiera en miljö med flera organisationer i Azure Migrate?

För VMware, om du har en miljö som delas mellan klienter och du inte vill identifiera de virtuella datorerna i en klient i en annan klient prenumeration, skapa vCenter Server-autentiseringsuppgifter med endast åtkomst till dessa virtuella datorer som du vill identifiera. Använd sedan autentiseringsuppgifterna när drog identifiering i Azure Migrate-installationen.

För Hyper-V-identifieringen använder Hyper-V-värd autentiseringsuppgifter, om de virtuella datorerna delar samma Hyper-V-värden, det finns för närvarande inget sätt att separera identifieringen.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Hur många virtuella datorer kan identifieras med hjälp av en installation för migrering?

Du kan identifiera upp till 10 000 virtuella VMware-datorer och upp till 5 000 Hyper-V-datorer med hjälp av en installation för migrering.  Om du har flera virtuella datorer i din lokala miljö, lär du dig hur du skalar [Hyper-V](scale-hyper-v-assessment.md) och [VMware](scale-vmware-assessment.md) utvärdering.


## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Server-utvärdering

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate: Server-utvärdering stöd för utvärdering av fysiska servrar?

Nej, Azure Migrate stöder för närvarande inte bedömning av fysiska servrar. 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Behöver Azure Migrate vCenter Server för att identifiera en VMware-miljö?

Ja, Azure Migrate måste vCenter Server för att identifiera en VMware-miljö. Det stöder inte identifiering av ESXi-värdar som inte hanteras av vCenter-servern.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Vad är skillnaden mellan att använda Azure Migrate: Server-utvärdering och Map Toolkit?

Azure Migrate: Server-utvärderingen innebär en migreringsutvärdering som underlättar beredskapen för migrering och utvärdering av arbetsbelastningar för migrering till Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) har andra funktioner, till exempel migreringsplanering för nyare versioner av Windows-klient och serveroperativsystem och spåra användningen av programvara. Dessa scenarier kan du fortsätta att använda MAP Toolkit.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Hur är Azure Migrate: Server-utvärdering skiljer sig från Distributionshanteraren för Azure Site Recovery?

Azure Migrate: Server-utvärderingen är ett verktyg för migreringsplanering. Distributionshanteraren för Azure Site Recovery är en katastrofåterställning kapacitetsplaneringsverktyget.

- **Migrering från VMware/Hyper-V till Azure**: Om du planerar att migrera dina lokala servrar till Azure, kan du använda Azure Migrate: Server Assessment tool för planering av migreringsaktiviteter. Verktyget utvärderar lokala arbetsbelastningar och tillhandahåller vägledning, insikter och mekanismer för att hjälpa dig att migrera till Azure. När du är klar med din migreringsplan, kan du använda verktyg som Azure Migrate: Migreringen av servern, att migrera datorerna till Azure.
- **Haveriberedskap från VMware/Hyper-V till Azure**: Använd Site Recovery Deployment Planner för planeringen för katastrofåterställning för haveriberedskap till Azure med Site Recovery. Distributionshanteraren för site Recovery har en djupgående, utvärdering av Site Recovery-specifika för din lokala miljö. Den innehåller rekommendationer som krävs av Site Recovery för lyckas åtgärder, exempelvis replikering och redundans för virtuella datorer. 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Stöder Azure Migrate Enterprise Agreement EA-baserade kostnadsuppskattning?

Azure Migrate stöder för närvarande inte kostnadsuppskattning för [erbjudande för Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). Lösningen är att ange betala per användning som erbjudandet och manuellt ange procentvärdet (gäller för prenumerationen) i fältet ”Rabatt” i egenskaperna för utvärdering.

  ![Rabatt](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Vad är skillnaden mellan som lokalt storlek och prestandabaserad storleksändring?

- I som lokala storleksändringar behöver Azure Migrate tar inte hänsyn VM prestandadata. Den storlekar på virtuella datorer baserat på den lokala konfigurationen. -Prestandabaserade storleksändringar storlek är baserad på användningsdata.
- Till exempel om en lokal virtuell dator har 4 kärnor och 8 GB minne med 50% CPU-användning och 50% minnesanvändning, rekommenderar lokala storlek en Azure VM-SKU med 4 kärnor och 8GB minne. Prestandabaserad storleksändring rekommenderar emellertid en VM-SKU 2 kärnor och 4 GB, eftersom utnyttjandeprocent anses.
- Disk storlek beror på samma sätt på två utvärderingsegenskaperna – ändra storlek på kriterium och lagringstyp.
= Om storlekskriteriet är prestandabaserat och lagringstyp är automatisk, anses värden för IOPS och dataflöde på disken när du identifierar disktyp (Standard eller Premium).
- Om storlekskriteriet är prestandabaserat och lagringstypen premium, rekommenderas en premiumdisk. Premium-disk SKU väljs baserat på storleken på den lokala disken. Samma logik används för att disk storlek om storlekskriteriet är som lokala storlek och lagringstyp är antingen standard eller premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Vilken effekt har prestanda historik och percentil belastningen på storleksrekommendationer som?

Dessa egenskaper gäller endast för prestandabaserad storleksändring.

- Azure Migrate samlar in prestandahistoriken för lokala datorer och använder den för att rekommendera VM-storlek och disk-typ i Azure.
- Installationen profiler kontinuerligt den lokala miljön för att samla in användningsdata i realtid var 20: e sekund. Programmet samlar in stickprov var 20:e sekund och skapar en enskild datapunkt för varje kvart. För att skapa den enskilda datapunkten väljer programmet det högsta värdet från alla 20-sekundersstickprov och skickar det till Azure.
- När du skapar en utvärdering i Azure (baserat på prestanda, varaktighet och prestandavärde historik: e percentilen), Azure Migrate beräknar effektivt utnyttjande värde och använder den för storleksändringar.
- Till exempel om du ställer in varaktigheten ska vara en dag och percentilvärdet till 95: e percentilen, Azure Migrate använder 15 minut exempel punkter som skickats av insamlaren för den sista dagen, sorterade i stigande ordning och hämtar percentilvärdet för den 95: e som de effektiv användning.
- 95-procentigt percentilvärde säkerställer att du ignorerar några avvikare som kan inträffa om du använder den 99: e percentilen. Om du vill välja den högsta användningen för perioden och inte vill missa några avvikande värden bör du välja den 99:e percentilen.

### <a name="what-is-dependency-visualization"></a>Vad är beroendevisualisering?

Beroendevisualisering gör det möjligt för dig att utvärdera grupper med virtuella datorer för migrering med större tillförlitlighet. Den kontroll datorberoenden innan du kör en utvärdering. Visualisering av beroenden kan du se till att inget kvar och undvika oväntade avbrott när du migrerar till Azure. Azure Migrate använder Tjänstkarta-lösningen i Azure Monitor-loggar att aktivera beroendevisualisering.

> [!NOTE]
> Funktionen för beroendevisualisering är inte tillgänglig i Azure Government.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Behöver jag betala för att använda funktionen beroendevisualisering?

Nej. [Läs mer](https://azure.microsoft.com/pricing/details/azure-migrate/) om priser för Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Måste jag installera något för visualisering av beroenden?

Om du vill använda visualisering av beroenden som du behöver hämta och installera agenter på varje lokal dator som du vill utvärdera.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) måste installeras på varje dator.
- Den [beroendeagenten](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) måste installeras på varje dator.
- Dessutom om du har datorer som saknar Internetanslutning kan behöva du hämta och installera Log Analytics-gatewayen på dem.

Du behöver inte dessa agenter om du inte använder beroendevisualisering.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kan jag använda en befintlig arbetsyta för visualisering av beroenden?

Ja, kan du bifoga en befintlig arbetsyta i migreringsprojektet och utnyttja dessa för visualisering av beroenden. [Läs mer](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Kan jag exportera visualisering beroenderapport?

Nej, visualiseringen av beroenden kan inte exporteras. Men eftersom Azure Migrate använder Tjänstkarta för visualisering av beroenden, du kan använda den [Service Map REST API: er](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) att hämta beroendena i json-format.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Hur kan jag automatisera installationen av Microsoft Monitoring Agent (MMA) och beroendeagenten?

[Använd det här skriptet](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) för installation av agenter. [Följ de här instruktionerna](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) installera MMA med hjälp av kommandoraden eller automation. MMA, utnyttja [det här skriptet](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Du kan använda distributionsverktyg som System Center Configuration Manager förutom skript, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) osv. för att distribuera agenter.

### <a name="what-operating-systems-are-supported-by-mma"></a>Vilka operativsystem stöds av MMA?

- [Granska](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) lista över Windows-operativsystem som stöds av MMA.
- [Granska] https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) listan med Linux-operativsystem som stöds av MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Vilka är de operativsystem som stöds av beroendeagenten?

[Granska](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) Windows-operativsystem som stöds av beroendeagenten.
[Granska](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) listan med Linux-operativsystem som stöds av beroendeagenten.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Kan jag visualisera beroenden i Azure Migrate i mer än en timme?
Nej, kan du visualisera beroenden för upp till en timme. Du kan gå tillbaka till ett visst datum i historiken för upp till den senaste månaden, men maximal varaktighet för visualisering är en timme. Exempel: du kan använda tidslängd i beroendekartan för att visa beroenden för igår, men kan bara visa för ett fönster med en timme. Du kan dock använda Azure Monitor-loggar till [frågedata beroende](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) över en längre period.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>Stöds visualisering av beroenden för grupper med fler än tio virtuella datorer?
Du kan [visualisera beroenden för grupper](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) med upp till tio virtuella datorer. Om du har en grupp med fler än tio virtuella datorer, vi rekommenderar att du dela upp gruppen i mindre grupper och sedan visualisera beroenden.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Servermigrering

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Hur är Azure Migrate: Servermigrering skiljer sig från Azure Site Recovery?

Azure Migrate: Servermigrering utnyttjar Site Recovery-replikeringsmotorn för agentbaserad migrering av servrar till Azure.
## <a name="next-steps"></a>Nästa steg
Läs den [Azure Migrate översikt](migrate-services-overview.md)
