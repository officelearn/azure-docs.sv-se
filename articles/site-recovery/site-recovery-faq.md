---
title: Allmänna frågor om Azure Site Recovery-tjänsten
description: I den här artikeln beskrivs populära allmänna frågor om Azure Site Recovery.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257685"
---
# <a name="general-questions-about-azure-site-recovery"></a>Allmänna frågor om Azure Site Recovery

Den här artikeln sammanfattar vanliga frågor om Azure Site Recovery. För specifika scenarier granska dessa artiklar

- [Frågor om Azure VM-haveriberedskap till Azure](azure-to-azure-common-questions.md)
- [Frågor om VMware VM-haveriberedskap till Azure](vmware-azure-common-questions.md)
- [Frågor om hyper-v vm-haveriberedskap till Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Allmänt

### <a name="what-does-site-recovery-do"></a>Vad gör Site Recovery?
Site Recovery bidrar till din strategi för affärskontinuitet och haveriberedskap (BCDR) genom att samordna och automatisera replikering av virtuella Azure-datorer mellan regioner, lokala virtuella datorer och fysiska servrar till Azure och lokala datorer till en sekundärt datacenter. [Läs mer](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Kan jag skydda en virtuell dator som har en Docker-disk?

Nej, det här är ett scenario som inte stöds.

## <a name="service-providers"></a>Tjänsteleverantörer

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jag är tjänsteleverantör. Fungerar Site Recovery för dedikerade och delade infrastrukturmodeller?
Ja, Site Recovery stöder både dedikerade och delade infrastrukturmodeller.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>För en tjänsteleverantör, delas identiteten på min klient organisation med tjänsten Site Recovery?
Nej. Klientidentiteten förblir anonym. Klienterna behöver inte åtkomst till Site Recovery-portalen. Endast tjänstproviderns administratör interagerar med portalen.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Kommer klientprogramdata någonsin att gå till Azure?
När du replikerar mellan platser som ägs av tjänstproviders hamnar programdata aldrig hos Azure. Data krypteras under överföringen och replikeras direkt mellan tjänstleverantörens webbplatser.

Om du replikera till Azure skickas programdata till Azure-lagring men inte till Site Recovery-tjänsten. Data krypteras under överföringen och förblir krypterade i Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Får mina klienter en faktura för några Azure-tjänster?
Nej. Azures faktureringsrelation sker direkt med tjänstprovidern. Tjänstprovidern ansvarar för att skapa fakturor för sina klienter.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Måste jag alltid köra virtuella datorer i Azure om jag replikerar till Azure?
Nej, data replikeras till Azure-lagring i din prenumeration. När du utför ett redundanstest (DR-test) eller en riktig redundansväxling skapar Site Recovery automatiskt virtuella datorer i din prenumeration.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garanterar ni isolering på klientnivå när jag replikerar till Azure?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Vilka plattformar stöds för närvarande?
Vi stöder Azure Pack-, Cloud Platform System- och System Center-baserade (2012 och senare) distributioner. [Läs mer](https://technet.microsoft.com/library/dn850370.aspx) om Integrering av Azure Pack och Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Finns det stöd för distributioner med ett enda Azure Pack och en enda VMM-server?
Ja, du kan replikera virtuella Hyper-V-datorer till Azure eller mellan tjänstproviderplatser.  Observera att om du replikerar mellan tjänstproviderplatser är Azure runbook-integrering inte tillgänglig.

## <a name="pricing"></a>Prissättning

### <a name="where-can-i-find-pricing-information"></a>Var hittar jag prisinformation?
Granska prisinformation för [webbplatsåterställning.](https://azure.microsoft.com/pricing/details/site-recovery/)


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hur kan jag beräkna ungefärliga avgifter under användningen av Site Recovery?

Du kan använda [priskalkylatorn](https://aka.ms/asr_pricing_calculator) för att uppskatta kostnaderna när du använder Site Recovery.

Om du vill ha detaljerad uppskattning av kostnader kör du distributionsplaneraren för [VMware](https://aka.ms/siterecovery_deployment_planner) eller [Hyper-V](https://aka.ms/asr-deployment-planner)och använder [kostnadsuppskattningsrapporten](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Hanterade diskar används nu för att replikera virtuella datorer med VMware och fysiska servrar. Debiterar jag ytterligare avgifter för cachelagringskontot med hanterade diskar?

Nej, det finns inga extra avgifter för cache. När du replikerar till standardlagringskonto är den här cachelagringen en del av samma mållagringskonto.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jag har varit azure site recovery-användare i över en månad. Får jag fortfarande de första 31 dagarna gratis för varje skyddad instans?

Ja. Varje skyddad instans medför inga Azure Site Recovery-avgifter för de första 31 dagarna. Om du till exempel har skyddat 10 instanser under de senaste 6 månaderna och du ansluter en elfte instans till Azure Site Recovery, finns det inga avgifter för den 11:e instansen för de första 31 dagarna. De första 10 instanserna fortsätter att ådra sig Azure Site Recovery-avgifter eftersom de har skyddats i mer än 31 dagar.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Under de första 31 dagarna, kommer jag att ådra mig några andra Azure-avgifter?

Ja, även om Site Recovery är gratis under de första 31 dagarna av en skyddad instans kan du debiteras avgifter för Azure Storage, lagringstransaktioner och dataöverföring. En återställd virtuell dator kan också medföra Azure-beräkningsavgifter.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Finns det en kostnad för att utföra haveriberedskapsövningar/testundans?

Det finns ingen separat kostnad för DR-borr. Det kommer att finnas beräkningsavgifter när den virtuella datorn har skapats efter testundansen.



## <a name="security"></a>Säkerhet

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?
Nej, Site Recovery avlyssnar inte replikerade data och har ingen information om vad som körs på dina virtuella datorer eller fysiska servrar.
Replikeringsdata utbyts mellan lokala Hyper-V-värdar, VMware-hypervisorer eller fysiska servrar och Azure-lagring eller din sekundära plats. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001:2013, 27018, HIPAA, DPA-certifierad och håller på att hantera SOC2- och FedRAMP JAB-bedömningar.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Av efterlevnadsskäl måste även våra lokala metadata finnas kvar inom samma geografiska region. Kan Site Recovery hjälpa oss?
Ja. När du skapar ett site recovery-valv i en region ser vi till att alla metadata som vi behöver för att aktivera och dirigera replikering och redundans förblir inom den regionens geografiska gräns.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
För virtuella datorer och fysiska servrar stöds replikera mellan lokala platser kryptering-in-transit. För virtuella datorer och fysiska servrar som replikeras till Azure stöds både kryptering i transit och [kryptering i vila (i Azure).](https://docs.microsoft.com/azure/storage/storage-service-encryption)

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>Hur kan jag framtvinga TLS 1.2 på alla lokala Azure Site Recovery-komponenter?
Mobilitetsagenter som är installerade på replikerade objekt kommunicerar endast till Process Server på TLS 1.2. Kommunikation från Configuration Server till Azure och från Process Server till Azure kan dock finnas på TLS 1.1 eller 1.0. Följ [anvisningarna](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) för att framtvinga TLS 1.2 på alla konfigurationsservrar och processservrar som konfigurerats av dig.


## <a name="disaster-recovery"></a>Haveriberedskap

### <a name="what-can-site-recovery-protect"></a>Vad kan Site Recovery skydda?
* **Virtuella Azure-datorer:** Platsåterställning kan replikera alla arbetsbelastningar som körs på en Azure VM-dator som stöds
* **Hyper-V virtuella datorer:** Site Recovery kan skydda alla arbetsbelastningar som körs på en Hyper-V VM.
* **Fysiska servrar**: Site Recovery kan skydda fysiska servrar som kör Windows eller Linux.
* **Virtuella VMware-datorer:** Site Recovery kan skydda alla arbetsbelastningar som körs i en virtuell virtuell VMware-dator.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Vilka arbetsbelastningar kan jag skydda med Site Recovery?
Du kan använda Site Recovery för att skydda de flesta arbetsbelastningar som körs på en virtuell dator eller fysisk server som stöds. Site Recovery ger stöd för programmedveten replikering, så att appar kan återställas till ett intelligent tillstånd. Den integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och har ett nära samarbete med ledande leverantörer, inklusive Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kan jag hantera haveriberedskap för mina avdelningskontor med Site Recovery?
Ja. När du använder Site Recovery för att dirigera replikering och redundans på filialkontoren får du en enhetlig orkestrering och vy över alla arbetsbelastningar på filialkontoret på en central plats. Du kan enkelt köra redundansväxlingar och administrera haveriberedskap för alla avdelningskontor från huvudkontoret, utan att besöka avdelningskontoren.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Stöds haveriberedskap för virtuella Azure-datorer?

Ja, Site Recovery stöder katastrof för virtuella Azure-datorer mellan Azure-regioner. [Granska vanliga frågor](azure-to-azure-common-questions.md) om azure VM-haveriberedskap.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Stöds haveriberedskap för virtuella datorer med VMware?

Ja, Site Recovery stöder haveriberedskap av lokala virtuella datorer med VMware. [Granska vanliga frågor](vmware-azure-common-questions.md) för haveriberedskap av virtuella datorer med VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Stöds haveriberedskap för virtuella hyper-virtuella datorer?
Ja, Site Recovery stöder haveriberedskap av lokala virtuella hyper-virtuella datorer. [Granska vanliga frågor](hyper-v-azure-common-questions.md) för haveriberedskap av virtuella hyper-virtuella datorer.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Stöds haveriberedskap för fysiska servrar?
Ja, Site Recovery stöder haveriberedskap av lokala fysiska servrar som kör Windows och Linux till Azure eller till en sekundär plats. Lär dig mer om krav för haveriberedskap till [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)och till[en sekundär plats](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Observera att fysiska servrar körs som virtuella datorer i Azure efter redundans. Återställning från Azure till en lokal fysisk server stöds för närvarande inte. Du kan bara växla tillbaka till en virtuell VMware-dator.





## <a name="replication"></a>Replikering

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kan jag replikera via en plats-till-plats-VPN till Azure?
Azure Site Recovery replikerar data till ett Azure-lagringskonto eller hanterade diskar, över en offentlig slutpunkt. Replikering är inte över en plats-till-plats-VPN. 

### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan jag inte replikera via VPN?

När du replikerar till Azure når replikeringstrafiken de offentliga slutpunkterna för en Azure Storage. På så sätt kan du bara replikera via det offentliga internet eller via ExpressRoute (Microsoft peering eller en befintlig offentlig peering).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Kan jag använda Riverbed SteelHeads för replikering?

Vår partner Riverbed ger detaljerad vägledning om hur du arbetar med Azure Site Recovery. Granska deras [lösning guide](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kan jag använda ExpressRoute för att replikera virtuella datorer till Azure?
Ja, [ExpressRoute kan användas](concepts-expressroute-with-site-recovery.md) för att replikera lokala virtuella datorer till Azure.

- Azure Site Recovery replikerar data till en Azure Storage via en offentlig slutpunkt. Du måste konfigurera [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) eller använda en befintlig [offentlig peering](../expressroute/about-public-peering.md) (föråldrad för nya kretsar) för att använda ExpressRoute för site recovery-replikering.
- Microsoft-peering är den rekommenderade routningsdomänen för replikering.
- Replikering stöds inte via privat peering.
- Om du skyddar VMware-datorer eller fysiska datorer kontrollerar du att [nätverkskraven](vmware-azure-configuration-server-requirements.md#network-requirements) för konfigurationsservern också uppfylls. Anslutning till specifika url:er krävs av Configuration Server för orkestrering av site recovery-replikering. ExpressRoute kan inte användas för den här anslutningen.
- När de virtuella datorerna har misslyckats över till ett virtuellt Azure-nätverk kan du komma åt dem med hjälp av den [privata peering-konfigurationen](../expressroute/expressroute-circuit-peerings.md#privatepeering) med det virtuella Azure-nätverket.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Om jag replikerar till Azure, vilken typ av lagringskonto eller hanterad disk behöver jag?

Du behöver en LRS eller GRS lagring. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Kontot måste finnas i samma region som Recovery Services-valvet. Premiumlagring stöds för virtuell vmware-dator, virtuell dator med hyper-v och fysisk serverreplikering när du distribuerar platsåterställning i Azure-portalen. Hanterade diskar stöder endast LRS.

### <a name="how-often-can-i-replicate-data"></a>Hur ofta kan jag replikera data?
* **Hyper-V:** Virtuella datorer med hyper-V kan replikeras var 30:e sekund (förutom premiumlagring), fem minuter eller 15 minuter.
* **Virtuella Azure-datorer, virtuella virtuella datorer med VMware, fysiska servrar:** En replikeringsfrekvens är inte relevant här. Replikeringen är kontinuerlig.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kan jag utöka replikeringen från befintlig återställningsplats till en annan tertiär plats?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kan jag göra en offlinereplikering första gången jag replikerar till Azure?
Det stöds inte. Begär den här funktionen i [feedbackforumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kan jag undanta specifika diskar från replikeringen?
Detta stöds när du replikerar virtuella virtuella datorer med VMware och virtuella virtuella hyper-v-datorer till Azure med hjälp av Azure-portalen.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar stöds vid replikerande hyper-V virtuella datorer och vid replikerande virtuella virtuella datorer och fysiska datorer till Azure. Operativsystemets disk måste vara en enkel disk.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Kan jag begränsa bandbredden som tilldelats för replikeringstrafik?
Ja. Du kan läsa mer om begränsning bandbredd i dessa artiklar:

* [Kapacitetsplanering för replikering av virtuella datorer och fysiska servrar](site-recovery-plan-capacity-vmware.md)
* [Kapacitetsplanering för replikering av virtuella hyper-virtuella datorer till Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Redundans
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Om jag misslyckas med Azure, hur kommer jag åt virtuella Azure-datorer efter redundans?

Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller över Azure ExpressRoute. Du måste förbereda ett antal saker för att ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Om jag växlar över till Azure hur ser Azure till att mina data är elastiska?
Azure är utformat med flexibilitet i fokus. Site Recovery är redan konstruerad för redundans till ett sekundärt Azure-datacenter, i enlighet med Azure SLA. Om detta inträffar ser vi till att dina metadata och valv ligger inom samma geografiska region som du valde för ditt valv.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Om jag replikerar mellan två datacenter, vad händer om mitt primära datacenter uppstår ett oväntat avbrott?
Du kan utlösa en oplanerad redundansväxling från den sekundära platsen. Site Recovery behöver ingen anslutning från den primära platsen för att utföra redundansväxlingen.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
Den sker inte automatiskt. Du initierar redundans med ett enda klick i portalen, eller så kan du använda [Site Recovery PowerShell](/powershell/module/az.recoveryservices) för att utlösa en redundans. Att misslyckas tillbaka är en enkel åtgärd i portalen för webbplatsåterställning.

För att automatisera kan du använda lokala Orchestrator eller Operations Manager för att identifiera ett fel på den virtuella datorn och sedan utlösa redundansen med SDK.

* [Läs mer](site-recovery-create-recovery-plans.md) om återhämtningsplaner.
* [Läs mer](site-recovery-failover.md) om redundans.
* [Läs mer](site-recovery-failback-azure-to-vmware.md) om att misslyckas tillbaka virtuella datorer och fysiska servrar

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Om min lokala värd inte svarar eller kraschar, kan jag växla tillbaka till en annan värd?
Ja, du kan använda den alternativa platsåterställning för att återställa en annan värd från Azure.

* [För virtuella VMware-datorer](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [För virtuella Hyper-V-datorer](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan jag automatisera scenarier för webbplatsåterställning med en SDK?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Scenarier som stöds för distribution av webbplatsåterställning med PowerShell:

* [Replikera virtuella hyper-virtuella datorer i VMMs-moln till Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replikera virtuella hyper-virtuella datorer utan VMM till Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replikera VMware till Azure med PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Uppgradering av komponent/provider

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Var hittar jag viktig information/samlade uppdateringar av uppgraderingar av site recovery

[Lär dig mer](site-recovery-whats-new.md) om nya uppdateringar och [få samlad information](service-updates-how-to.md).

## <a name="next-steps"></a>Nästa steg
* Läs [Site Recovery-översikten](site-recovery-overview.md)

