---
title: Vanliga frågor – VMware till Azure-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln sammanfattas vanliga frågor när du konfigurerar haveriberedskap för lokala virtuella VMware-datorer till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 10/29/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c261dd083fed8b9c4a0f3846157c666cbb52083c
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636823"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Vanliga frågor – VMware till Azure replikering

Den här artikeln innehåller svar på vanliga frågor som vi kan se när du distribuerar haveriberedskap för lokala virtuella VMware-datorer till Azure. Om du har frågor när du har läst den här artikeln kan publicera dem på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Allmänt
### <a name="how-is-site-recovery-priced"></a>Hur prissätts Site Recovery?
Granska [priserna för Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) information.

### <a name="how-do-i-pay-for-azure-vms"></a>Hur betalar jag för virtuella Azure-datorer?
Data replikeras till Azure-lagring under replikering, och du behöver inte betala några VM-ändringar. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt Azure IaaS-datorer. Efter det du faktureras för de beräkningsresurser som du använder i Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Vad kan jag göra med VMware till Azure replikering?
- **Haveriberedskap**: du kan ställa in fullständig haveriberedskap. I det här scenariot kan replikera du lokala virtuella VMware-datorer till Azure storage. Sedan, om den lokala infrastrukturen inte är tillgänglig kan du växla över till Azure. När du redundansväxlar, skapas virtuella Azure-datorer med hjälp av replikerade data. Du kan komma åt appar och arbetsbelastningar på Azure Virtual Machines tills ditt datacenter på plats är tillgänglig igen. Sedan kan du växla tillbaka från Azure till din lokala plats.
- **Migrering**: du kan använda Site Recovery för att migrera lokala virtuella VMware-datorer till Azure. I det här scenariot replikera du lokala virtuella VMware-datorer till Azure storage. Sedan kan växla du över från den lokala till Azure. Efter redundansväxlingen är dina appar och arbetsbelastningar tillgängliga och körs på virtuella Azure-datorer.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Vad behöver jag i Azure?
Du behöver en Azure-prenumeration, Recovery Services-valvet, ett lagringskonto och ett virtuellt nätverk. Valvet, lagringskontot och nätverket måste vara i samma region.

