---
title: 'Azure Site Recovery: Vanliga frågor och svar | Microsoft Docs'
description: Den här artikeln beskriver vanliga frågor om Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/08/2019
ms.author: raynew
ms.openlocfilehash: 74ccc76ff139cae21e3583b0fea11596f5fd6b62
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413931"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: vanliga frågor (och svar FAQ)
Den här artikeln sammanfattas vanliga frågor och svar om Azure Site Recovery.</br>
Specifika frågor på olika ASR scenarier finns scenariot specifika vanliga frågor och svar.<br>

- [Azure VM-katastrofåterställning till Azure](azure-to-azure-common-questions.md)
- [Haveriberedskap för virtuella VMware-datorer till Azure](vmware-azure-common-questions.md)
- [Haveriberedskap för virtuella Hyper-V-datorer till Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Allmänt

### <a name="what-does-site-recovery-do"></a>Vad gör Site Recovery?
Site Recovery bidrar till affärskontinuitet och haveriberedskap (BCDR), genom att samordna och automatisera replikeringen av virtuella Azure-datorer mellan regioner, lokala virtuella datorer och fysiska servrar till Azure och lokala datorer till en sekundärt datacenter. [Läs mer](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Kan jag skydda en virtuell dator som har en Docker-disk?

Nej, detta är ett scenario som inte stöds.

## <a name="service-providers"></a>Tjänstleverantörer

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jag är en tjänstleverantör. Fungerar Site Recovery för dedikerade och delade infrastrukturmodeller?
Ja, Site Recovery stöder både dedikerade och delade infrastrukturmodeller.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>För en service provider, är identiteten för min klient som delas med Site Recovery-tjänsten?
Nej. Identiteten för klienten fortfarande är anonyma. Klienterna behöver inte åtkomst till Site Recovery-portalen. Endast tjänstproviderns administratör interagerar med portalen.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Kommer klient programdata någonsin gå till Azure?
När du replikerar mellan platser som ägs av tjänstproviders hamnar programdata aldrig hos Azure. Data krypteras under överföring och replikeras direkt mellan tjänstproviderns platser.

Om du replikera till Azure skickas programdata till Azure-lagring men inte till Site Recovery-tjänsten. Data krypteras under överföringen och förblir krypterade i Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Får mina klienter en faktura för några Azure-tjänster?
Nej. Azures faktureringsrelation sker direkt med tjänstprovidern. Tjänstprovidern ansvarar för att skapa fakturor för sina klienter.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Måste jag alltid köra virtuella datorer i Azure om jag replikerar till Azure?
Nej, Data replikeras till Azure storage i din prenumeration. När du utför ett redundanstest (DR-test) eller en riktig redundansväxling skapar Site Recovery automatiskt virtuella datorer i din prenumeration.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garanterar ni isolering på klientnivå när jag replikerar till Azure?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Vilka plattformar stöds för närvarande?
Vi har stöd för Azure-paket, Cloud Platform System, och System Center-baserade distributioner (2012 och senare). [Läs mer](https://technet.microsoft.com/library/dn850370.aspx) om integrering av Azure-paket och Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Finns det stöd för distributioner med ett enda Azure Pack och en enda VMM-server?
Ja, du kan replikera virtuella Hyper-V-datorer till Azure, eller mellan en tjänstproviders platser.  Observera att om du replikerar mellan en tjänstproviders platser, Azure runbook-integrering är inte tillgänglig.

## <a name="pricing"></a>Prissättning

### <a name="where-can-i-find-pricing-information"></a>Var hittar jag information om priser?
Granska [priserna för Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) information.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hur kan jag för att beräkna ungefärliga avgifter vid användning av Site Recovery?

Du kan använda den [priskalkylator](https://aka.ms/asr_pricing_calculator) uppskatta kostnader när du använder Site Recovery.

För detaljerad uppskattning på kostnader och köra distributionsplaneringsverktyget för [VMware](https://aka.ms/siterecovery_deployment_planner) eller [Hyper-V](https://aka.ms/asr-deployment-planner), och använda den [kostnad rapporten kostnadsuppskattning](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Hanterade diskar används nu för att replikera virtuella VMware-datorer och fysiska servrar. Debiteras ytterligare avgifter för cachelagringskontot med hanterade diskar?

Nej, det finns inga ytterligare avgifter för cache. När du replikerar till standard storage-konto är en del av samma mål-lagringskontot i den här cachelagring.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jag har använt Azure Site Recovery i över en månad. Får jag ändå de första 31 dagarna kostnadsfritt för varje skyddad instans?

Ja. Varje skyddad instans är fri från Azure Site Recovery-avgifter under de första 31 dagarna. Till exempel om du har skyddat 10 instanser under de senaste 6 månaderna och ansluter en 11: e instans till Azure Site Recovery, finns det inga avgifter för den 11: e instansen under de första 31 dagarna. Första 10 instanserna debiteras även Azure Site Recovery debiteras, eftersom de har skyddats i mer än 31 dagar.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Förekommer det några andra avgifter för Azure under de första 31 dagarna?

Ja, även om Site Recovery är kostnadsfritt under de första 31 dagarna för en skyddad instans kan du komma att debiteras för Azure Storage, lagringstransaktioner och dataöverföring. En återställd virtuell dator kan också debiteras för Azure-beräkningsavgifter.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Kostar det om du vill utföra disaster recovery-test/redundanstest?

Det finns ingen separat kostnad för DR-test. Det är beräkningsavgifter när den virtuella datorn skapas efter redundanstestet.



## <a name="security"></a>Säkerhet

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?
Nej, Site Recovery komma åt inte replikerade data och har inte någon information om vad som körs på dina virtuella datorer eller fysiska servrar.
Replikeringsdata utbyts mellan lokala Hyper-V-värdar, VMware-hypervisorer eller fysiska servrar och Azure-lagring eller din sekundära plats. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001: 2013, 27018, HIPAA, DPA certifierade och håller på att SOC2 och FedRAMP JAB-utvärderingar.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Av kompatibilitetsskäl finnas även våra lokala metadata inom samma geografiska område. Site Recovery kan hjälpa oss?
Ja. När du skapar ett Site Recovery-valv i en region kan vi se till att alla metadata som vi behöver att aktivera och samordna replikering och redundans finns inom den regionen är geografisk gräns.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
För virtuella datorer och fysiska servrar stöds replikerar mellan lokala platser kryptering under överföring. För virtuella datorer och fysiska servrar replikerar till Azure, både kryptering under överföring och [kryptering i vila (i Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.




## <a name="disaster-recovery"></a>Haveriberedskap

### <a name="what-can-site-recovery-protect"></a>Vad kan Site Recovery skydda?
* **Virtuella Azure-datorer**: Site Recovery kan replikera alla arbetsbelastningar som körs på en stöds Azure VM
* **Hyper-V-datorer**: Site Recovery kan skydda alla arbetsbelastningar som körs på en Hyper-V virtuell dator.
* **Fysiska servrar**: Site Recovery kan skydda fysiska servrar som kör Windows eller Linux.
* **Virtuella VMware-datorer**: Site Recovery kan skydda alla arbetsbelastningar som körs i en VMware-VM.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Vilka arbetsbelastningar kan jag skydda med Site Recovery?
Du kan använda Site Recovery för att skydda de flesta arbetsbelastningar som körs på en stöds virtuell dator eller fysisk server. Site Recovery har stöd för Programmedveten replikering så att appar kan återställas till en intelligent tillstånd. Den integrerar med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och har ett nära samarbete med ledande leverantörer, som Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kan jag hantera haveriberedskap för mina avdelningskontor med Site Recovery?
Ja. När du använder Site Recovery för att samordna replikering och redundans på ditt avdelningskontor, får du en enhetlig orkestrering och vy över alla arbetsbelastningar på en central plats. Du kan enkelt köra redundansväxlingar och administrera haveriberedskap för alla avdelningskontor från huvudkontoret, utan att besöka avdelningskontoren.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Stöds haveriberedskap för virtuella Azure-datorer?

Ja, Site Recovery har stöd för haveriberedskap för virtuella Azure-datorer mellan Azure-regioner. [Granska frågor](azure-to-azure-common-questions.md) om haveriberedskap för virtuella Azure-datorer.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Stöds haveriberedskap för virtuella VMware-datorer?

Ja, Site Recovery stöder haveriberedskap för lokala virtuella VMware-datorer. [Granska frågor](vmware-azure-common-questions.md) för haveriberedskap för virtuella VMware-datorer.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Stöds katastrofåterställning för Hyper-V-datorer?
Ja, Site Recovery stöder haveriberedskap för lokala Hyper-V-datorer. [Granska frågor](hyper-v-azure-common-questions.md) för haveriberedskap för Hyper-V-datorer.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Stöds haveriberedskap för fysiska servrar?
Ja, Site Recovery stöder haveriberedskap för lokala fysiska servrar som kör Windows och Linux till Azure eller till en sekundär plats. Läs om krav på haveriberedskap till [Azure](vmware-physical-azure-support-matrix.md#replicated-machines), och[en sekundär plats](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Observera att fysiska servrar körs som virtuella datorer i Azure efter en redundansväxling. Återställning efter fel från Azure till en lokal fysisk server stöds inte för närvarande. Du kan bara växla tillbaka till en virtuell VMware-dator.





## <a name="replication"></a>Replikering

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kan jag replikera via ett VPN för plats-till-plats till Azure?
Azure Site Recovery replikerar data till ett Azure storage-konto eller hanterade diskar via en offentlig slutpunkt. Replikering är inte via ett plats-till-plats-VPN. 

### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan inte replikera via VPN?

När du replikerar till Azure når replikeringstrafiken de offentliga slutpunkterna av ett Azure Storage. Därför kan du bara replikera via det offentliga internet med ExpressRoute (offentlig peering) och VPN fungerar inte.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Kan jag använda Riverbed SteelHeads för replikering?

Vår partner Riverbed, ger detaljerad vägledning om hur du arbetar med Azure Site Recovery. Granska deras [lösningsguide](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kan jag använda ExpressRoute för att replikera virtuella datorer till Azure?
Ja, [ExpressRoute kan användas för](concepts-expressroute-with-site-recovery.md) att replikera lokala virtuella datorer till Azure.

- Azure Site Recovery replikerar data till Azure Storage via en offentlig slutpunkt. Du måste konfigurera [offentlig peering](../expressroute/expressroute-circuit-peerings.md#publicpeering) eller [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) använder ExpressRoute under Site Recovery-replikering.
- Microsoft-peering är den rekommenderade routningsdomän för replikering.
- När de virtuella datorerna har redundansväxlats till en Azure-nätverk kan du komma åt dem med hjälp av den [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) installation med virtuella Azure-nätverket.
- Replikering stöds inte över privat peering.
- Om du skyddar VMware-datorer eller fysiska datorer, se till att konfigurationsservern uppfyller [nätverkskraven](vmware-azure-configuration-server-requirements.md#network-requirements) för replikering. 



### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Om jag replikerar till Azure, vilken typ av storage-konto eller hanterad disk behöver jag?

Du behöver en LRS eller GRS-lagring. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Kontot måste finnas i samma region som Recovery Services-valvet. Premium storage stöds för VMware VM, Hyper-V-dator och fysisk serverreplikering, när du distribuerar Site Recovery i Azure-portalen. Hanterade diskar endast stöder LRS.

### <a name="how-often-can-i-replicate-data"></a>Hur ofta kan jag replikera data?
* **Hyper-V:** Hyper-V-datorer kan replikeras var femte minut, o 30 sekunder (förutom för premium storage)
* **Azure virtuella datorer, virtuella VMware-datorer, fysiska servrar:** En replikeringsfrekvensen är irrelevant här. Replikeringen är kontinuerlig.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kan jag utöka replikeringen från den befintliga återställningsplatsen till en annan tertiär plats?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kan jag göra en offlinereplikering första gången jag replikerar till Azure?
Det stöds inte. Begär den här funktionen i den [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kan jag undanta specifika diskar från replikeringen?
Detta stöds när du replikerar virtuella VMware-datorer och Hyper-V-datorer till Azure med Azure-portalen.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar stöds vid replikering av Hyper-V-datorer och när du replikerar virtuella VMware-datorer och fysiska datorer till Azure. Operativsystemdisken måste vara en standarddisk.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Kan jag begränsa bandbredden som tilldelas för replikeringstrafik?
Ja. Du kan läsa mer om hur du begränsar bandbredd i de här artiklarna:

* [Planera kapacitet för replikering av virtuella VMware-datorer och fysiska servrar](site-recovery-plan-capacity-vmware.md)
* [Kapacitetsplanering för Hyper-V-datorer som replikeras till Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Redundans
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Om jag redundansväxla till Azure, hur kommer jag åt virtuella Azure-datorer efter redundans?

Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller över Azure ExpressRoute. Du måste du förbereda ett antal saker för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Om jag växlar över till Azure hur Azure gör att Mina data är flexibla?
Azure är utformat med flexibilitet i fokus. Site Recovery är redan utformat för redundansväxling till en sekundär Azure-datacenter, i enlighet med serviceavtalet för Azure. Om detta inträffar kan vi Kontrollera att dina metadata och valv finns kvar i samma geografiska region som du har valt för ditt valv.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Om jag replikerar mellan två Datacenter vad händer om min primära datacenter uppstår ett oväntat avbrott?
Du kan utlösa en oplanerad redundansväxling från den sekundära platsen. Site Recovery behöver ingen anslutning från den primära platsen för att utföra redundansväxlingen.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
Den sker inte automatiskt. Du startar redundansväxlingar med ett enda klick i portalen eller använda [Site Recovery PowerShell](/powershell/module/az.recoveryservices) att utlösa redundans. Växlar tillbaka är en enkel åtgärd i Site Recovery-portalen.

Om du vill automatisera du kan använda lokala Orchestrator eller Operations Manager för att identifiera fel på virtuella datorer och sedan utlösa en redundansväxling med hjälp av SDK.

* [Läs mer](site-recovery-create-recovery-plans.md) om återställningsplaner.
* [Läs mer](site-recovery-failover.md) om redundans.
* [Läs mer](site-recovery-failback-azure-to-vmware.md) om hur du återställer virtuella VMware-datorer och fysiska servrar

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Om min lokala värd svarar inte eller kraschade, kan jag återställa till en annan värd?
Ja, du kan använda den alternativa platsåterställningen för att växla tillbaka till en annan värd från Azure.

* [För virtuella VMware-datorer](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [För Hyper-V-datorer](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan jag automatisera Site Recovery-scenarier med ett SDK?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Scenarier som stöds för närvarande för att distribuera Site Recovery med PowerShell:

* [Replikera Hyper-V-datorer i VMMs-moln till Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replikera virtuella Hyper-V-datorer utan VMM till Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replikera VMware till Azure med PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Komponent-leverantören uppgradering

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Var hittar jag viktig information/samlade uppdateringar av Site Recovery-uppgraderingar

[Lär dig](site-recovery-whats-new.md) om nya uppdateringar och [Hämta samlad information](service-updates-how-to.md).

## <a name="next-steps"></a>Nästa steg
* Läs [Site Recovery-översikten](site-recovery-overview.md)

