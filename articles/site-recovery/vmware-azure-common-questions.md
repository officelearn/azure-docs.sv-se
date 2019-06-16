---
title: Vanliga frågor om VMware till Azure-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Få svar på vanliga frågor om haveriberedskap för lokala virtuella VMware-datorer till Azure med hjälp av Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 05/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 59be8e0585f0bedcafc868ee42f5113509c9c4ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417770"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Vanliga frågor om VMware till Azure replikering

Den här artikeln innehåller vanliga frågor och svar som kan uppstå när du distribuerar haveriberedskap för lokala virtuella VMware-datorer (VM) till Azure.

## <a name="general"></a>Allmänt

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Vad behöver jag för haveriberedskap för VMware VM?

[Lär dig mer om komponenterna som ingår](vmware-azure-architecture.md) i haveriberedskap för virtuella VMware-datorer.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Kan jag använda Site Recovery för att migrera virtuella VMware-datorer till Azure?

Ja. Förutom att använda Site Recovery för att ställa in fullständig haveriberedskap för virtuella VMware-datorer kan använda du även Site Recovery för att migrera lokala virtuella VMware-datorer till Azure. I det här scenariot kan replikera du lokala virtuella VMware-datorer till Azure Storage. Sedan kan växla du över från den lokala till Azure. Efter redundansväxlingen är dina appar och arbetsbelastningar tillgängliga och körs på virtuella Azure-datorer. Processen är t.ex. Ange fullständig haveriberedskap, förutom att du kan inte i en migrering från Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver Mina Azure-konto behörighet att skapa virtuella datorer?

Om du är administratör för prenumerationen har Replikeringsbehörighet som du behöver. Om du inte är administratör, behöver du behörighet att utföra följande åtgärder:

- Skapa en Azure virtuell dator i resursen i gruppen och virtuella nätverk du att du anger när du konfigurerar Site Recovery.
- Skriva till det valda lagringskontot eller hanterad disk baserat på din konfiguration.

[Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) om behörigheter som krävs.

### <a name="what-applications-can-i-replicate"></a>Vilka program kan jag replikera?