### <a name="what-azure-storage-account-do-i-need"></a>Vilka Azure-lagringskonto behöver jag?
Du behöver ett LRS eller GRS-lagringskonto. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Premium storage stöds.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver Mina Azure-konto behörighet att skapa virtuella datorer?
Om du är administratör för prenumerationen har Replikeringsbehörighet som du behöver. Om du inte behöver du behörighet att skapa en Azure-dator i resursgruppen och virtuellt nätverk som du anger när du konfigurerar Site Recovery och behörighet att skriva till det valda lagringskontot. [Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Lokal

### <a name="what-do-i-need-on-premises"></a>Vad gör jag behöver på plats?
På den lokala måste du Site Recovery-komponenter installeras på en enda VMware VM. Du behöver också en VMware-infrastruktur, med minst en ESXi-värden och vi rekommenderar en vCenter-server. Dessutom behöver du en eller flera virtuella VMware-datorer att replikera. [Läs mer](vmware-azure-architecture.md) om VMware till Azure-arkitektur.

Den lokala konfigurationsservern kan distribueras på något av följande två sätt

1. Distribuera det med hjälp av en mall som har konfigurationsservern förinstallerat. [Läs mer här](vmware-azure-tutorial.md#download-the-vm-template).
2. Distribuera det med installationen på en Windows Server 2016-dator väljer. [Läs mer här](physical-azure-disaster-recovery.md#set-up-the-source-environment).

För att identifiera steg för att distribuera konfigurationsservern på dina egna Windows Server-datorer i skyddsmål för att aktivera skydd att komma igång, väljer **till Azure > inte virtualiserad/övrigt**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Där replikerar lokala virtuella datorer till?
Data replikeras till Azure storage. När du kör en redundansväxling skapar Site Recovery automatiskt virtuella datorer i Azure från storage-kontot.

## <a name="replication"></a>Replikering

### <a name="what-apps-can-i-replicate"></a>Vilka appar kan jag replikera?
Du kan replikera en app eller arbetsbelastning som körs på en VMware-VM som uppfyller [replikeringskraven](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery har stöd för Programmedveten replikering så att appar kan växlas över och återställde till en intelligent tillstånd. Site Recovery kan integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och har ett nära samarbete med ledande leverantörer, som Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan jag replikera till Azure med ett plats-till-plats-VPN?
Site Recovery replikerar data från en lokal plats till Azure storage via en offentlig slutpunkt eller med offentlig peering i ExpressRoute. Replikering via ett plats-till-plats VPN-nätverk stöds inte.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan jag replikera till Azure med ExpressRoute?
Ja, ExpressRoute kan användas för att replikera datorer till Azure. Site Recovery replikerar data till ett Azure Storage-konto via en offentlig slutpunkt och du måste konfigurera [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) för Site Recovery-replikering. När virtuella datorer som redundansväxlar till Azure-nätverk, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan inte replikera via VPN?

När du replikerar till Azure replikeringstrafik når de offentliga slutpunkterna för ett Azure Storage-konto och därför kan du bara replikera via det offentliga internet med ExpressRoute (offentlig peering) VPN fungerar inte.


### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är kraven för replikerade virtuella datorer?

För replikering, måste en VMware VM köra ett operativsystem som stöds. Dessutom kan måste den virtuella datorn uppfylla kraven för virtuella Azure-datorer. [Läs mer](vmware-physical-azure-support-matrix.md##replicated-machines) i supportmatrisen.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?
Replikeringen är kontinuerlig när du replikerar virtuella VMware-datorer till Azure.

### <a name="can-i-extend-replication"></a>Kan jag utöka replikeringen?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en offlinereplikering första?
Det stöds inte. Begär den här funktionen i den [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?
Ja, kan du undanta diskar från replikering.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar kan replikeras. Operativsystemdisken måste vara en standarddisk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Om jag använder replikeringsgrupper för konsekvens för flera datorer, kan jag lägga till en ny virtuell dator till en befintlig replikeringsgrupp?
Ja, kan du lägga till nya virtuella datorer till en befintlig replikeringsgrupp när du aktiverar replikering för dessa. Du kan inte lägga till en virtuell dator till en befintlig replikeringsgrupp när replikering initieras, och du kan inte skapa en replikeringsgrupp för befintliga virtuella datorer.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan jag ändra virtuella datorer som replikerar genom att lägga till eller ändra storlek på diskar?

Du kan ändra diskens storlek för VMware-replikering till Azure. Om du vill lägga till nya diskar måste du lägga till disken och återaktivera skyddet för den virtuella datorn.

## <a name="configuration-server"></a>Konfigurationsservern

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
[Lär dig mer om](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) uppdaterar konfigurationsservern. Du hittar den senaste uppdatering informationen i den [uppdateringar i Azure-sidan](https://azure.microsoft.com/updates/?product=site-recovery). Du kan också direkt ladda ned den senaste versionen av konfigurationsservern från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="should-i-backup-the-deployed-configuration-server"></a>Bör jag säkerhetskopiera distribuerade konfigurationsservern?
Vi rekommenderar att utföra regelbundna schemalagda säkerhetskopieringar av konfigurationsservern. Den virtuella datorn att växlas tillbaka måste finnas i server-konfigurationsdatabasen för lyckad återställning efter fel och konfigurationsservern måste vara körs och är i anslutet tillstånd. Du kan lära dig mer om vanliga hanteringsaktiviteter för configuration server [här](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>När jag ställer in konfigurationsservern, kan jag hämta och installera MySQL manuellt?
Ja. Hämta MySQL och placera den i den **C:\Temp\ASRSetup** mapp. Installera den manuellt. När du ställer in konfigurationsservern VM och accepterar villkoren MySQL listas som **redan installerat** i **ladda ned och installera**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kan jag undvika att överföra MySQL men låta Site Recovery installerar den?
Ja. Ladda ned MySQL-installationsprogrammet och placera den i den **C:\Temp\ASRSetup** mapp.  När du ställer in konfigurationsservern VM acceptera villkoren och klicka på **ladda ned och installera**, installationsprogram som du har lagt till för att installera MySQL används.
 
### <a name="canl-i-use-the-configuration-server-vm-for-anything-else"></a>CanL jag använder configuration server VM för något annat?
Nej, bör du endast använda den virtuella datorn för konfigurationsservern. 

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>Kan jag ändra det valv som är registrerade på konfigurationsservern?
Nej. När ett valv har registrerats med konfigurationsservern kan inte ändras.

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
### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?
För VMware till Azure är den äldsta återställningspunkten som du kan använda 72 timmar.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur kommer jag åt virtuella Azure-datorer efter redundans?
Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller via Azure ExpressRoute efter redundansväxlingen. Du måste förbereda ett antal saker för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Växlas över data som är elastiska?
Azure är utformat med flexibilitet i fokus. Site Recovery är utformat för redundansväxling till en sekundär Azure-datacenter, i enlighet med serviceavtalet för Azure. Vid redundans vi Kontrollera att dina metadata och valv finns kvar i samma geografiska region som du har valt för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
[Redundans](site-recovery-failover.md) inte automatiskt. Du startar redundansväxlingar med ett enda klick i portalen eller använda [ PowerShell](/powershell/module/azurerm.siterecovery) att utlösa redundans.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag återställa till en annan plats?
Ja, om du växlas över till Azure, du kan växla tillbaka till en annan plats om den ursprungliga som inte är tillgänglig. [Läs mer](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Varför behöver jag en VPN eller ExpressRoute för att återställa efter felet?

När du växlar tillbaka från Azure kopieras data från Azure tillbaka till den lokala virtuella datorn och privat åtkomst krävs.



## <a name="automation-and-scripting"></a>Automatisering och skript

### <a name="can-i-set-up-replication-with-scripting"></a>Kan jag konfigurera replikering med skript?
Ja. Du kan automatisera Site Recovery arbetsflöden med hjälp av Rest API, PowerShell eller Azure SDK. [Mer](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestanda och kapacitet
### <a name="can-i-throttle-replication-bandwidth"></a>Kan jag begränsa bandbredden för replikering?
Ja. [Läs mer](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Nästa steg
* [Granska](vmware-physical-azure-support-matrix.md) supportkrav.
* [Konfigurera](vmware-azure-tutorial.md) VMware till Azure-replikering.
