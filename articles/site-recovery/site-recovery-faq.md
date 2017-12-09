---
title: "Azure Site Recovery: Vanliga frågor och svar | Microsoft Docs"
description: "Den här artikeln beskrivs vanliga frågor om Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: raynew
ms.openlocfilehash: ad6f70cf9c2f420e887031c8b240d2f831e6c359
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: Vanliga frågor och svar (FAQ)
Den här artikeln innehåller vanliga frågor och svar om Azure Site Recovery. Om du har frågor när du har läst den här artikeln kan publicera dem på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Allmänt
### <a name="what-does-site-recovery-do"></a>Vad gör Site Recovery?
Site Recovery bidrar till din affärskontinuitet och haveriberedskap (BCDR), genom att samordna och automatisera replikering av virtuella Azure-datorer mellan regioner, lokala virtuella datorer och fysiska servrar till Azure och lokala datorer till en sekundärt datacenter. [Läs mer](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Vad kan Site Recovery skydda?
* **Virtuella Azure-datorer**: Site Recovery kan replikera alla arbetsbelastningar som körs på en stöds Azure VM
* **Hyper-V-datorer**: Site Recovery kan skydda alla arbetsbelastningar som körs på en Hyper-V virtuell dator.
* **Fysiska servrar**: Site Recovery kan skydda fysiska servrar som kör Windows eller Linux.
* **Virtuella VMware-datorer**: Site Recovery kan skydda alla arbetsbelastningar som körs i ett VMware-VM.



### <a name="can-i-replicate-azure-vms"></a>Kan jag replikera virtuella datorer i Azure?
Ja, kan du replikera virtuella datorer som stöds Azure mellan Azure-regioner. [Läs mer](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Vad behöver jag i Hyper-V att samordna replikeringen med Site Recovery?
Vad du behöver för Hyper-V-värdservern beror på distributionsscenariot. Du hittar kraven för Hyper-V i:

* [Replikera virtuella Hyper-V-datorer (utan VMM) till Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikera virtuella Hyper-V-datorer (med VMM) till Azure](site-recovery-vmm-to-azure.md)
* [Replikera virtuella Hyper-V-datorer till ett sekundärt datacenter](site-recovery-vmm-to-vmm.md)
* Om du replikerar till ett sekundärt datacenter Läs mer om [operativsystem som stöds för Hyper-V virtuella datorer](https://technet.microsoft.com/library/mt126277.aspx).
* Om du replikerar till Azure Site Recovery stöder alla gästoperativsystem som är [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan jag skydda virtuella datorer när Hyper-V körs på ett klientoperativsystem?
Nej, virtuella datorer måste finnas på en Hyper-V-värdserver som körs på en Windows-serverdator som stöds. Om du behöver skydda en klientdator kan du replikera den som en fysisk dator att [Azure](site-recovery-vmware-to-azure.md) eller en [sekundärt datacenter](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Vilka arbetsbelastningar kan jag skydda med Site Recovery?
Du kan använda Site Recovery för att skydda de flesta arbetsbelastningar som körs på en stöds virtuell eller fysisk server. Site Recovery har stöd för Programmedveten replikering så att appar kan återställas till en intelligent tillstånd. Den kan integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och användas tillsammans med ledande, inklusive Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Behöver Hyper-V-värdar i VMM-moln?
Om du vill replikera till ett sekundärt datacenter Hyper-V-datorer måste vara på Hyper-V är värd för servrar som finns i ett VMM-moln. Om du vill replikera till Azure kan du replikera virtuella datorer med eller utan VMM-moln. [Läs mer](tutorial-hyper-v-to-azure.md) om Hyper-V-replikering till Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kan jag distribuera Site Recovery med VMM om jag bara har en VMM-server?

Ja. Du kan antingen replikera virtuella datorer i Hyper-V-servrar i VMM-moln till Azure eller replikera mellan VMM-moln på samma server. För lokal till lokal replikering rekommenderar vi att du har en VMM-server i både de primära och sekundära platserna.  

### <a name="what-physical-servers-can-i-protect"></a>Vilka fysiska servrar kan jag skydda?
Du kan replikera fysiska servrar som kör Windows och Linux till Azure eller till en sekundär plats. [Lär dig mer om](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) krav på operativsystem.  Samma krav gäller oavsett om du replikerar fysiska servrar till Azure eller till en sekundär plats.


Observera att fysiska servrar körs som virtuella datorer i Azure om lokal server kraschar. Återställning till en lokal fysisk server stöds inte för närvarande. För en dator som skyddas som fysiska kan du endast återställning till en virtuell dator för VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Vilka virtuella VMware-datorer kan jag skydda?

För att skydda virtuella VMware-datorer behöver du en vSphere-hypervisor och virtuella datorer som kör VMware-verktyg. Vi rekommenderar också att du har en VMware vCenter-server för att hantera hypervisorer. [Lär dig mer](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) om de exakta kraven för att replikera VMware-servrar och virtuella datorer till Azure eller till en sekundär plats.


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kan jag hantera haveriberedskap för mina avdelningskontor med Site Recovery?
Ja. När du använder Site Recovery för att samordna replikering och redundans på avdelningskontor, får du en enhetlig orchestration och visning av alla arbetsbelastningar på en central plats. Du kan enkelt köra redundansväxlingar och administrera haveriberedskap för alla avdelningskontor från huvudkontoret, utan att besöka avdelningskontoren.

## <a name="pricing"></a>Prissättning
Priser frågor finns i frågor och svar på [priser för Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Säkerhet
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?
Nej, Site Recovery fånga upp inte replikerade data och har inte någon information om vad som körs på virtuella datorer eller fysiska servrar.
Replikeringsdata utbyts mellan lokala Hyper-V-värdar, VMware-hypervisorer eller fysiska servrar och Azure-lagring eller din sekundära plats. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är 27001: ISO 2013, 27018, HIPAA, DPA certifierad och håller på att SOC2 och FedRAMP JAB-utvärderingar.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Av kompatibilitetsskäl, måste även våra lokala metadata vara inom samma geografiska region. Site Recovery kan hjälpa oss?
Ja. När du skapar ett Site Recovery-valv i en region, kontrollera att alla metadata som vi behöver att aktivera och samordna replikering och redundans finns kvar i regionen är geografisk gräns.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
För virtuella datorer och fysiska servrar stöds replikering mellan lokala platser kryptering under överföring. För virtuella datorer och fysiska servrar som replikerar till Azure, både kryptering under överföring och [kryptering i vila (i Azure)](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption) stöds.

## <a name="replication"></a>Replikering

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kan jag replikera via ett plats-till-plats-VPN till Azure?
Azure Site Recovery replikerar data till Azure storage-konto via en offentlig slutpunkt. Replikering är inte via ett plats-till-plats-VPN. Du kan skapa en plats-till-plats-VPN med Azure-nätverk. Detta stör inte Site Recovery replikering.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kan jag använda ExpressRoute för att replikera virtuella datorer till Azure?
Ja, ExpressRoute kan användas för att replikera virtuella datorer till Azure. Azure Site Recovery replikerar data till ett Azure Storage-konto via en offentlig slutpunkt. Du måste ställa in [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) att använda ExpressRoute för Site Recovery replikering. När de virtuella datorerna har har redundansväxlats till Azure-nätverk kan du komma åt dem med hjälp av den [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) installation med virtuella Azure-nätverket.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Finns det några krav för att replikera virtuella datorer till Azure?
Virtuella datorer du vill replikera till Azure måste uppfylla [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

Ditt Azure-konto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) att aktivera replikering för en ny virtuell dator till Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan jag replikera virtuella Hyper-V-datorer i generation 2 till Azure?
Ja. Site Recovery konverterar från generation 2 till generation 1 under växling vid fel. Vid återställningen konverteras datorn tillbaka till generation 2. [Läs mer](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Hur betalar jag för virtuella datorer i Azure om jag replikerar till Azure?
Vid vanlig replikering replikeras data till geo-redundant Azure-lagring och du behöver inte betalar avgifter Azure IaaS-virtuella datorn att tillhandahålla en stor fördel. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt IaaS-baserade virtuella Azure-datorer. Därefter debiteras du för de beräkningsresurser som du använder i Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan jag automatisera Site Recovery-scenarier med en SDK?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Scenarier som stöds för närvarande för att distribuera Site Recovery med hjälp av PowerShell:

* [Replikera virtuella Hyper-V-datorer i VMMs moln till Azure PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Replikera virtuella Hyper-V-datorer utan VMM till Azure PowerShell Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Vilken typ av lagringskonto behöver jag om jag replikerar till Azure?
Du behöver ett LRS eller GRS-lagringskonto. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Kontot måste finnas i samma region som Recovery Services-valvet. Premium-lagring stöds för VMware VM, Hyper-V virtuell och fysisk server replication när du distribuerar Site Recovery på Azure-portalen.

### <a name="how-often-can-i-replicate-data"></a>Hur ofta kan jag replikera data?
* **Hyper-V:** Hyper-V virtuella datorer kan replikeras var 30 sekunder (förutom för premium-lagring), 5 minuter eller 15 minuter. Om du har konfigurerat SAN-replikering är replikeringen synkron.
* **VMware och fysiska servrar:** Replikeringsfrekvensen är irrelevant här. Replikeringen är kontinuerlig.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kan jag utöka replikeringen från den befintliga återställningsplatsen till en annan tertiär plats?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kan jag göra en offlinereplikering första gången jag replikerar till Azure?
Det stöds inte. Begär den här funktionen i den [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kan jag undanta specifika diskar från replikeringen?
Detta stöds när du är [replikera virtuella VMware-datorer och datorer med Hyper-V](site-recovery-exclude-disk.md) till Azure med Azure-portalen.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar stöds vid replikering av virtuella Hyper-V-datorer. De stöds också vid replikering av virtuella VMware-datorer och fysiska datorer till Azure. Operativsystemdisken måste vara en standarddisk.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Kan jag lägga till en ny dator till en befintlig replikeringsgrupp?
Att lägga till nya datorer i befintliga replikgrupper stöds. Om du vill göra det, Välj replikeringsgrupp (från bladet ”replikerade objekt”) och högerklicka på/Välj snabbmenyn på replikeringsgruppen och välj lämpligt alternativ.

![Lägg till replikeringsgruppen](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Kan jag begränsa bandbredden som tilldelas för Hyper-V-replikeringstrafik?
Ja. Du kan läsa mer om hur du begränsar bandbredd i distributionsartiklarna:

* [Kapacitetsplanering för att replikera virtuella VMware-datorer och fysiska servrar](site-recovery-plan-capacity-vmware.md)
* [Kapacitetsplanering för att replikera virtuella Hyper-V-datorer till Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Redundans
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Om jag redundansväxla till Azure, hur kommer jag åt de virtuella Azure-datorerna efter redundans?
Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller över Azure ExpressRoute. Du måste du förbereda ett antal åtgärder för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Om jag växla över till Azure hur ser Azure till att Mina data är flexibla?
Azure är utformat med flexibilitet i fokus. Site Recovery är redan utformad för redundanskluster till ett sekundärt Azure datacenter i enlighet med Azure-serviceavtalet om behovet uppstår. Om detta händer ser vi till att dina metadata och valv finns kvar i samma geografiska region som du valde för ditt valv.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Om jag replikerar mellan två Datacenter vad händer om min primära datacenter uppstår ett oväntat avbrott?
Du kan utlösa en oplanerad redundansväxling från den sekundära platsen. Site Recovery behöver ingen anslutning från den primära platsen för att utföra redundansväxlingen.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
Den sker inte automatiskt. Du startar redundansväxlingar med ett enda klick på portalen eller använda [Site Recovery PowerShell](/powershell/module/azurerm.siterecovery) att utlösa en växling vid fel. Återställas är en enkel åtgärd i Site Recovery-portalen.

För att automatisera du kan använda lokala Orchestrator eller Operations Manager för att identifiera fel på virtuella datorer och sedan utlösa en redundansväxling med hjälp av SDK.

* [Läs mer](site-recovery-create-recovery-plans.md) om återställningsplaner.
* [Läs mer](site-recovery-failover.md) om redundans.
* [Läs mer](site-recovery-failback-azure-to-vmware.md) om misslyckas säkerhetskopiera virtuella VMware-datorer och fysiska servrar

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Om min lokala värden inte svarar eller krasch, kan jag redundansväxling tillbaka till en annan värd?
Ja, du kan använda den alternativa platsåterställningen för återställning till en annan värd från Azure. Läs mer om alternativen i den nedan länkar för VMware och Hyper-v virtuella datorer.

* [För virtuella VMware-datorer](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [För Hyper-v-datorer](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>Leverantörer
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jag är en tjänstprovider. Fungerar Site Recovery för dedikerade och delade infrastrukturmodeller?
Ja, Site Recovery stöder både dedikerade och delade infrastrukturmodeller.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>För en tjänstprovider är identiteten för min klient som delas med Site Recovery-tjänsten?
Nej. Klient-ID är anonym. Klienterna behöver inte åtkomst till Site Recovery-portalen. Endast tjänstproviderns administratör interagerar med portalen.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Kommer klient programdata någonsin hos Azure?
När du replikerar mellan platser som ägs av tjänstproviders hamnar programdata aldrig hos Azure. Data krypteras under överföring och replikeras direkt mellan tjänstproviderns platser.

Om du replikera till Azure skickas programdata till Azure-lagring men inte till Site Recovery-tjänsten. Data krypteras under överföringen, och förblir krypterade i Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Får mina klienter en faktura för några Azure-tjänster?
Nej. Azures faktureringsrelation sker direkt med tjänstprovidern. Tjänstprovidern ansvarar för att skapa fakturor för sina klienter.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Måste jag alltid köra virtuella datorer i Azure om jag replikerar till Azure?
Nej, replikeras Data till ett Azure storage-konto i din prenumeration. När du utför ett redundanstest (DR-test) eller en riktig redundansväxling skapar Site Recovery automatiskt virtuella datorer i din prenumeration.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garanterar ni isolering på klientnivå när jag replikerar till Azure?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Vilka plattformar stöds för närvarande?
Vi stöder Azure Pack, Cloud Platform System och System Center-baserade distributioner (2012 och senare). [Lär dig mer](https://technet.microsoft.com/library/dn850370.aspx) om Azure-paket och Site Recovery-integration.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Finns det stöd för distributioner med ett enda Azure Pack och en enda VMM-server?
Ja, kan du replikera virtuella Hyper-V-datorer till Azure eller mellan en tjänstproviders platser.  Observera att om du replikerar mellan en tjänstproviders platser Azure runbook-integrering är inte tillgänglig.

## <a name="next-steps"></a>Nästa steg
* Läs [Site Recovery-översikten](site-recovery-overview.md)
* Lär dig mer om [Site Recovery-arkitekturen](site-recovery-components.md)  
