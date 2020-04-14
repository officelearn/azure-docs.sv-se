---
title: Vanliga frågor om VMware-haveriberedskap med Azure Site Recovery
description: Få svar på vanliga frågor om haveriberedskap av lokala virtuella datorer med VMware till Azure med hjälp av Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: d551cef7037c0b6d7286cbb4b70d8f7a8f7f5cae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259518"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Vanliga frågor om replikering av VMware till Azure

Den här artikeln besvarar vanliga frågor som kan komma upp när du distribuerar haveriberedskap av lokala virtuella VMware-datorer (VMs) till Azure.

## <a name="general"></a>Allmänt

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Vad behöver jag för VMware VM-haveriberedskap?

[Lär dig mer om komponenterna i](vmware-azure-architecture.md) katastrofåterställning av virtuella datorer med VMware.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Kan jag använda Site Recovery för att migrera virtuella virtuella datorer med VMware till Azure?

Ja. Förutom att använda Site Recovery för att ställa in fullständig haveriberedskap för virtuella datorer med VMware kan du också använda Site Recovery för att migrera lokala virtuella virtuella datorer med VMware till Azure. I det här fallet replikerar du lokala virtuella datorer med VMware till Azure Storage. Sedan kan du växla över från lokalt till Azure. Efter redundans är dina appar och arbetsbelastningar tillgängliga och körs på virtuella Azure-datorer. Processen är som att konfigurera fullständig haveriberedskap, förutom att i en migrering kan du inte växla tillbaka från Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver mitt Azure-konto behörigheter för att skapa virtuella datorer?

Om du är prenumerationsadministratör har du de replikeringsbehörigheter du behöver. Om du inte är administratör behöver du behörighet för att kunna vidta följande åtgärder:

- Skapa en virtuell Azure-dator i resursgruppen och det virtuella nätverket som du anger när du konfigurerar Platsåterställning.
- Skriv till det valda lagringskontot eller den hanterade disken baserat på din konfiguration.

[Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) om nödvändiga behörigheter.

### <a name="what-applications-can-i-replicate"></a>Vilka program kan jag replikera?

