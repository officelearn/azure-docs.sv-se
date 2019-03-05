---
title: Vanliga frågor – VMware till Azure-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln sammanfattas vanliga frågor när du konfigurerar haveriberedskap för lokala virtuella VMware-datorer till Azure med Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
services: site-recovery
ms.date: 03/03/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 038716161845e94011688e8af80a5d4830ac1a5b
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338152"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Vanliga frågor – VMware till Azure replikering

Den här artikeln innehåller svar på vanliga frågor som vi kan se när du distribuerar haveriberedskap för lokala virtuella VMware-datorer till Azure. Om du har frågor när du har läst den här artikeln kan publicera dem på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Allmänt
### <a name="how-is-site-recovery-priced"></a>Hur prissätts Site Recovery?
Granska [priserna för Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) information.

### <a name="how-do-i-pay-for-azure-vms"></a>Hur betalar jag för virtuella Azure-datorer?
Data replikeras till Azure-lagring under replikering, och du behöver inte betala några VM-ändringar. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt Azure IaaS-datorer. Efter det du faktureras för de beräkningsresurser som du använder i Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Vad kan jag göra med VMware till Azure replikering?
- **Haveriberedskap**: Du kan ställa in fullständig haveriberedskap. I det här scenariot kan replikera du lokala virtuella VMware-datorer till Azure storage. Sedan, om den lokala infrastrukturen inte är tillgänglig kan du växla över till Azure. När du redundansväxlar, skapas virtuella Azure-datorer med hjälp av replikerade data. Du kan komma åt appar och arbetsbelastningar på Azure Virtual Machines tills ditt datacenter på plats är tillgänglig igen. Sedan kan du växla tillbaka från Azure till din lokala plats.
- **Migrering**: Du kan använda Site Recovery för att migrera lokala virtuella VMware-datorer till Azure. I det här scenariot replikera du lokala virtuella VMware-datorer till Azure storage. Sedan kan växla du över från den lokala till Azure. Efter redundansväxlingen är dina appar och arbetsbelastningar tillgängliga och körs på virtuella Azure-datorer.

## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Vad behöver jag i Azure?
Du behöver en Azure-prenumeration, Recovery Services-valvet, ett cachelagringskonto, hanterade diskar och ett virtuellt nätverk. Valvet cachelagringskontot, hanterade diskar och nätverket måste finnas i samma region.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver Mina Azure-konto behörighet att skapa virtuella datorer?
Om du är administratör för prenumerationen har Replikeringsbehörighet som du behöver. Om du inte behöver behörighet att skapa en Azure-dator i resursgruppen och virtuellt nätverk som du anger genom att konfigurera Site Recovery och behörighet att skriva till det valda lagringskontot eller hanterad disk baserat på din konfiguration. [Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="can-i-use-guest-os-server-license-on-azure"></a>Kan jag använda gäst-OS server-licens i Azure?
Ja, Microsoft Software Assurance-kunder kan använda [Azure Hybrid-förmånen](https://azure.microsoft.com/en-in/pricing/hybrid-benefit/) att spara på licenskostnaden för **datorer med Windows Server** som migreras till Azure, eller att använda Azure för haveriberedskap.

## <a name="pricing"></a>Prissättning

### <a name="how-are-licensing-charges-handled-during-replication-after-failover"></a>Hur hanteras licensiering avgifter vid replikering efter redundans?

Se vanliga frågor och svar om licensiering [här](https://aka.ms/asr_pricing_FAQ) för mer information.

### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hur kan jag för att beräkna ungefärliga avgifter vid användning av Site Recovery?

Du kan använda [priskalkylator](https://aka.ms/asr_pricing_calculator) uppskatta kostnader när du använder Azure Site Recovery. För detaljerad uppskattning på kostnader och köra distributionskapacitetsplaneraren (https://aka.ms/siterecovery_deployment_planner) och analysera den [kostnad rapporten kostnadsuppskattning](https://aka.ms/asr_DP_costreport).

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jag har använt Azure Site Recovery i över en månad. Får jag ändå de första 31 dagarna kostnadsfritt för varje skyddad instans?

Ja, det spelar ingen roll hur länge du har använt Azure Site Recovery. Varje skyddad instans är fri från Azure Site Recovery-avgifter under de första 31 dagarna. Om du till exempel har skyddat 10 instanser under de senaste 6 månaderna och ansluter en 11:e instans till Azure Site Recovery debiteras inga Azure Site Recovery-avgifter för den 11:e instansen under de första 31 dagarna. De första 10 instanserna debiteras även fortsättningsvis Azure Site Recovery-avgifter, eftersom de har skyddats i mer än 31 dagar.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Förekommer det några andra avgifter för Azure under de första 31 dagarna?

Ja, även om Azure Site Recovery är kostnadsfritt under de första 31 dagarna för en skyddad instans kan du komma att debiteras för Azure Storage, lagringstransaktioner och dataöverföring. En återställd virtuell dator kan också debiteras för Azure-beräkningsavgifter.

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>Vilka avgifter debiteras vid användning av Azure Site Recovery?

Se våra [vanliga frågor och svar på kostnaderna](https://aka.ms/asr_pricing_FAQ) detaljerad information.

### <a name="is-there-a-cost-associated-to-perform-dr-drillstest-failover"></a>Kostar det om du vill utföra DR-test/redundanstest?

Det finns ingen separat kostnad för DR-test. Det är beräkningsavgifter när den virtuella datorn har skapats efter testa redundans.

## <a name="azure-site-recovery-components-upgrade"></a>Uppgradera Azure Site Recovery-komponenter

### <a name="my-mobility-agentconfiguration-serverprocess-server-version-is-very-old-and-my-upgrade-has-failed-how-should-i-upgrade-to-latest-version"></a>Min Mobility/Agentkonfiguration Server/processerverns version är mycket gammal och min uppgraderingen misslyckades. Hur ska jag uppgraderar till senaste versionen?

Azure Site Recovery följer N-4 supportmodell. Se våra [supportmeddelande](https://aka.ms/asr_support_statement) att förstå information om hur du uppgraderar från mycket äldre versioner.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Var hittar jag release notes/samlade uppdateringar av Azure Site Recovery?

Referera till den [dokumentet](https://aka.ms/asr_update_rollups) release notes information. Du hittar länkar för installation av respektive komponenter i varje samlade uppdateringen.

### <a name="how-should-i-upgrade-site-recovery-components-for-on-premises-vmware-or-physical-site-to-azure"></a>Hur ska jag uppgradera Site Recovery-komponenter lokalt VMware eller fysiska plats till Azure?

Se våra vägledningen [här](https://aka.ms/asr_vmware_upgrades) att uppgradera din komponenter.

## <a name="is-reboot-of-source-machine-mandatory-for-each-upgrade"></a>Är omstart av källdatorn obligatoriskt för varje uppgraderingen?

Alternativ som rekommenderas, är det inte obligatoriskt för varje uppgradering. Se [här](https://aka.ms/asr_vmware_upgrades) för tydliga anvisningar.

## <a name="on-premises"></a>Lokal

### <a name="what-do-i-need-on-premises"></a>Vad gör jag behöver på plats?

Lokalt måste du:
- Site Recovery-komponenter installeras på en enda VMware VM.
- En VMware-infrastruktur, med minst en ESXi-värden, och vi rekommenderar att en vCenter-server.
- En eller flera virtuella VMware-datorer att replikera.

[Läs mer](vmware-azure-architecture.md) om VMware till Azure-arkitektur.

Den lokala konfigurationsservern kan distribueras på följande sätt:

- Vi rekommenderar du distribuerade konfigurationsservern som en VMware-VM med en OVA-mall med konfigurationsservern förinstallerat.
- Om du av någon anledning inte kan du använda en mall, kan du ställa in konfigurationsservern manuellt. [Läs mer](physical-azure-disaster-recovery.md#set-up-the-source-environment).



### <a name="where-do-on-premises-vms-replicate-to"></a>Där replikerar lokala virtuella datorer till?
Data replikeras till Azure storage. När du kör en redundans, Site Recovery automatiskt skapar virtuella Azure-datorer från storage-konto eller en hanterad disk baserat på din konfiguration.

## <a name="replication"></a>Replikering

### <a name="what-applications-can-i-replicate"></a>Vilka program kan jag replikera?
Du kan replikera en app eller arbetsbelastning som körs på en VMware-VM som uppfyller [replikeringskraven](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery har stöd för Programmedveten replikering så att appar kan växlas över och återställde till en intelligent tillstånd. Site Recovery kan integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och har ett nära samarbete med ledande leverantörer, som Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-protect-a-virtual-machine-that-has-docker-disk-configuration"></a>Kan jag skydda en virtuell dator som har Docker diskkonfigurationen?

Nej, detta är ett scenario som inte stöds.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan jag replikera till Azure med ett plats-till-plats-VPN?
Site Recovery replikerar data från en lokal plats till Azure storage via en offentlig slutpunkt eller med offentlig peering i ExpressRoute. Replikering via ett plats-till-plats VPN-nätverk stöds inte.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan jag replikera till Azure med ExpressRoute?
Ja, ExpressRoute kan användas för att replikera datorer till Azure. Site Recovery replikerar data till Azure Storage via en offentlig slutpunkt. Du måste konfigurera [offentlig peering](../expressroute/expressroute-circuit-peerings.md#publicpeering) eller [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) använder ExpressRoute under Site Recovery-replikering. Microsoft-peering är den rekommenderade routningsdomän för replikering. Se till att den [nätverk krav](vmware-azure-configuration-server-requirements.md#network-requirements) uppfylls också för replikering. När virtuella datorer som redundansväxlar till Azure-nätverk, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).

### <a name="how-can-i-change-storage-account-after-machine-is-protected"></a>Hur kan jag ändra storage-konto när datorn är skyddad?

För en pågående replikering kan storage-konto bara uppgraderas till premium. Om du vill använda standardpriserna måste du inaktivera replikeringen av källdatorn och återaktivera skyddet med hanterade standarddiskar. Förutom detta finns det en något annat sätt att ändra storage-konto när skyddsinställningarna är aktiverade.

### <a name="how-can-i-change-managed-disk-type-after-machine-is-protected"></a>Hur kan jag ändra typ av hanterad Disk när datorn är skyddad?

Ja, du kan enkelt ändra typen av hanterade diskar. [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage).

### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan inte replikera via VPN?

När du replikerar till Azure replikeringstrafik når de offentliga slutpunkterna i ett Azure Storage och därför kan du bara replikera via det offentliga internet med ExpressRoute (offentlig peering) VPN fungerar inte.

### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är kraven för replikerade virtuella datorer?

För replikering, måste en VMware VM köra ett operativsystem som stöds. Dessutom kan måste den virtuella datorn uppfylla kraven för virtuella Azure-datorer. [Läs mer](vmware-physical-azure-support-matrix.md##replicated-machines) i supportmatrisen.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?
Replikeringen är kontinuerlig när du replikerar virtuella VMware-datorer till Azure.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Kan jag behålla IP-adress på redundans?
Ja, kan du behålla IP-adress vid redundans. Se till att du nämner mål-IP ”beräkning och nätverk-bladet före redundans. Kontrollera också om du vill stänga av datorer vid tidpunkten för redundans för att undvika IP-konflikter vid tidpunkten för återställning efter fel.

### <a name="can-i-extend-replication"></a>Kan jag utöka replikeringen?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en offlinereplikering första?
Det stöds inte. Begär den här funktionen i den [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?
Ja, kan du undanta diskar från replikering.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Kan jag ändra storlek för virtuell måldator eller typ av virtuell dator innan redundans?
Ja, du kan ändra typen eller storleken på den virtuella datorn före redundans genom att gå till beräknings- och nätverksinställningar objektets replikering från portalen.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar kan replikeras. Operativsystemdisken måste vara en standarddisk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Om jag använder replikeringsgrupper för konsekvens för flera datorer, kan jag lägga till en ny virtuell dator till en befintlig replikeringsgrupp?
Ja, kan du lägga till nya virtuella datorer till en befintlig replikeringsgrupp när du aktiverar replikering för dessa. Du kan inte lägga till en virtuell dator till en befintlig replikeringsgrupp när replikering initieras, och du kan inte skapa en replikeringsgrupp för befintliga virtuella datorer.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan jag ändra virtuella datorer som replikerar genom att lägga till eller ändra storlek på diskar?

Du kan ändra diskens storlek för VMware-replikering till Azure. Om du vill lägga till nya diskar måste du lägga till disken och återaktivera skyddet för den virtuella datorn.

### <a name="can-i-migrate-on-prem-machines-to-a-new-vcenter-without-impacting-ongoing-replication"></a>Kan jag migrera lokala datorer till en ny Vcenter utan att påverka pågående replikering?
Nej, ändring av Vcenter eller migrering kommer att påverka pågående replikering. Du måste konfigurera ASR med nya Vcenter och aktivera replikering för virtuella datorer.

### <a name="can-i-replicate-to-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Kan jag replikera till cache/mål-lagringskontot som har ett virtuellt nätverk (med Azure storage-brandväggar) konfigurerats på den?
Nej, Azure Site Recovery inte stöder replikering till lagring för virtuella nätverk.

## <a name="configuration-server"></a>Konfigurationsserver

### <a name="what-does-the-configuration-server-do"></a>Vad gör configuration server?
Konfigurationsservern körs lokalt Site Recovery-komponenter, inklusive:
- Konfigurationsservern som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar det till Azure storage., processervern installerar också Mobilitetstjänsten på virtuella datorer du vill replikera och utför automatisk identifiering av lokala virtuella VMware-datorer.
- Huvudmålservern som hanterar replikeringsdata vid återställning från Azure.

[Läs mer](vmware-azure-architecture.md) om konfigurationsserverns komponenter och processer.

### <a name="where-do-i-set-up-the-configuration-server"></a>Var konfigurerar jag configuration server?
Du behöver en enda med hög tillgänglighet lokal VMware VM för konfigurationsservern.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Vilka är kraven för konfigurationsservern?

Granska den [krav](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan jag manuellt konfigurera konfigurationsservern istället för att använda en mall?
Vi rekommenderar att du använder den senaste versionen av OVF-mall till [skapa konfigurationsserverns Virtuella](vmware-azure-deploy-configuration-server.md). Om du av någon anledning som du kan till exempel du inte har åtkomst till VMware-servern, kan du [Filnedladdning enhetliga installationsprogrammet](physical-azure-set-up-source.md) från portalen och kör den på en virtuell dator.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Replikera en konfigurationsserver till fler än en region?
Nej. Om du vill göra detta måste du ställa in en konfigurationsserver i varje region.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan jag vara värd för en konfigurationsserver i Azure?
Även om det går måste den virtuella Azure-datorer som kör konfigurationsservern kommunicera med dina lokala VMware-infrastruktur och virtuella datorer. Detta kan lägga till svarstider och påverka pågående replikering.

### <a name="how-do-i-update-the-configuration-server"></a>Hur uppdaterar jag configuration server?
[Lär dig mer om](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) uppdaterar konfigurationsservern. Du hittar den senaste uppdatering informationen i den [uppdateringar i Azure-sidan](https://azure.microsoft.com/updates/?product=site-recovery). Du kan också direkt ladda ned den senaste versionen av konfigurationsservern från [Microsoft Download Center](https://aka.ms/asrconfigurationserver). Om din version är äldre än 4 versioner från den aktuella versionen, se vår [supportmeddelande](https://aka.ms/asr_support_statement) uppgraderingsanvisningar.

### <a name="should-i-backup-the-deployed-configuration-server"></a>Bör jag säkerhetskopiera distribuerade konfigurationsservern?
Vi rekommenderar att utföra regelbundna schemalagda säkerhetskopieringar av konfigurationsservern. Den virtuella datorn att växlas tillbaka måste finnas i server-konfigurationsdatabasen för lyckad återställning efter fel och konfigurationsservern måste vara körs och är i anslutet tillstånd. Du kan lära dig mer om vanliga hanteringsaktiviteter för configuration server [här](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>När jag ställer in konfigurationsservern, kan jag hämta och installera MySQL manuellt?
Ja. Hämta MySQL och placera den i den **C:\Temp\ASRSetup** mapp. Installera den manuellt. När du ställer in konfigurationsservern VM och accepterar villkoren MySQL listas som **redan installerat** i **ladda ned och installera**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kan jag undvika att överföra MySQL men låta Site Recovery installerar den?
Ja. Ladda ned MySQL-installationsprogrammet och placera den i den **C:\Temp\ASRSetup** mapp.  När du ställer in konfigurationsservern VM acceptera villkoren och klicka på **ladda ned och installera**, installationsprogram som du har lagt till för att installera MySQL används.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Kan jag använda konfigurationsservern VM för något annat?
Nej, bör du endast använda den virtuella datorn för konfigurationsservern. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Kan jag klona en konfigurationsserver och använda det för dirigering?
Nej, bör du konfigurera en ny konfigurationsserver för att undvika problem med registreringen.

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>Kan jag ändra det valv som är registrerade på konfigurationsservern?
Nej. När ett valv har registrerats med konfigurationsservern kan inte ändras. Granska [i den här artikeln](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) omregistrering anvisningar.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Jag kan använda samma konfigurationsservern för haveriberedskap för både virtuella VMware-datorer och fysiska servrar
Ja, men Observera att den fysiska datorn kan vara endast att växlas tillbaka till en VMware-VM.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Var kan jag hämta lösenfrasen för configuration server?
[Läsa den här artikeln](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) att lära dig om hur du hämtar lösenfrasen.

### <a name="where-can-i-download-vault-registration-keys"></a>Var kan jag hämta valv registreringsnycklar?

I den **Recovery Services-valv**, **hantera** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**. I **servrar**väljer **ladda ned Registreringsnyckeln** att hämta valvautentiseringsfilen.



## <a name="mobility-service"></a>Mobilitetstjänsten

### <a name="where-can-i-find-the-mobility-service-installers"></a>Var hittar jag Mobility service installationsprogram?
Installationsprogram för hålls kvar i den **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** mapp på konfigurationsservern.

## <a name="how-do-i-install-the-mobility-service"></a>Hur gör jag för att installera mobilitetstjänsten?
Installera på varje virtuell dator som du vill replikera, med hjälp av en [push-installation](vmware-azure-install-mobility-service.md), eller [manuell installation](vmware-physical-mobility-service-install-manual.md) från Användargränssnittet eller Powershell. Du kan också distribuera med ett distributionsverktyg som [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).



## <a name="security"></a>Säkerhet

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Vilken åtkomst behöver du VMware-servrar i Site Recovery?
Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Konfigurera en VMware-VM som kör konfigurationsservern och andra lokala Site Recovery-komponenter. [Läs mer](vmware-azure-deploy-configuration-server.md)
- Automatiskt identifiera virtuella datorer för replikering. Lär dig mer om hur du förbereder ett konto för automatisk identifiering. [Läs mer](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Vilken åtkomst behöver du virtuella VMware-datorer i Site Recovery?

- För att kunna replikera, ha en VMware VM i Site Recovery-mobilitetstjänsten installerad och igång. Du kan distribuera verktyget manuellt eller ange att Site Recovery bör göra en push-installation av tjänsten när du aktiverar replikering för en virtuell dator. Site Recovery behöver ett konto som kan användas för att installera tjänstkomponenten som för push-installation. [Läs mer](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Den processerver (körs som standard på konfigurationsservern) utför den här installationen. [Läs mer](vmware-azure-install-mobility-service.md) om mobilitetstjänsten.
- Under replikering kommunicerar virtuella datorer med Site Recovery på följande sätt:
    - Virtuella datorer kommunicerar med konfigurationsservern på port HTTPS 443 för replikeringshantering.
    - Virtuella datorer skickar replikeringsdata till processervern på port HTTPS 9443 (kan ändras).
    - Om du aktiverar konsekvens för flera datorer, kommunicera virtuella datorer med varandra på port 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till Site Recovery?
Nej, Site Recovery komma åt inte replikerade data och har inte någon information om vad som körs på dina virtuella datorer. Replikeringsdata utbyts mellan VMware-hypervisorer och Azure storage. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001: 2013, 27018, HIPAA, DPA certifierade och håller på att SOC2 och FedRAMP JAB-utvärderingar.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Kan vi lagrar lokala metadata inom en geografiska regioner?
Ja. När du skapar ett valv i en region, kontrollera att alla metadata som används av Site Recovery finns kvar inom regionens geografisk gräns.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
Ja, både kryptering under överföring och [kryptering i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.


## <a name="failover-and-failback"></a>Redundans och återställning efter fel
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Kan jag använda processervern på den lokala för återställning efter fel?
Vi rekommenderar starkt att skapa en processerver i Azure för återställning efter fel för att undvika fördröjning för överföring av data. Dessutom om du åtskiljda källnätverket för virtuella datorer med Azure mot nätverket på konfigurationsservern är det viktigt att använda den Processerver som skapats i Azure för återställning efter fel.

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?
För VMware till Azure är den äldsta återställningspunkten som du kan använda 72 timmar.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur kommer jag åt virtuella Azure-datorer efter redundans?
Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller via Azure ExpressRoute efter redundansväxlingen. Du måste förbereda ett antal saker för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Växlas över data som är elastiska?
Azure är utformat med flexibilitet i fokus. Site Recovery är utformat för redundansväxling till en sekundär Azure-datacenter, i enlighet med serviceavtalet för Azure. Vid redundans vi Kontrollera att dina metadata och valv finns kvar i samma geografiska region som du har valt för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
[Redundans](site-recovery-failover.md) inte automatiskt. Du startar redundansväxlingar med ett enda klick i portalen eller använda [PowerShell](/powershell/module/azurerm.siterecovery) att utlösa redundans.

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