Du kan replikera en app eller arbetsbelastning som körs på en VMware-VM som uppfyller den [replikeringskraven](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery har stöd för Programmedveten replikering så att appar kan växlas över och återställde till en intelligent tillstånd.
- Site Recovery integrerar med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory. Den samarbetar även nära med ledande leverantörer, som Oracle, SAP, IBM och Red Hat.

[Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Kan jag använda en serverlicens för gäst-OS i Azure?

Ja, Microsoft Software Assurance-kunder kan använda [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) att spara på licenskostnaden för Windows Server-datorer som har migrerats till Azure eller att använda Azure för haveriberedskap.

## <a name="security"></a>Säkerhet

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Vilken åtkomst till VMware-servrar behöver Site Recovery?

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Konfigurera en VMware-VM som kör Site Recovery konfigurationsserver.
- Automatiskt identifiera virtuella datorer för replikering.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Vilken åtkomst till virtuella VMware-datorer behöver Site Recovery?

- Om du vill replikera, ha en VMware VM i Site Recovery-mobilitetstjänsten installerad och igång. Du kan distribuera verktyget manuellt eller du kan ange att Site Recovery göra en push-installation av tjänsten när du aktiverar replikering för en virtuell dator.
- Under replikering kommunicerar virtuella datorer med Site Recovery på följande sätt:
    - Virtuella datorer kommunicerar med konfigurationsservern på HTTPS-port 443 för replikeringshantering.
    - Virtuella datorer skickar replikeringsdata till processervern på port HTTPS 9443. (Den här inställningen kan ändras.)
    - Om du aktiverar konsekvens för flera datorer, kommunicera virtuella datorer med varandra på port 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till Site Recovery?

Nej, Site Recovery komma åt inte replikerade data och har inte någon information om vad som körs på dina virtuella datorer. Replikeringsdata utväxlas mellan VMware-hypervisorer och Azure Storage. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är certifierat för ISO 27001: 2013 och 27018, HIPAA och DPA. Det håller SOC2 och FedRAMP JAB-utvärderingar.

## <a name="pricing"></a>Prissättning

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hur jag för att beräkna ungefärliga kostnaderna för VMware-haveriberedskap?

Använd den [priskalkylator](https://aka.ms/asr_pricing_calculator) uppskatta kostnader när du använder Site Recovery.

För en detaljerad uppskattning av kostnaderna kör distributionsplaneringsverktyget för [VMware](https://aka.ms/siterecovery_deployment_planner) och använda den [kostnad rapporten kostnadsuppskattning](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Finns det någon skillnad i kostnaden för mellan replikerar till lagring eller direkt till hanterade diskar?

Hanterade diskar debiteras lite annorlunda från storage-konton. [Läs mer](https://azure.microsoft.com/pricing/details/managed-disks/) om priser för hanterad disk.

## <a name="mobility-service"></a>Mobilitetstjänsten

### <a name="where-can-i-find-the-mobility-service-installers"></a>Var hittar jag Mobility service installationsprogram?

Installationsprogrammen är i mappen %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository på konfigurationsservern.

## <a name="how-do-i-install-the-mobility-service"></a>Hur gör jag för att installera mobilitetstjänsten?

På varje virtuell dator som du vill replikera, installera tjänsten med en eller flera metoder:

- [Push-installation](vmware-physical-mobility-service-overview.md#push-installation)
- [Manuell installation](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) från Användargränssnittet eller PowerShell
- Distribution genom att använda ett distributionsverktyg som [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Hanterade diskar

### <a name="where-does-site-recovery-replicate-data-to"></a>Där replikerar data till av Site Recovery?

Site Recovery replikerar lokala virtuella VMware-datorer och fysiska servrar till managed disks i Azure.

- Processervern för Site Recovery skriver replikeringsloggar till ett cachelagringskonto i målregionen.
- De här loggarna används för att skapa återställningspunkter på Azure-hanterade diskar som har prefixet för **asrseeddisk**.
- Vid redundans används den återställningspunkt som du väljer att skapa en ny hanterad disk för målet. Den här hanterade disken är ansluten till den virtuella datorn i Azure.
- Virtuella datorer som tidigare har replikerats till ett lagringskonto (före mars 2019) påverkas inte.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Kan jag replikera nya datorer till storage-konton?

Nej. Från och med mars 2019 i Azure-portalen kan du replikera endast till Azure hanterade diskar.

Replikering av nya virtuella datorer till ett lagringskonto är bara tillgänglig om du använder PowerShell eller REST-API (version 2018-01-10 eller 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Vilka är fördelarna med att replikera till managed disks?

[Lär dig hur](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery förenklar haveriberedskapen med hanterade diskar.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Kan jag ändra typ av hanterad disk när en dator skyddas?

Ja, du kan enkelt [ändra typen av hanterad disk](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) för pågående replikeringar. Innan du ändrar typen, se till att ingen signatur för delad åtkomst som URL: en genereras på en hanterad disk:

1. Gå till den **Managed Disk** resurs på Azure portal och kontrollera om det finns en delad åtkomst signatur URL: en banderoll den **översikt** bladet.
1. Om popup-meddelandet är tillgängligt, väljer du den för att avbryta pågående exporten.
1. Ändra typ av disk inom några minuter. Om du ändrar typ av hanterad disk, vänta tills nya återställningspunkter som ska genereras av Azure Site Recovery.
1. Använd de nya återställningspunkterna för redundanstest eller redundans i framtiden.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Kan jag byta replikeringen från hanterade diskar till ohanterade diskar?

Nej. Växla från hanterad till ohanterad stöds inte.

## <a name="replication"></a>Replikering

### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är kraven för replikerade virtuella datorer?

[Läs mer](vmware-physical-azure-support-matrix.md#replicated-machines) om kraven för stöd för virtuella VMware-datorer och fysiska servrar.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Replikeringen är kontinuerlig när du replikerar virtuella VMware-datorer till Azure.

### <a name="can-i-extend-replication"></a>Kan jag utöka replikeringen?

Utökad eller länkad replikering stöds inte. Begär den här funktionen i den [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en offlinereplikering första?

Offlinereplikering stöds inte. Begär den här funktionen i den [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Vad är asrseeddisk?

För varje källdisken replikeras data till en hanterad disk i Azure. Den här disken har prefixet för **asrseeddisk**. Registret lagrar kopian av källdisken och alla ögonblicksbilder för återställning-punkt.

### <a name="can-i-exclude-disks-from-replication"></a>Kan jag undanta diskar från replikering?

Ja, kan du utesluta diskar.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Kan jag replikera virtuella datorer som har dynamiska diskar?

Dynamiska diskar kan replikeras. Operativsystemdisken måste vara en standarddisk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Om jag använder replikeringsgrupper för konsekvens för flera datorer, kan jag lägga till en ny virtuell dator till en befintlig replikeringsgrupp?

Ja, kan du lägga till nya virtuella datorer till en befintlig replikeringsgrupp när du aktiverar replikering för dessa. Men:

- Du kan inte lägga till en virtuell dator till en befintlig replikeringsgrupp när replikering har startat.
- Du kan inte skapa en replikeringsgrupp för befintliga virtuella datorer.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan jag ändra virtuella datorer som replikerar genom att lägga till eller ändra storlek på diskar?

Du kan ändra diskens storlek för VMware-replikering till Azure. Om du vill lägga till nya diskar måste du lägga till disken och återaktivera skyddet för den virtuella datorn.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Kan jag migrera lokala datorer till en ny vCenter-Server utan att påverka pågående replikering?

Nej. En ändring av VMware Vcenter eller migrering kommer att påverka pågående replikering. Ställ in Site Recovery med nya vCenter-servern och aktivera replikering för datorer igen.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Kan jag replikera till en cache- eller målinstansen storage-konto som har konfigurerat ett virtuellt nätverk (med Azure-brandväggar) på den?

Nej, Site Recovery stöder inte replikering till Azure Storage på virtuella nätverk.

## <a name="component-upgrade"></a>Komponent-uppgradering

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Min Mobility services-agenten eller configuration server-version är gammal och min uppgraderingen misslyckades. Vad gör jag nu?

Site Recovery följer supportmodell N-4. [Läs mer](https://aka.ms/asr_support_statement) om hur du uppgraderar från mycket äldre versioner.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Var kan jag hitta viktig information och samlade uppdateringar för Azure Site Recovery?

[Lär dig mer om nya uppdateringar](site-recovery-whats-new.md), och [Hämta samlad information](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Var hittar jag information om uppgradering för haveriberedskap till Azure?

[Läs om hur du uppgraderar](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Behöver jag omstart källdatorer för varje uppgradering?

En omstart är rekommenderas, men är inte obligatoriskt för varje uppgradering. [Läs mer](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Konfigurationsservern

### <a name="what-does-the-configuration-server-do"></a>Vad gör configuration server?

Konfigurationsservern körs lokalt Site Recovery-komponenter, inklusive:

- Konfigurationsservern själva. Servern samordnar kommunikationen mellan lokala komponenter och Azure och hanterar datareplikering.
- Processervern, som fungerar som en replikeringsgateway. Den här servern:
    1. Tar emot replikeringsdata.
    2. Optimerar data med cachelagring, komprimering och kryptering.
    3. Skickar data till Azure Storage.
  Processervern även gör en push-installation av Mobilitetstjänsten på virtuella datorer och utför automatisk identifiering av lokala virtuella VMware-datorer.
- Master target-server som hanterar replikeringsdata vid återställning från Azure.

[Läs mer](vmware-azure-architecture.md) om konfigurationsserverns komponenter och processer.

### <a name="where-do-i-set-up-the-configuration-server"></a>Var konfigurerar jag configuration server?

Du behöver en enda, med hög tillgänglighet, lokal VMware VM för konfigurationsservern. Installera konfigurationsservern för haveriberedskap för fysisk server, på en fysisk dator.

### <a name="what-do-i-need-for-the-configuration-server"></a>Vad behöver jag för konfigurationsservern?

Granska den [krav](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan jag manuellt konfigurera konfigurationsservern istället för att använda en mall?

Vi rekommenderar att du [skapa konfigurationsserverns Virtuella](vmware-azure-deploy-configuration-server.md) med hjälp av den senaste versionen av mallen Öppna Virtual Machine (OVF). Om du inte kan använda mallen (till exempel, om du inte har åtkomst till VMware-server), [hämta](physical-azure-set-up-source.md) installationsfilen från portalen och Ställ in konfigurationsservern.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Replikera en konfigurationsserver till fler än en region?

Nej. Om du vill replikera till fler än en region, behöver du en konfigurationsserver i varje region.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan jag vara värd för en konfigurationsserver i Azure?

Även om det är möjligt, måste den virtuella Azure-datorer som kör configuration server kommunicera med dina lokala VMware-infrastruktur och virtuella datorer. Den här kommunikationen lägger svarstid och påverkar pågående replikering.

### <a name="how-do-i-update-the-configuration-server"></a>Hur uppdaterar jag configuration server?

[Lär dig](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) så här uppdaterar du konfigurationsservern.

- Du hittar den senaste uppdatering informationen på den [uppdateringar i Azure-sidan](https://azure.microsoft.com/updates/?product=site-recovery).
- Du kan hämta den senaste versionen från portalen. Du kan hämta den senaste versionen av konfigurationsservern direkt från den [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Om din version är fler än fyra versioner som är äldre än den aktuella versionen, se den [supportmeddelande](https://aka.ms/asr_support_statement) uppgraderingsanvisningar.

### <a name="should-i-back-up-the-configuration-server"></a>Bör jag säkerhetskopiera configuration server?

Vi rekommenderar att utföra regelbundna schemalagda säkerhetskopieringar av konfigurationsservern.

- Den virtuella datorn att växlas tillbaka måste finnas i server-konfigurationsdatabasen för lyckad återställning efter fel.
- Konfigurationsservern måste vara körs och är i anslutet tillstånd.
- [Läs mer](vmware-azure-manage-configuration-server.md) om vanliga hanteringsaktiviteter för configuration server.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>När jag ställer in konfigurationsservern, kan jag hämta och installera MySQL manuellt?

Ja. Hämta MySQL och placera den i mappen C:\Temp\ASRSetup. Sedan kan installera det manuellt. När du ställer in konfigurationsservern VM och accepterar villkoren MySQL listas som **redan installerat** i **ladda ned och installera**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kan jag undvika att överföra MySQL men låta Site Recovery installerar den?

Ja. Ladda ned MySQL-installationsprogrammet och placera den i mappen C:\Temp\ASRSetup. När du ställer in konfigurationsservern VM godkänner licensvillkoren och välj **ladda ned och installera**. Det installationsprogram som du har lagt till för att installera MySQL används.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Kan jag använda konfigurationsservern VM för något annat?

Nej. Använd den virtuella datorn endast för konfigurationsservern.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Kan jag klona en konfigurationsserver och använda det för dirigering?

Nej. Konfigurera en ny konfigurationsserver för att undvika problem med registreringen.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Kan jag ändra det valv som konfigurationsservern är registrerad?

Nej. När ett valv är associerat med konfigurationsservern kan inte ändras. [Lär dig](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) om hur du registrerar en konfigurationsserver med ett annat valv.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Kan jag använda samma konfigurationsservern för haveriberedskap för både virtuella VMware-datorer och fysiska servrar?

Ja, men Observera att den fysiska datorn kan att växlas tillbaka endast till en VMware-VM.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Var kan jag hämta lösenfrasen för configuration server?

[Lär dig](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) hur du laddar ned lösenfrasen.

### <a name="where-can-i-download-vault-registration-keys"></a>Var kan jag hämta valv registreringsnycklar?

I Recovery Services-valv väljer **Konfigurationsservrar** i **Site Recovery-infrastruktur** > **hantera**. I **servrar**väljer **ladda ned Registreringsnyckeln** att hämta valvautentiseringsfilen.

## <a name="process-server"></a>Processervern

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Varför kan jag inte välja processerver när jag aktiverar replikering?

Uppdateringar versioner 9,24 och senare nu visas den [hälsotillståndet för processervern när du aktiverar replikering](vmware-azure-enable-replication.md#enable-replication). Den här funktionen hjälper att undvika processervern begränsning och för att minska användningen av defekta processervrar.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Hur uppdaterar jag processervern till version 9.24 eller senare för korrekt hälsoinformation?

Från och med [version 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups), fler aviseringar har lagts till indikerar tillståndet för processervern. [Uppdatera din Site Recovery-komponenter till version 9.24 eller senare] (service-updates-how-to.md#links-to-currently-supported-update-rollups) så att alla aviseringar genereras.

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Kan jag använda en lokal processerver för återställning efter fel?

Vi rekommenderar starkt att skapa en processerver i Azure för återställning efter fel, och undvika fördröjning för överföring av data. Dessutom är det viktigt att använda den processerver som skapats i Azure för återställning efter fel om du åtskiljda källnätverket för virtuella datorer med Azure-riktade nätverket på konfigurationsservern.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Kan jag behålla IP-adressen på redundans?

Ja, du kan behålla IP-adress vid redundans. Kontrollera att du anger att mål-IP-adressen i den **beräkning och nätverk** inställningar för den virtuella datorn före redundans. Dessutom vill stänga av datorer vid tidpunkten för redundans för att undvika IP-adresskonflikter uppstå vid återställning efter fel.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Kan jag ändra storlek för virtuell måldator eller typ av virtuell dator innan redundans?

Ja, du kan ändra typ eller storlek på den virtuella datorn före redundans. På portalen kan använda den **beräkning och nätverk** inställningar för den replikerade virtuella datorn.

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?

För VMware till Azure är den äldsta återställningspunkten som du kan använda 72 timmar.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur kommer jag åt virtuella Azure-datorer efter redundans?

Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller via Azure ExpressRoute efter redundansväxlingen. För att ansluta, måste du förbereda flera saker. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Är över data elastiska?

Azure är utformat med flexibilitet i fokus. Site Recovery är utformat för redundansväxling till en sekundär Azure-datacentret som krävs av de Azure serviceavtal (SLA). Vid redundans vi Kontrollera att dina metadata och valv finns kvar i samma geografiska region som du har valt för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

[Redundans](site-recovery-failover.md) inte automatiskt. Du startar en redundans genom att göra ett val i portalen eller använda [PowerShell](/powershell/module/az.recoveryservices) att utlösa redundans.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag återställa till en annan plats?

Ja. Om du växlas över till Azure kan du växla tillbaka till en annan plats om den ursprungliga som inte är tillgänglig. [Läs mer](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Varför behöver jag en VPN eller ExpressRoute med privat peering för att återställa?

När du växlar tillbaka från Azure kopieras data från Azure tillbaka till den lokala virtuella datorn och privat åtkomst krävs.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Kan jag ändra storlek på virtuella Azure-datorer efter redundans?

Nej, du kan inte ändra storlek och typ av den Virtuella måldatorn efter redundansen.

## <a name="automation-and-scripting"></a>Automatisering och skript

### <a name="can-i-set-up-replication-with-scripting"></a>Kan jag konfigurera replikering med skript?

Ja. Du kan automatisera Site Recovery arbetsflöden med hjälp av Rest API, PowerShell eller Azure SDK. [Läs mer](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestanda och kapacitet

### <a name="can-i-throttle-replication-bandwidth"></a>Kan jag begränsa bandbredden för replikering?

Ja. [Läs mer](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Nästa steg

- [Granska](vmware-physical-azure-support-matrix.md) supportkrav.
- [Konfigurera](vmware-azure-tutorial.md) VMware till Azure-replikering.