Du kan replikera alla appar eller arbetsbelastningar som körs på en virtuell virtuell vmware-dator som uppfyller [replikeringskraven](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery stöder programmedveten replikering, så att appar kan överföras och återställas till ett intelligent tillstånd.
- Site Recovery integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory. Det har också ett nära samarbete med ledande leverantörer, inklusive Oracle, SAP, IBM och Red Hat.

[Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Kan jag använda en gästoperosserverlicens på Azure?

Ja, Microsoft Software Assurance-kunder kan använda [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) för att spara på licenskostnader för Windows Server-datorer som migreras till Azure eller för att använda Azure för haveriberedskap.

## <a name="security"></a>Säkerhet

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Vilken åtkomst till VMware-servrar behöver Site Recovery?

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Konfigurera en virtuell virtuell dator med VMware som kör konfigurationsservern för platsåterställning.
- Identifiera virtuella datorer automatiskt för replikering.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Vilken åtkomst till virtuella datorer med VMware behöver Site Recovery?

- Om du vill replikera måste en virtuell VMware-dator ha tjänsten Site Recovery Mobility installerad och körs. Du kan distribuera verktyget manuellt eller ange att Site Recovery gör en push-installation av tjänsten när du aktiverar replikering för en virtuell dator.
- Under replikering kommunicerar virtuella datorer med site recovery enligt följande:
    - Virtuella datorer kommunicerar med konfigurationsservern på HTTPS-port 443 för replikeringshantering.
    - Virtuella datorer skickar replikeringsdata till processservern på HTTPS-port 9443. (Den här inställningen kan ändras.)
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar virtuella datorer med varandra via port 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till platsåterställning?

Nej, site recovery avlyssnar inte replikerade data och har ingen information om vad som körs på dina virtuella datorer. Replikeringsdata utbyts mellan VMware-hypervisorer och Azure Storage. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.

Site Recovery är certifierad för ISO 27001:2013 och 27018, HIPAA och DPA. Det är i färd med SOC2 och FedRAMP JAB bedömningar.

## <a name="pricing"></a>Prissättning

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hur beräknar jag ungefärliga avgifter för VMware-haveriberedskap?

Använd [priskalkylatorn](https://aka.ms/asr_pricing_calculator) för att uppskatta kostnaderna när du använder Site Recovery.

Om du vill ha en detaljerad uppskattning av kostnaderna kör du distributionsplaneraren för [VMware](https://aka.ms/siterecovery_deployment_planner) och använder [kostnadsuppskattningsrapporten](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Finns det någon skillnad i kostnad mellan replikera till lagring eller direkt till hanterade diskar?

Hanterade diskar debiteras något annorlunda än lagringskonton. [Läs mer](https://azure.microsoft.com/pricing/details/managed-disks/) om priser för hanterade diskar.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Finns det någon skillnad i kostnad vid replikera till Allmänt ändamål v2 lagringskonto?

Du kommer vanligtvis att se en ökning av transaktionskostnaden som uppkommit på GPv2-lagringskonton eftersom Azure Site Recovery är transaktioner tunga. [Läs mer](../storage/common/storage-account-upgrade.md#pricing-and-billing) för att uppskatta förändringen.

## <a name="mobility-service"></a>Mobilitetstjänsten

### <a name="where-can-i-find-the-mobility-service-installers"></a>Var hittar jag mobilitetsserviceinstallationsarna?

Installationsprogrammet finns i mappen %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository på konfigurationsservern.

## <a name="how-do-i-install-the-mobility-service"></a>Hur installerar jag mobilitetstjänsten?

Installera tjänsten på varje virtuell dator som du vill replikera med en av flera metoder:

- [Push-installation](vmware-physical-mobility-service-overview.md#push-installation)
- [Manuell installation](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) från användargränssnittet eller PowerShell
- Distribution med hjälp av ett distributionsverktyg som [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Hanterade diskar

### <a name="where-does-site-recovery-replicate-data-to"></a>Var replikerar Site Recovery data till?

Site Recovery replikerar lokala virtuella datorer med VMware och fysiska servrar till hanterade diskar i Azure.

- Site Recovery process server skriver replikeringsloggar till ett cachelagringskonto i målregionen.
- Dessa loggar används för att skapa återställningspunkter på Azure-hanterade diskar som har prefix för **asrseeddisk**.
- När redundans inträffar används återställningspunkten som du väljer för att skapa en ny målhanterad disk. Den här hanterade disken är kopplad till den virtuella datorn i Azure.
- Virtuella datorer som tidigare replikerats till ett lagringskonto (före mars 2019) påverkas inte.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Kan jag replikera nya datorer till lagringskonton?

Nej. Från och med mars 2019, i Azure-portalen, kan du replikera endast till Azure-hanterade diskar.

Replikering av nya virtuella datorer till ett lagringskonto är endast tillgängligt med PowerShell eller REST API (version 2018-01-10 eller 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Vilka är fördelarna med att replikera till hanterade diskar?

[Läs mer](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery förenklar haveriberedskap med hanterade diskar.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Kan jag ändra typen av hanterad disk när en dator har skyddats?

Ja, du kan enkelt [ändra typen av hanterad disk](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) för pågående replikeringar. Innan du ändrar typen bör du se till att ingen URL för signatur för delad åtkomst genereras på den hanterade disken:

1. Gå till resursen **Hanterad disk** på Azure-portalen och kontrollera om du har en URL-banner för signatur med delad åtkomst på **bladet Översikt.**
1. Om banderollen finns väljer du den för att avbryta den pågående exporten.
1. Ändra diskens typ inom de närmaste minuterna. Om du ändrar typen av hanterad disk väntar du på att nya återställningspunkter ska genereras av Azure Site Recovery.
1. Använd de nya återställningspunkterna för alla test redundans- eller redundansväxlingar i framtiden.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Kan jag växla replikering från hanterade diskar till ohanterade diskar?

Nej. Det går inte att byta från hanterad till ohanterad.

## <a name="replication"></a>Replikering

### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är kraven för replikerad virtuell dator?

[Läs mer](vmware-physical-azure-support-matrix.md#replicated-machines) om supportkrav för virtuella datorer med VMware och fysiska servrar.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Replikering är kontinuerligt när VMware-datorer replikeras till Azure.

### <a name="can-i-extend-replication"></a>Kan jag förlänga replikeringen?

Utökad eller länkad replikering stöds inte. Begär den här funktionen i [feedbackforumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en första offlinereplikering?

Offlinereplikering stöds inte. Begär den här funktionen i [feedbackforumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Vad är asrseeddisk?

För varje källdisk replikeras data till en hanterad disk i Azure. Den här disken har prefixet **asrseeddisk**. Den lagrar kopian av källdisken och alla ögonblicksbilder återställningspunkt.

### <a name="can-i-exclude-disks-from-replication"></a>Kan jag utesluta diskar från replikering?

Ja, du kan utesluta diskar.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Kan jag replikera virtuella datorer som har dynamiska diskar?

Dynamiska diskar kan replikeras. Operativsystemets disk måste vara en enkel disk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Om jag använder replikeringsgrupper för konsekvens med flera virtuella datorer, kan jag lägga till en ny virtuell dator i en befintlig replikeringsgrupp?

Ja, du kan lägga till nya virtuella datorer i en befintlig replikeringsgrupp när du aktiverar replikering för dem. Emellertid:

- Du kan inte lägga till en virtuell dator i en befintlig replikeringsgrupp när replikeringen har påbörjats.
- Du kan inte skapa en replikeringsgrupp för befintliga virtuella datorer.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan jag ändra virtuella datorer som replikerar genom att lägga till eller ändra storlek på diskar?

För VMware-replikering till Azure kan du ändra diskstorleken för virtuella käll-datorer. Om du vill lägga till nya diskar måste du lägga till disken och återanvändningsskydd för den virtuella datorn.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Kan jag migrera lokala datorer till en ny vCenter-server utan att påverka pågående replikering?

Nej. En ändring av VMware Vcenter eller migrering påverkar pågående replikering. Konfigurera Site Recovery med den nya vCenter-servern och aktivera replikering för datorer igen.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Kan jag replikera till ett cache- eller mållagringskonto som har ett virtuellt nätverk (med Azure-brandväggar) konfigurerat på det?

Nej, site recovery stöder inte replikering till Azure Storage i virtuella nätverk.

## <a name="component-upgrade"></a>Uppgradering av komponenter

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Min version av Mobility-tjänstagenten eller konfigurationsservern är gammal och uppgraderingen misslyckades. Vad gör jag nu?

Site Recovery följer N-4-supportmodellen. [Läs mer](https://aka.ms/asr_support_statement) om hur du uppgraderar från mycket gamla versioner.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Var hittar jag viktig information och samlade uppdateringar för Azure Site Recovery?

[Lär dig mer om nya uppdateringar](site-recovery-whats-new.md)och få [samlad information](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Var hittar jag uppgraderingsinformation för haveriberedskap till Azure?

[Läs mer om att uppgradera](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Måste jag starta om källdatorer för varje uppgradering?

En omstart rekommenderas men inte obligatorisk för varje uppgradering. [Läs mer](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Konfigurationsserver

### <a name="what-does-the-configuration-server-do"></a>Vad gör konfigurationsservern?

Konfigurationsservern kör de lokala site recovery-komponenterna, inklusive:

- Själva konfigurationsservern. Servern samordnar kommunikationen mellan lokala komponenter och Azure och hanterar datareplikering.
- Processservern, som fungerar som en replikeringsgateway. Den här servern:
    1. Tar emot replikeringsdata.
    2. Optimerar data med cachelagring, komprimering och kryptering.
    3. Skickar data till Azure Storage.
  Processservern gör också en push-installation av mobilitetstjänsten på virtuella datorer och utför automatisk identifiering av lokala virtuella virtuella datorer.
- Huvudmålservern, som hanterar replikeringsdata under återställning efter fel från Azure.

[Läs mer](vmware-azure-architecture.md) om konfigurationsserverns komponenter och processer.

### <a name="where-do-i-set-up-the-configuration-server"></a>Var konfigurerar jag konfigurationsservern?

Du behöver en enda, mycket tillgänglig, lokal virtuell vmware-dator för konfigurationsservern. Installera konfigurationsservern på en fysisk dator för att återställa en fysisk server.

### <a name="what-do-i-need-for-the-configuration-server"></a>Vad behöver jag för konfigurationsservern?

Granska [förutsättningarna](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan jag konfigurera konfigurationsservern manuellt i stället för att använda en mall?

Vi rekommenderar att du [skapar den virtuella konfigurationsservern](vmware-azure-deploy-configuration-server.md) med den senaste versionen av OVF-mallen (Open Virtualization Format). Om du inte kan använda mallen (till exempel om du inte har tillgång till VMware-servern) [hämtar du](physical-azure-set-up-source.md) installationsfilen från portalen och konfigurerar konfigurationsservern.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Kan en konfigurationsserver replikeras till mer än en region?

Nej. Om du vill replikera till mer än en region behöver du en konfigurationsserver i varje region.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan jag vara värd för en konfigurationsserver i Azure?

Även om det är möjligt måste den virtuella Azure-datorn som kör konfigurationsservern kommunicera med din lokala VMware-infrastruktur och virtuella datorer. Den här kommunikationen lägger till svarstid och påverkar pågående replikering.

### <a name="how-do-i-update-the-configuration-server"></a>Hur uppdaterar jag konfigurationsservern?

[Lär dig](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) hur du uppdaterar konfigurationsservern.

- Du hittar den senaste uppdateringsinformationen på [sidan Azure-uppdateringar](https://azure.microsoft.com/updates/?product=site-recovery).
- Du kan ladda ner den senaste versionen från portalen. Du kan också hämta den senaste versionen av konfigurationsservern direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Om din version är fler än fyra versioner äldre än den aktuella versionen läser du [supportsatsen](https://aka.ms/asr_support_statement) för uppgraderingsvägledning.

### <a name="should-i-back-up-the-configuration-server"></a>Ska jag säkerhetskopiera konfigurationsservern?

Vi rekommenderar att du tar regelbundna schemalagda säkerhetskopior av konfigurationsservern.

- För en lyckad återställning efter fel måste den virtuella datorn som misslyckades tillbaka finnas i konfigurationsserverdatabasen.
- Konfigurationsservern måste köras och vara ansluten.
- [Läs mer](vmware-azure-manage-configuration-server.md) om vanliga konfigurationsserverhanteringsuppgifter.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Kan jag hämta och installera MySQL manuellt när jag konfigurerar konfigurationsservern?

Ja. Ladda ner MySQL och placera den i mappen C:\Temp\ASRSetup. Installera den sedan manuellt. När du konfigurerar den virtuella konfigurationsservern och accepterar villkoren visas MySQL som **Redan installerat** i Hämta **och installera**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kan jag undvika att ladda ner MySQL men låta Site Recovery installera det?

Ja. Hämta MySQL-installationsprogrammet och placera den i mappen C:\Temp\ASRSetup. När du konfigurerar den virtuella konfigurationsservern godkänner du villkoren och väljer **Hämta och installera**. Portalen kommer att använda installationsprogrammet som du har lagt till för att installera MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Kan jag använda den virtuella konfigurationsservern för något annat?

Nej. Använd endast den virtuella datorn för konfigurationsservern.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Kan jag klona en konfigurationsserver och använda den för orkestrering?

Nej. Konfigurera en ny konfigurationsserver för att undvika registreringsproblem.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Kan jag ändra valvet där konfigurationsservern är registrerad?

Nej. När ett valv har associerats med konfigurationsservern kan det inte ändras. [Läs mer](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) om hur du registrerar en konfigurationsserver med ett annat valv.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Kan jag använda samma konfigurationsserver för haveriberedskap för både virtuella datorer med VMware och fysiska servrar?

Ja, men observera att den fysiska datorn bara kan skickas tillbaka till en virtuell virtuell VMware-dator.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Var kan jag hämta lösenfrasen för konfigurationsservern?

[Läs om](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) hur du laddar ned lösenfrasen.

### <a name="where-can-i-download-vault-registration-keys"></a>Var kan jag hämta valvregistreringsnycklar?

I valvet för återställningstjänster väljer du **Konfigurationsservrar** i**Hantering av**infrastruktur för **platsåterställning** > . Välj sedan Hämta **registreringsnyckel** i **Servrar**för att hämta autentiseringsfilen för valvautentiseringsuppgifter.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Kan en enda konfigurationsserver användas för att skydda flera vCenter-instanser?

Ja, en enda konfigurationsserver kan skydda virtuella datorer i flera virtuellacenter.  Det finns ingen gräns för hur många vCenter-instanser som kan läggas till på konfigurationsservern, men gränserna för hur många virtuella datorer som en enda konfigurationsserver kan skydda gäller.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Kan en enda konfigurationsserver skydda flera kluster inom vCenter?

Ja, Azure Site Recovery kan skydda virtuella datorer i olika kluster.

## <a name="process-server"></a>Bearbeta server

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Varför kan jag inte välja processservern när jag aktiverar replikering?

Uppdateringar i version 9.24 och senare visar nu [hälsotillståndet för processservern när du aktiverar replikering](vmware-azure-enable-replication.md#enable-replication). Den här funktionen hjälper till att undvika begränsning av processserver och minimera användningen av felaktiga processservrar.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Hur uppdaterar jag processservern till version 9.24 eller senare för korrekt hälsoinformation?

Från och med [version 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)har fler aviseringar lagts till för att ange hälsotillståndet för processservern. [Uppdatera komponenterna för webbplatsåterställning till version 9.24 eller senare](service-updates-how-to.md#links-to-currently-supported-update-rollups) så att alla aviseringar genereras.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Hur kan jag säkerställa hög tillgänglighet för processservern?

Genom att konfigurera mer än en processserver ger designen flexibilitet att flytta skyddade datorer från en felfritt processserver till arbetsprocessserver. Förflyttning av en dator från en processserver till en annan måste initieras uttryckligen/manuellt via de definierade stegen här: [att flytta virtuella datorer mellan processservrar](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Kan jag använda den lokala processservern för återställning efter fel?

Vi rekommenderar starkt att du skapar en processserver i Azure för återställning efter fel, för att undvika dataöverföringsfördämningar. Om du dessutom separerar käll-virtuella datorer-nätverket med Azure-nätverket i konfigurationsservern är det viktigt att använda processservern som skapats i Azure för återställning efter fel.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Kan jag behålla IP-adressen vid redundans?

Ja, du kan behålla IP-adressen vid redundans. Se till att du anger mål-IP-adressen i **beräknings- och nätverksinställningarna** för den virtuella datorn före redundans. Stäng också av datorer vid tidpunkten för redundans för att undvika IP-adresskonflikter under återställning av fel.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Kan jag ändra mål-VM-storlek eller VM-typ före redundans?

Ja, du kan ändra typ eller storlek på den virtuella datorn när som helst före redundans. I portalen använder du **beräknings- och nätverksinställningarna** för den replikerade virtuella datorn.

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återhämta mig?

För VMware till Azure är den äldsta återställningspunkten du kan använda 72 timmar.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur kommer jag åt virtuella Azure-datorer efter redundans?

Efter redundans kan du komma åt virtuella Azure-datorer via en säker internetanslutning, via en VPN från plats till plats eller via Azure ExpressRoute. För att ansluta måste du förbereda flera saker. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Är data som inte överskrids flexibel?

Azure är utformat med flexibilitet i fokus. Site Recovery är konstruerad för redundans till ett sekundärt Azure-datacenter, som krävs av Azure service-level agreement (SLA). När redundans inträffar ser vi till att dina metadata och valv finns kvar i samma geografiska region som du har valt för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

[Redundans](site-recovery-failover.md) är inte automatiskt. Du startar en redundans genom att göra en enda markering i portalen, eller så kan du använda [PowerShell](/powershell/module/az.recoveryservices) för att utlösa en redundans.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag växla tillbaka till en annan plats?

Ja. Om du misslyckades med Azure kan du växla tillbaka till en annan plats om den ursprungliga inte är tillgänglig. [Läs mer](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Varför behöver jag en VPN eller ExpressRoute med privat peering för att misslyckas tillbaka?

När du växlar tillbaka från Azure kopieras data från Azure tillbaka till den lokala virtuella datorn och privat åtkomst krävs.


## <a name="automation-and-scripting"></a>Automatisering och skript

### <a name="can-i-set-up-replication-with-scripting"></a>Kan jag ställa in replikering med skript?

Ja. Du kan automatisera arbetsflöden för webbplatsåterställning med hjälp av rest-API:et, PowerShell eller Azure SDK. [Läs mer](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestanda och kapacitet

### <a name="can-i-throttle-replication-bandwidth"></a>Kan jag begränsa replikeringsbandbredden?

Ja. [Läs mer](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Nästa steg

- [Granska](vmware-physical-azure-support-matrix.md) supportkrav.
- [Ställ in](vmware-azure-tutorial.md) VMware till Azure-replikering.
