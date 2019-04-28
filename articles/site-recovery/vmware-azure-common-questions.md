---
title: Vanliga frågor – VMware till Azure-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln sammanfattas vanliga frågor filnamnsdelen haveriberedskap för lokala virtuella VMware-datorer till Azure med hjälp av Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 04/23/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: dffbb2c52b4e43eefe6b4f377bd7af529bae8cc5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125567"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Vanliga frågor – VMware till Azure replikering

Den här artikeln innehåller svar på vanliga frågor när du distribuerar haveriberedskap för lokala virtuella VMware-datorer till Azure. 

## <a name="general"></a>Allmänt 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Vad behöver jag för haveriberedskap för VMware VM?

[Lär dig mer om](vmware-azure-architecture.md) komponenterna som ingår i haveriberedskap för virtuella VMware-datorer. 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Kan jag använda Site Recovery för att migrera virtuella VMware-datorer till Azure?

Ja, förutom att använda Site Recovery för att ställa in fullständig haveriberedskap för virtuella VMware-datorer kan du kan även använda Site Recovery för att migrera lokala virtuella VMware-datorer till Azure. I det här scenariot replikera du lokala virtuella VMware-datorer till Azure storage. Sedan kan växla du över från den lokala till Azure. Efter redundansväxlingen är dina appar och arbetsbelastningar tillgängliga och körs på virtuella Azure-datorer. Processen liknar hur du konfigurerar fullständig haveriberedskap, förutom att du kan inte i en migrering från Azure.


### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver Mina Azure-konto behörighet att skapa virtuella datorer?
Om du är administratör för prenumerationen har Replikeringsbehörighet som du behöver. Om du inte behöver behörighet att skapa en Azure-dator i resursgruppen och virtuellt nätverk som du anger genom att konfigurera Site Recovery och behörighet att skriva till det valda lagringskontot eller hanterad disk baserat på din konfiguration. [Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="what-applications-can-i-replicate"></a>Vilka program kan jag replikera?
Du kan replikera en app eller arbetsbelastning som körs på en VMware-VM som uppfyller [replikeringskraven](vmware-physical-azure-support-matrix.md##replicated-machines).
- Site Recovery har stöd för Programmedveten replikering så att appar kan växlas över och återställde till en intelligent tillstånd.
- Site Recovery kan integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och har ett nära samarbete med ledande leverantörer, som Oracle, SAP, IBM och Red Hat.
- [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Kan jag använda en serverlicens för gäst-OS i Azure?
Ja, Microsoft Software Assurance-kunder kan använda [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) att spara på licenskostnaden för **datorer med Windows Server** som migreras till Azure, eller att använda Azure för haveriberedskap.

## <a name="security"></a>Säkerhet

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Vilken åtkomst behöver du VMware-servrar i Site Recovery?
Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Konfigurera en VMware-VM som kör konfigurationsservern Site Recovery
- Automatiskt identifiera virtuella datorer för replikering. 


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Vilken åtkomst behöver du virtuella VMware-datorer i Site Recovery?

- För att kunna replikera, ha en VMware VM i Site Recovery-mobilitetstjänsten installerad och igång. Du kan distribuera verktyget manuellt eller ange att Site Recovery bör göra en push-installation av tjänsten när du aktiverar replikering för en virtuell dator. 
- Under replikering kommunicerar virtuella datorer med Site Recovery på följande sätt:
    - Virtuella datorer kommunicerar med konfigurationsservern på port HTTPS 443 för replikeringshantering.
    - Virtuella datorer skickar replikeringsdata till processervern på port HTTPS 9443 (kan ändras).
    - Om du aktiverar konsekvens för flera datorer, kommunicera virtuella datorer med varandra på port 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till Site Recovery?
Nej, Site Recovery komma åt inte replikerade data och har inte någon information om vad som körs på dina virtuella datorer. Replikeringsdata utbyts mellan VMware-hypervisorer och Azure storage. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001: 2013, 27018, HIPAA, DPA certifierade och håller på att SOC2 och FedRAMP JAB-utvärderingar.


## <a name="pricing"></a>Prissättning
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hur kan jag för att beräkna ungefärliga avgifter för VMware-haveriberedskap?

Du kan använda den [priskalkylator](https://aka.ms/asr_pricing_calculator) uppskatta kostnader när du använder Site Recovery.

För detaljerad uppskattning på kostnader och köra distributionsplaneringsverktyget för [VMware](https://aka.ms/siterecovery_deployment_planner), och använda den [kostnad rapporten kostnadsuppskattning](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Finns det någon skillnad i kostnaden för mellan replikerar till lagring eller direkt till hanterade diskar?

Hanterade diskar debiteras annorlunda ut än storage-konton. [Läs mer](https://azure.microsoft.com/pricing/details/managed-disks/) om priser för hanterad disk.

## <a name="mobility-service"></a>Mobilitetstjänsten

### <a name="where-can-i-find-the-mobility-service-installers"></a>Var hittar jag Mobility service installationsprogram?
Installationsprogrammen är i den **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** mapp på konfigurationsservern.

## <a name="how-do-i-install-the-mobility-service"></a>Hur gör jag för att installera mobilitetstjänsten?
Du installerar på varje virtuell dator som du vill replikera, med hjälp av ett antal metoder:
- [Push-installation](vmware-physical-mobility-service-overview.md#push-installation)
- [Manuell installation](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) från Användargränssnittet eller Powershell.
- Distributionen med hjälp av ett distributionsverktyg som [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).

## <a name="managed-disks"></a>Hanterade diskar

### <a name="where-does-site-recovery-replicate-data-to"></a>Där replikerar data till av Site Recovery?

Site Recovery replikerar lokala virtuella VMware-datorer och fysiska servrar till managed disks i Azure.
- Processervern för Site Recovery skriver replikeringsloggar till ett cachelagringskonto i målregionen.
- De här loggarna används för att skapa återställningspunkter på hanterade diskar.
- Vid redundans används den återställningspunkt som du väljer att skapa hanterade måldisken.
- Virtuella datorer som tidigare har replikerats till ett lagringskonto (före mars 2019) påverkas inte.


### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Kan jag replikera nya datorer till storage-konton?

Nej, från och med mars 2019 portalen, kan du replikera endast till Azure hanterade diskar. 

Replikering av nya virtuella datorer till ett lagringskonto är endast tillgänglig med PowerShell eller REST-API (version 2018-01-10 eller 2016-08-10).

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Vilka är fördelarna i replikeras till hanterade diskar?

[Lär dig hur](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery förenklar haveriberedskapen med hanterade diskar.


### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>Kan jag ändra typ av hanterad disk när datorn är skyddad?

Ja, du kan enkelt [ändra typen av hanterad disk](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). Se till att du återkallar SAS-Webbadress för disken genom att gå till den hanterade diskresursen på Azure-portalen innan du ändrar typen. Avbryt alla pågående export från översiktsbladet. När SAS-Webbadressen har återkallats, ändra typ av disk inom några minuter. Men om du ändrar typ av hanterad disk, vänta tills nya återställningspunkter som ska genereras av Azure Site Recovery. Använd de nya återställningspunkterna för redundanstest eller redundans framöver.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Kan jag byta replikeringen från hanterade diskar till ohanterade diskar?

Nej, byter från hanterad till ohanterad stöds inte.

## <a name="replication"></a>Replikering


### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är kraven för replikerade virtuella datorer?

[Läs mer](vmware-physical-azure-support-matrix.md##replicated-machines) om virtuella VMware-datorer/fysiska krav och support.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?
Replikeringen är kontinuerlig när du replikerar virtuella VMware-datorer till Azure.


### <a name="can-i-extend-replication"></a>Kan jag utöka replikeringen?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en offlinereplikering första?
Det stöds inte. Begär den här funktionen i den [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks-from-replication"></a>Kan jag undanta diskar från replikering?
Ja, kan du utesluta diskar.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar kan replikeras. Operativsystemdisken måste vara en standarddisk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Om jag använder replikeringsgrupper för konsekvens för flera datorer, kan jag lägga till en ny virtuell dator till en befintlig replikeringsgrupp?
Ja, kan du lägga till nya virtuella datorer till en befintlig replikeringsgrupp när du aktiverar replikering för dessa.
- Du kan inte lägga till en virtuell dator till en befintlig replikeringsgrupp när replikering har initierats.
- Du kan inte skapa en replikeringsgrupp för befintliga virtuella datorer.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan jag ändra virtuella datorer som replikerar genom att lägga till eller ändra storlek på diskar?

Du kan ändra diskens storlek för VMware-replikering till Azure. Om du vill lägga till nya diskar måste du lägga till disken och återaktivera skyddet för den virtuella datorn.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Kan jag migrera lokala datorer till en ny vCenter-Server utan att påverka pågående replikering?
Nej, ändring av Vcenter eller migrering kommer att påverka pågående replikering. Du måste konfigurera Site Recovery med nya vCenter-servern och aktivera replikering för virtuella datorer igen.

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Kan jag replikera till en cache-/ mållagringskontot som har ett virtuellt nätverk (med Azure storage-brandväggar) konfigurerats på den?
Nej, Site Recovery stöder inte replikering till lagring för virtuella nätverk.





## <a name="component-upgrade"></a>Komponent-uppgradering

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Min Mobility services-agenten eller configuration server-version är gammal och min uppgraderingen misslyckades. Vad gör jag nu?  

Site Recovery följer supportmodell N-4. [Läs mer](https://aka.ms/asr_support_statement) om hur du uppgraderar från mycket äldre versioner.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Var hittar jag release notes/samlade uppdateringar av Azure Site Recovery?

[Lär dig](site-recovery-whats-new.md) om nya uppdateringar och [Hämta samlad information](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Var hittar jag information om uppgradering för haveriberedskap till Azure?

[Lär dig mer om](https://aka.ms/asr_vmware_upgrades) uppgradera.

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Behöver jag omstart källdatorer för varje uppgradering?

Alternativ som rekommenderas, är det inte obligatoriskt för varje uppgradering. [Läs mer](https://aka.ms/asr_vmware_upgrades).


## <a name="configuration-server"></a>Konfigurationsserver

### <a name="what-does-the-configuration-server-do"></a>Vad gör configuration server?

Konfigurationsservern körs lokalt Site Recovery-komponenter, inklusive:
- Konfigurationsservern själva som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Processervern också har en push-installation av Mobilitetstjänsten på virtuella datorer och utför automatisk identifiering av lokala virtuella VMware-datorer.
- Huvudmålservern som hanterar replikeringsdata vid återställning från Azure.

[Läs mer](vmware-azure-architecture.md) om konfigurationsserverns komponenter och processer.

### <a name="where-do-i-set-up-the-configuration-server"></a>Var konfigurerar jag configuration server?
Du behöver en enda med hög tillgänglighet lokal VMware VM för konfigurationsservern. Du kan installera konfigurationsservern för haveriberedskap för fysisk server, på en fysisk dator.

### <a name="what-do-i-need-for-the-configuration-server"></a>Vad behöver jag för konfigurationsservern?

Granska den [krav](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan jag manuellt konfigurera konfigurationsservern istället för att använda en mall?
Vi rekommenderar att du [skapa konfigurationsserverns Virtuella](vmware-azure-deploy-configuration-server.md) med den senaste versionen av OVF-mall. Om du av någon anledning som du kan till exempel du inte har åtkomst till VMware-servern, kan du [hämta](physical-azure-set-up-source.md) installationsfilen från portalen och Ställ in konfigurationsservern.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Replikera en konfigurationsserver till fler än en region?
Nej. Då behöver du en konfigurationsserver i varje region.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan jag vara värd för en konfigurationsserver i Azure?
Även om det går måste den virtuella Azure-datorer som kör konfigurationsservern kommunicera med dina lokala VMware-infrastruktur och virtuella datorer. Detta lägger till svarstid och påverkar pågående replikering.

### <a name="how-do-i-update-the-configuration-server"></a>Hur uppdaterar jag configuration server?

[Lär dig](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) så här uppdaterar du konfigurationsservern.
- Du hittar den senaste uppdatering informationen på den [uppdateringar i Azure-sidan](https://azure.microsoft.com/updates/?product=site-recovery).
- Du kan hämta den senaste versionen från portalen. Du kan också direkt hämta den senaste versionen av konfigurationsservern från den [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Om din version är fler än fyra versioner som är äldre än den aktuella versionen, se vår [supportmeddelande](https://aka.ms/asr_support_statement) uppgraderingsanvisningar.

### <a name="should-i-back-up-the-configuration-server"></a>Bör jag säkerhetskopiera configuration server?
Vi rekommenderar att utföra regelbundna schemalagda säkerhetskopieringar av konfigurationsservern.
- Den virtuella datorn att växlas tillbaka måste finnas i server-konfigurationsdatabasen för lyckad återställning efter fel.
- Konfigurationsservern måste vara körs och är i anslutet tillstånd.
- [Läs mer](vmware-azure-manage-configuration-server.md) om vanliga hanteringsaktiviteter för configuration server.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>När jag ställer in konfigurationsservern, kan jag hämta och installera MySQL manuellt?

Ja. Hämta MySQL och placera den i den **C:\Temp\ASRSetup** mapp. Installera den manuellt. När du ställer in konfigurationsservern VM och accepterar villkoren MySQL listas som **redan installerat** i **ladda ned och installera**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kan jag undvika att överföra MySQL men låta Site Recovery installerar den?

Ja. Ladda ned MySQL-installationsprogrammet och placera den i den **C:\Temp\ASRSetup** mapp.  När du ställer in konfigurationsservern VM acceptera villkoren och klicka på **ladda ned och installera**. Installationsprogrammet som du har lagt till för att installera MySQL används.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Kan jag använda konfigurationsservern VM för något annat?
Nej, bör du endast använda den virtuella datorn för konfigurationsservern. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Kan jag klona en konfigurationsserver och använda det för dirigering?
Nej, bör du ställa in en ny konfigurationsserver att undvika problem med registreringen.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Kan jag ändra det valv som konfigurationsservern är registrerad?
Nej. När ett valv är associerat med konfigurationsservern kan inte ändras. Granska [i den här artikeln](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) vill veta mer om att registrera.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Jag kan använda samma konfigurationsservern för haveriberedskap för både virtuella VMware-datorer och fysiska servrar
Ja, men Observera att den fysiska datorn kan bara att växlas tillbaka till en VMware-VM.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Var kan jag hämta lösenfrasen för configuration server?
[Lär dig hur du](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) hämta lösenfrasen.

### <a name="where-can-i-download-vault-registration-keys"></a>Var kan jag hämta valv registreringsnycklar?

I Recovery Services-valvet klickar du på **Konfigurationsservrar** i **Site Recovery-infrastruktur** > **hantera**. I **servrar**väljer **ladda ned Registreringsnyckeln** att hämta valvautentiseringsfilen.

## <a name="process-server"></a>Process Server

### <a name="unable-to-select-process-server-during-enable-replication"></a>Det går inte att välja processerver under Aktivera replikering

Från 9.24 version förbättringar har gjorts att tillhandahålla [i produkten vägledning](vmware-azure-manage-process-server.md#process-server-selection-guidance) på när du skapar en skalbar processerver. Det här är att undvika process server begränsning och undvika användningen av defekta processervern.

### <a name="what-should-i-do-to-obtain-accurate-health-status-of-process-server"></a>Vad gör jag för att få korrekt hälsostatus för processervern?

Uppgradera Site Recovery-komponenter till den [senaste versionerna](service-updates-how-to.md#links-to-currently-supported-update-rollups) (minst 9.24 eller senare).

## <a name="failover-and-failback"></a>Redundans och återställning efter fel
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Kan jag använda processervern på den lokala för återställning efter fel?
Vi rekommenderar starkt att skapa en processerver i Azure för återställning efter fel, och undvika fördröjning för överföring av data. Dessutom är det viktigt att använda den processerver som skapats i Azure för återställning efter fel om du åtskiljda källnätverket för virtuella datorer med Azure-riktade nätverket på konfigurationsservern.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Kan jag behålla IP-adress på redundans?
Ja, kan du behålla IP-adress vid redundans. Kontrollera att du anger IP-måladressen i beräknings- och nätverksinställningar' inställningarna för den virtuella datorn före redundans. Dessutom vill stänga av datorer vid tidpunkten för redundans för att undvika IP-adresskonflikter uppstå vid återställning efter fel.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Kan jag ändra storlek för virtuell måldator eller typ av virtuell dator innan redundans?
Ja, du kan ändra typ eller storlek på den virtuella datorn när som helst före redundans på inställningarna för beräkning och nätverk för den replikerade virtuella datorn i portalen.

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?
För VMware till Azure är den äldsta återställningspunkten som du kan använda 72 timmar.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur kommer jag åt virtuella Azure-datorer efter redundans?
Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller via Azure ExpressRoute efter redundansväxlingen. Du måste förbereda ett antal saker för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Växlas över data som är elastiska?

Azure är utformat med flexibilitet i fokus. Site Recovery är utformat för redundansväxling till en sekundär Azure-datacenter, i enlighet med serviceavtalet för Azure. Vid redundans vi Kontrollera att dina metadata och valv finns kvar i samma geografiska region som du har valt för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
[Redundans](site-recovery-failover.md) inte automatiskt. Du startar redundansväxlingar med ett enda klick i portalen eller använda [PowerShell](/powershell/module/az.recoveryservices) att utlösa redundans.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag återställa till en annan plats?
Ja, om du växlas över till Azure, du kan växla tillbaka till en annan plats om den ursprungliga som inte är tillgänglig. [Läs mer](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Varför behöver jag en VPN eller ExpressRoute för att återställa efter felet?
När du växlar tillbaka från Azure kopieras data från Azure tillbaka till den lokala virtuella datorn och privat åtkomst krävs.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Kan jag ändra storlek på virtuella Azure-datorer efter redundans?
Nej, du kan inte ändra storlek och typ av den Virtuella måldatorn efter redundansen.


## <a name="automation-and-scripting"></a>Automatisering och skript

### <a name="can-i-set-up-replication-with-scripting"></a>Kan jag konfigurera replikering med skript?
Ja. Du kan automatisera Site Recovery arbetsflöden med hjälp av Rest API, PowerShell eller Azure SDK. [Mer](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestanda och kapacitet
### <a name="can-i-throttle-replication-bandwidth"></a>Kan jag begränsa bandbredden för replikering?
Ja. [Läs mer](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Nästa steg
* [Granska](vmware-physical-azure-support-matrix.md) supportkrav.
* [Konfigurera](vmware-azure-tutorial.md) VMware till Azure-replikering.
