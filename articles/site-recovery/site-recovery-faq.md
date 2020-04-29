---
title: Allmänna frågor om tjänsten Azure Site Recovery
description: I den här artikeln beskrivs populära allmänna frågor om Azure Site Recovery.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257685"
---
# <a name="general-questions-about-azure-site-recovery"></a>Allmänna frågor om Azure Site Recovery

I den här artikeln sammanfattas vanliga frågor om Azure Site Recovery. Granska de här artiklarna för vissa scenarier

- [Frågor om haveri beredskap för virtuella Azure-datorer till Azure](azure-to-azure-common-questions.md)
- [Frågor om haveri beredskap för virtuella VMware-datorer till Azure](vmware-azure-common-questions.md)
- [Frågor om haveri beredskap för virtuella Hyper-V-datorer till Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Allmänt

### <a name="what-does-site-recovery-do"></a>Vad gör Site Recovery?
Site Recovery bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR) genom att dirigera och automatisera replikeringen av virtuella Azure-datorer mellan regioner, lokala virtuella datorer och fysiska servrar till Azure och lokala datorer till ett sekundärt Data Center. [Läs mer](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Kan jag skydda en virtuell dator som har en Docker-disk?

Nej, det här är ett scenario som inte stöds.

## <a name="service-providers"></a>Tjänst leverantörer

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jag är en tjänste leverantör. Fungerar Site Recovery för dedikerade och delade infrastruktur modeller?
Ja, Site Recovery stöder både dedikerade och delade infrastrukturmodeller.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Är identiteten för min klient som delas med Site Recovery tjänsten för en tjänst leverantör?
Nej. Klient identiteten är fortfarande anonym. Klienterna behöver inte åtkomst till Site Recovery-portalen. Endast tjänstproviderns administratör interagerar med portalen.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Kommer klient program data någonsin att gå till Azure?
När du replikerar mellan platser som ägs av tjänstproviders hamnar programdata aldrig hos Azure. Data krypteras under överföringen och replikeras direkt mellan tjänste leverantörs platserna.

Om du replikera till Azure skickas programdata till Azure-lagring men inte till Site Recovery-tjänsten. Data krypteras under överföringen och förblir krypterade i Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Får mina klienter en faktura för några Azure-tjänster?
Nej. Azures faktureringsrelation sker direkt med tjänstprovidern. Tjänstprovidern ansvarar för att skapa fakturor för sina klienter.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Måste jag alltid köra virtuella datorer i Azure om jag replikerar till Azure?
Nej, data replikeras till Azure Storage i din prenumeration. När du utför ett redundanstest (DR-test) eller en riktig redundansväxling skapar Site Recovery automatiskt virtuella datorer i din prenumeration.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garanterar ni isolering på klientnivå när jag replikerar till Azure?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Vilka plattformar stöds för närvarande?
Vi har stöd för Azure-paket, moln plattforms system och System Center-baserade (2012 och högre) distributioner. [Läs mer](https://technet.microsoft.com/library/dn850370.aspx) om Azure-paket och Site Recovery-integrering.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Finns det stöd för distributioner med ett enda Azure Pack och en enda VMM-server?
Ja, du kan replikera virtuella Hyper-V-datorer till Azure, eller mellan Service Provider-platser.  Observera att Azure Runbook-integrering inte är tillgängligt om du replikerar mellan platser för tjänst leverantörer.

## <a name="pricing"></a>Prissättning

### <a name="where-can-i-find-pricing-information"></a>Var kan jag hitta pris information?
Granska [Site Recovery pris](https://azure.microsoft.com/pricing/details/site-recovery/) information.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hur kan jag beräkna ungefärliga avgifter vid användning av Site Recovery?

Du kan använda [pris kalkylatorn](https://aka.ms/asr_pricing_calculator) för att beräkna kostnader när du använder Site Recovery.

Om du vill ha en detaljerad uppskattning av kostnader kör du verktyget Deployment Planner för [VMware](https://aka.ms/siterecovery_deployment_planner) eller [Hyper-V](https://aka.ms/asr-deployment-planner)och använder [rapporten kostnads uppskattning](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Hanterade diskar används nu för att replikera virtuella VMware-datorer och fysiska servrar. Debiteras jag ytterligare avgifter för cache Storage-kontot med Managed disks?

Nej, det finns inga ytterligare avgifter för cachelagring. När du replikerar till ett standard lagrings konto är denna cache del av samma mål lagrings konto.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jag har varit Azure Site Recovery användare under en månad. Får jag fortfarande de första 31 dagarna kostnads fritt för varje skyddad instans?

Ja. Alla skyddade instanser debiteras inga Azure Site Recovery avgifter under de första 31 dagarna. Om du till exempel har skyddat 10 instanser under de senaste 6 månaderna och ansluter en 11: e instans till Azure Site Recovery, finns det inga avgifter för den 11: e instansen under de första 31 dagarna. De första 10 instanserna fortsätter att ådra sig Azure Site Recovery avgifter eftersom de har skyddats i mer än 31 dagar.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Under de första 31 dagarna kommer jag att debiteras andra Azure-avgifter?

Ja, även om Site Recovery är kostnads fritt under de första 31 dagarna i en skyddad instans kan du debiteras för Azure Storage, lagrings transaktioner och data överföring. En återställd virtuell dator kan också ådra sig Azure Compute-avgifter.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Är det en kostnad som är kopplad till att utföra haveri beredskap/redundanstest?

Det finns ingen separat kostnad för katastrof granskning. Det kommer att finnas beräknings avgifter när den virtuella datorn har skapats efter redundanstest.



## <a name="security"></a>Säkerhet

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?
Nej, Site Recovery inte fångar upp replikerade data och har inte någon information om vad som körs på dina virtuella datorer eller fysiska servrar.
Replikeringsdata utbyts mellan lokala Hyper-V-värdar, VMware-hypervisorer eller fysiska servrar och Azure-lagring eller din sekundära plats. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001:2013, 27018, HIPAA, DPA Certified och är i processen för SOC2-och FedRAMP JAB-utvärderingar.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Av kompatibilitetsskäl måste även våra lokala metadata finnas kvar i samma geografiska region. Kan Site Recovery hjälpa oss?
Ja. När du skapar ett Site Recovery valv i en region ser vi till att alla metadata som vi behöver för att aktivera och dirigera replikering och redundans kvarstår inom regionens geografiska gränser.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
För virtuella datorer och fysiska servrar stöds replikering mellan lokala platser vid kryptering under överföring. För virtuella datorer och fysiska servrar som replikeras till Azure stöds både kryptering under överföring och [kryptering vid vila (i Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) .

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>Hur kan jag använda TLS 1,2 på alla lokala Azure Site Recovery-komponenter?
Mobilitets agenter som är installerade på replikerade objekt kommunicerar endast med processervern på TLS 1,2. Kommunikationen från konfigurations servern till Azure och från processervern till Azure kan dock finnas i TLS 1,1 eller 1,0. Följ [anvisningarna för att](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) genomdriva TLS 1,2 på alla konfigurations servrar och process servrar som du har skapat.


## <a name="disaster-recovery"></a>Haveriberedskap

### <a name="what-can-site-recovery-protect"></a>Vad kan Site Recovery skydda?
* **Virtuella Azure-datorer**: Site Recovery kan replikera alla arbets belastningar som körs på en virtuell Azure-dator som stöds
* **Virtuella Hyper-v-datorer**: Site Recovery kan skydda alla arbets belastningar som körs på en virtuell Hyper-v-dator.
* **Fysiska servrar**: Site Recovery kan skydda fysiska servrar som kör Windows eller Linux.
* **Virtuella VMware-datorer**: Site Recovery kan skydda alla arbets belastningar som körs i en virtuell VMware-dator.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Vilka arbetsbelastningar kan jag skydda med Site Recovery?
Du kan använda Site Recovery för att skydda de flesta arbets belastningar som körs på en virtuell dator eller fysisk server som stöds. Site Recovery ger stöd för program medveten replikering, så att appar kan återställas till ett intelligent tillstånd. Den integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och fungerar nära ledande leverantörer, inklusive Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kan jag hantera haveriberedskap för mina avdelningskontor med Site Recovery?
Ja. När du använder Site Recovery för att dirigera replikering och redundans på avdelnings kontoren får du ett enhetligt dirigering och en översikt över alla arbets belastningar för arbets belastnings kontor på en central plats. Du kan enkelt köra redundansväxlingar och administrera haveriberedskap för alla avdelningskontor från huvudkontoret, utan att besöka avdelningskontoren.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Stöds haveri beredskap för virtuella Azure-datorer?

Ja, Site Recovery stöder haveri beredskap för virtuella Azure-datorer mellan Azure-regioner. [Läs vanliga frågor](azure-to-azure-common-questions.md) om haveri beredskap för virtuella Azure-datorer.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Stöds haveri beredskap för virtuella VMware-datorer?

Ja, Site Recovery stöder haveri beredskap för lokala virtuella VMware-datorer. [Läs vanliga frågor](vmware-azure-common-questions.md) om haveri beredskap för virtuella VMware-datorer.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Stöds haveri beredskap för virtuella Hyper-V-datorer?
Ja, Site Recovery stöder haveri beredskap för lokala virtuella Hyper-V-datorer. [Läs vanliga frågor](hyper-v-azure-common-questions.md) om haveri beredskap för virtuella Hyper-V-datorer.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Stöds haveri beredskap för fysiska servrar?
Ja, Site Recovery stöder haveri beredskap för lokala fysiska servrar som kör Windows och Linux till Azure eller till en sekundär plats. Läs om kraven för haveri beredskap till [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)och[en sekundär plats](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Observera att fysiska servrar kommer att köras som virtuella datorer i Azure efter en redundansväxling. Återställning efter fel från Azure till en lokal fysisk server stöds inte för närvarande. Du kan bara växla tillbaka till en virtuell VMware-dator.





## <a name="replication"></a>Replikering

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kan jag replikera via en VPN-anslutning från plats till plats till Azure?
Azure Site Recovery replikerar data till ett Azure Storage-konto eller Managed disks över en offentlig slut punkt. Replikeringen är inte över en plats-till-plats-VPN. 

### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan jag inte replikera via VPN?

När du replikerar till Azure når replikeringstrafiken de offentliga slut punkterna för en Azure Storage. Därför kan du bara replikera via offentliga Internet eller via ExpressRoute (Microsoft-peering eller en befintlig offentlig peering).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Kan jag använda Riverbed SteelHeads för replikering?

Vår partner, Riverbed, ger detaljerad vägledning om hur du arbetar med Azure Site Recovery. Granska sin [lösnings guide](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kan jag använda ExpressRoute för att replikera virtuella datorer till Azure?
Ja, [ExpressRoute kan användas](concepts-expressroute-with-site-recovery.md) för att replikera lokala virtuella datorer till Azure.

- Azure Site Recovery replikerar data till en Azure Storage över en offentlig slut punkt. Du måste konfigurera Microsoft- [peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) eller använda en befintlig [offentlig peering](../expressroute/about-public-peering.md) (inaktuell för nya kretsar) för att använda ExpressRoute för Site Recovery replikering.
- Microsoft-peering är den rekommenderade routningsdomänen för replikering.
- Replikering stöds inte för privat peering.
- Om du skyddar VMware-datorer eller fysiska datorer kontrollerar du att [nätverks kraven](vmware-azure-configuration-server-requirements.md#network-requirements) för konfigurations servern också är uppfyllda. Anslutning till vissa URL: er krävs av konfigurations servern för att dirigera Site Recovery replikering. ExpressRoute kan inte användas för den här anslutningen.
- När de virtuella datorerna har redundansväxlats till ett virtuellt Azure-nätverk kan du komma åt dem med hjälp av den [privata peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) -installationen med det virtuella Azure-nätverket.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Om jag replikerar till Azure, vilken typ av lagrings konto eller hanterad disk behöver jag?

Du behöver en LRS-eller GRS-lagring. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Kontot måste finnas i samma region som Recovery Services-valvet. Premium Storage stöds för virtuella VMware-datorer, virtuella Hyper-V-datorer och replikering av fysiska servrar när du distribuerar Site Recovery i Azure Portal. Managed disks stöder endast LRS.

### <a name="how-often-can-i-replicate-data"></a>Hur ofta kan jag replikera data?
* **Hyper-V:** Virtuella Hyper-V-datorer kan replikeras var 30: e sekund (med undantag för Premium Storage), fem minuter eller 15 minuter.
* **Virtuella Azure-datorer, virtuella VMware-datorer och fysiska servrar:** En replikeringsfrekvens är inte relevant här. Replikeringen är kontinuerlig.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kan jag utöka replikeringen från den befintliga återställnings platsen till en annan tertiär plats?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [feedback-forumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kan jag göra en offlinereplikering första gången jag replikerar till Azure?
Det stöds inte. Begär den här funktionen i [feedback-forumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kan jag undanta specifika diskar från replikeringen?
Detta stöds när du replikerar virtuella VMware-datorer och virtuella Hyper-V-datorer till Azure med hjälp av Azure Portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar stöds vid replikering av virtuella Hyper-V-datorer och vid replikering av virtuella VMware-datorer och fysiska datorer till Azure. Operativ system disken måste vara en standard disk.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Kan jag begränsa bandbredden som tilldelas för replikeringstrafik?
Ja. Du kan läsa mer om hur du begränsar bandbredden i de här artiklarna:

* [Kapacitets planering för att replikera virtuella VMware-datorer och fysiska servrar](site-recovery-plan-capacity-vmware.md)
* [Kapacitets planering för att replikera virtuella Hyper-V-datorer till Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Redundans
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Hur får jag åtkomst till virtuella Azure-datorer efter redundansväxlingen om jag växlar över till Azure?

Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller över Azure ExpressRoute. Du måste förbereda ett antal saker för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Om jag växlar över till Azure hur ser Azure till att mina data är elastiska?
Azure är utformat med flexibilitet i fokus. Site Recovery har redan byggts för redundansväxling till ett sekundärt Azure-datacenter, i enlighet med Azure SLA. Om detta inträffar ser vi till att dina metadata och valv finns kvar i samma geografiska region som du valde för ditt valv.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Om jag replikerar mellan två Data Center vad händer om mitt primära Data Center upplever ett oväntat avbrott?
Du kan utlösa en oplanerad redundansväxling från den sekundära platsen. Site Recovery behöver ingen anslutning från den primära platsen för att utföra redundansväxlingen.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
Den sker inte automatiskt. Du initierar redundans med ett enda klick i portalen, eller så kan du använda [Site Recovery PowerShell](/powershell/module/az.recoveryservices) för att utlösa en redundansväxling. Att återställa igen är en enkel åtgärd i Site Recovery portalen.

Om du vill automatisera kan du använda lokala Orchestrator eller Operations Manager för att identifiera ett fel i en virtuell dator och sedan utlösa redundansväxlingen med hjälp av SDK.

* [Läs mer](site-recovery-create-recovery-plans.md) om återställnings planer.
* [Läs mer](site-recovery-failover.md) om redundans.
* [Läs mer](site-recovery-failback-azure-to-vmware.md) om att återställa virtuella VMware-datorer och fysiska servrar

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Kan jag växla tillbaka till en annan värd om min lokala värd inte svarar eller kraschar?
Ja, du kan använda den alternativa plats återställningen för återställning efter fel till en annan värd från Azure.

* [För virtuella VMware-datorer](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [För virtuella Hyper-V-datorer](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan jag automatisera Site Recovery scenarier med ett SDK?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Scenarier som stöds för närvarande för distribution av Site Recovery med hjälp av PowerShell:

* [Replikera virtuella Hyper-V-datorer i VMMs-moln till Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replikera virtuella Hyper-V-datorer utan VMM till Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replikera VMware till Azure med PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Komponent/Provider-uppgradering

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Var hittar jag viktig information/samlade uppdateringar av Site Recovery uppgraderingar

[Lär dig mer](site-recovery-whats-new.md) om nya uppdateringar och [Hämta information om sammanslagning](service-updates-how-to.md).

## <a name="next-steps"></a>Nästa steg
* Läs [Site Recovery-översikten](site-recovery-overview.md)

