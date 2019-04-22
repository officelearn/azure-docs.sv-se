---
title: Vanliga frågor – Hyper-V till Azure-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln sammanfattas vanliga frågor om hur du konfigurerar haveriberedskap för lokala Hyper-V-datorer till Azure med hjälp av Azure Site Recovery-plats.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.date: 04/08/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: ce4a6ab24aaa5ed693f8d64782fb025a2ca9ce30
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357988"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Vanliga frågor – Hyper-V till Azure-haveriberedskap

Den här artikeln innehåller svar på vanliga frågor som visas när du replikerar lokala virtuella Hyper-V-datorer till Azure. 

## <a name="general"></a>Allmänt

### <a name="how-is-site-recovery-priced"></a>Hur prissätts Site Recovery?
Granska [priserna för Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) information.

### <a name="how-do-i-pay-for-azure-vms"></a>Hur betalar jag för virtuella Azure-datorer?
Data replikeras till Azure-lagring under replikering, och du behöver inte betala några VM-ändringar. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt Azure IaaS-datorer. Efter det du faktureras för de beräkningsresurser som du använder i Azure.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Vad behöver jag i Hyper-V att samordna replikeringen med Site Recovery?

Vad du behöver för Hyper-V-värdservern beror på distributionsscenariot. Du hittar kraven för Hyper-V i:

* [Replikera Hyper-V-datorer (utan VMM) till Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikera Hyper-V-datorer (med VMM) till Azure](site-recovery-vmm-to-azure.md)
* [Replikera Hyper-V-datorer till ett sekundärt datacenter](site-recovery-vmm-to-vmm.md)
* Om du replikerar till ett sekundärt datacenter Läs mer om [gästoperativsystem som stöds för Hyper-V-datorer](https://technet.microsoft.com/library/mt126277.aspx).
* Om du replikerar till Azure Site Recovery stöder alla gästoperativsystem som är [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan jag skydda virtuella datorer när Hyper-V körs på ett klientoperativsystem?
Nej, virtuella datorer måste finnas på en Hyper-V-värdserver som körs på en Windows-serverdator som stöds. Om du vill skydda en klientdator kan du replikera den som en fysisk dator [Azure](site-recovery-vmware-to-azure.md) eller en [sekundärt datacenter](site-recovery-vmware-to-vmware.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Behöver Hyper-V-värdar i VMM-moln?
Om du vill replikera till ett sekundärt datacenter Hyper-V-datorer måste vara på Hyper-V-värdar servrar som finns i ett VMM-moln. Om du vill replikera till Azure kan du replikera virtuella datorer med eller utan VMM-moln. [Läs mer](tutorial-hyper-v-to-azure.md) om Hyper-V-replikering till Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan jag replikera virtuella Hyper-V-datorer i generation 2 till Azure?
Ja. Site Recovery konverterar från generation 2 till generation 1 under redundansväxlingen. Vid återställningen konverteras datorn tillbaka till generation 2. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kan jag distribuera Site Recovery med VMM om jag bara har en VMM-server?

Ja. Du kan antingen replikera virtuella datorer på Hyper-V-servrar i VMM-moln till Azure eller replikera mellan VMM-moln på samma server. För en lokal plats till en lokal replikering rekommenderar vi att du har en VMM-server i båda primära och sekundära platser. 

### <a name="what-do-i-need-in-azure"></a>Vad behöver jag i Azure?
Du behöver en Azure-prenumeration, Recovery Services-valvet, ett lagringskonto och ett virtuellt nätverk. Valvet, lagringskontot och nätverket måste vara i samma region.

### <a name="what-azure-storage-account-do-i-need"></a>Vilka Azure-lagringskonto behöver jag?
Du behöver ett LRS eller GRS-lagringskonto. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Premium storage stöds.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver Mina Azure-konto behörighet att skapa virtuella datorer?
Om du är administratör för prenumerationen har Replikeringsbehörighet som du behöver. Om du inte behöver du behörighet att skapa en Azure-dator i resursgruppen och virtuellt nätverk som du anger när du konfigurerar Site Recovery och behörighet att skriva till det valda lagringskontot. [Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till Site Recovery?
Nej, Site Recovery komma åt inte replikerade data och har inte någon information om vad som körs på dina virtuella datorer. Replikeringsdata utväxlas mellan Hyper-V-värdar och Azure storage. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001: 2013, 27018, HIPAA, DPA certifierade och håller på att SOC2 och FedRAMP JAB-utvärderingar.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Kan vi lagrar lokala metadata inom ett geografiskt område?
Ja. När du skapar ett valv i en region, kontrollera att alla metadata som används av Site Recovery finns kvar inom regionens geografisk gräns.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
Ja, både kryptering under överföring och [kryptering i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.


## <a name="deployment"></a>Distribution

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Vad kan jag göra med Hyper-V till Azure replikering?

- **Haveriberedskap**: Du kan ställa in fullständig haveriberedskap. I det här scenariot kan replikera du lokala virtuella Hyper-V-datorer till Azure storage:
    - Du kan replikera datorer till Azure. Om din infrastruktur på plats är tillgänglig, växlar du över till Azure.
    - När du redundansväxlar, skapas virtuella Azure-datorer med hjälp av replikerade data. Du kan komma åt appar och arbetsbelastningar på virtuella Azure-datorer.
    - När ditt datacenter på plats är tillgänglig igen, kan du växla tillbaka från Azure till din lokala plats.
- **Migrering**: Du kan använda Site Recovery för att migrera lokala virtuella Hyper-V-datorer till Azure storage. Sedan kan växla du över från den lokala till Azure. Efter redundansväxlingen är dina appar och arbetsbelastningar tillgängliga och körs på virtuella Azure-datorer.


### <a name="what-do-i-need-on-premises"></a>Vad gör jag behöver på plats?

Du behöver en eller flera virtuella datorer som körs på en eller flera fristående eller klustrade Hyper-V-värdar. Du kan också replikera virtuella datorer som körs på värdar som hanteras av System Center Virtual Machine Manager (VMM).
- Om du inte använder VMM under distributionen av Site Recovery kan du samla in Hyper-V-värdar och kluster i Hyper-V-platser. Du installerar Site Recovery-agenter (Azure Site Recovery-providern och Recovery Services-agent) på varje Hyper-V-värd.
- Om Hyper-V-värdar finns i VMM-moln kan dirigera du replikering i VMM. Du installerar Site Recovery-providern på VMM-servern och Recovery Services-agenten på varje Hyper-V-värd. Du kan mappa mellan VMM logiska och VM-nätverk och virtuella Azure-nätverk.
- [Läs mer](hyper-v-azure-architecture.md) om Hyper-V till Azure-arkitektur.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Kan jag replikera virtuella datorer på Hyper-V-kluster?

Ja, Site Recovery har stöd för klustrade Hyper-V-värdar. Tänk på följande:

- Alla noder i klustret ska registreras på samma valv.
- Om du inte använder VMM, ska alla Hyper-V-värdar i klustret läggas till på samma Hyper-V-plats.
- Du installerar Azure Site Recovery-providern och Recovery Services-agenten på varje Hyper-V-värd i klustret och Lägg till varje värd till en Hyper-V-plats.
- Inga specifika steg måste utföras på klustret.
- Om du kör verktyget Distributionshanteraren för Hyper-V, verktyget att samlar in data för användarprofiler från den nod som körs och där Virtuellt datorn körs. Verktyget kan inte samla in data från en nod som är avstängd, men den spårar noden. När noden är igång, startar verktyget samlar in data för VM-profil från den (om den virtuella datorn är en del av profilen VM-listan och körs på noden).
- Om en virtuell dator på Hyper-V-värd i ett Site Recovery-valv migrerar till en annan Hyper-V-värd i samma kluster eller till en fristående värd, påverkas replikering för den virtuella datorn inte. Hyper-V-värden måste uppfylla [krav](hyper-v-azure-support-matrix.md#on-premises-servers), och konfigureras i ett Site Recovery-valv. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan jag skydda virtuella datorer när Hyper-V körs på ett klientoperativsystem?
Nej, virtuella datorer måste finnas på en Hyper-V-värdserver som körs på en Windows-serverdator som stöds. Om du vill skydda en klientdator kan du [replikera den som en fysisk dator](physical-azure-disaster-recovery.md) till Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan jag replikera virtuella Hyper-V-datorer i generation 2 till Azure?
Ja. Site Recovery konverterar från generation 2 till generation 1 under redundansväxlingen. Vid återställningen konverteras datorn tillbaka till generation 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan jag automatisera Site Recovery-scenarier med ett SDK?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Scenarier som stöds för närvarande för att replikera Hyper-V till Azure med hjälp av PowerShell:

- [Replikera Hyper-V utan VMM med hjälp av PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replikera Hyper-V med VMM med Powershell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikering

### <a name="where-do-on-premises-vms-replicate-to"></a>Där replikerar lokala virtuella datorer till?
Data replikeras till Azure storage. När du kör en redundansväxling skapar Site Recovery automatiskt virtuella datorer i Azure från storage-kontot.

### <a name="what-apps-can-i-replicate"></a>Vilka appar kan jag replikera?
Du kan replikera en app eller arbetsbelastning som körs på en Hyper-V virtuell dator som uppfyller [replikeringskraven](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery har stöd för Programmedveten replikering så att appar kan växlas över och återställde till en intelligent tillstånd. Site Recovery kan integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och har ett nära samarbete med ledande leverantörer, som Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="whats-the-replication-process"></a>Vad är replikeringen?

1. När den inledande replikeringen utlöses tas en Hyper-V VM-ögonblicksbild.
2. Virtuella hårddiskar på den virtuella datorn är replikeras en i taget, tills alla har kopierats till Azure. Detta kan ta en stund, beroende på VM-storlek och nätverkets bandbredd. Lär dig hur du ökar nätverksbandbredden.
3. Om diskändringar inträffar under den inledande replikeringen pågår, spårar ändringarna i den spårningsverktyget för Hyper-V-replikering som Hyper-V-replikeringsloggar (.hrl). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad hrl-fil som skickas till sekundär lagring. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den initiala replikeringen är klar tas VM-ögonblicksbilden bort.
5. Diskändringar i loggen synkroniseras och sammanfogas till den överordnade disken.
6. Slutför skydd på VM-jobbet körs när den inledande replikeringen är klar. Den konfigurerar nätverksinställningar och andra inställningar som krävs efter replikeringen så att den virtuella datorn är skyddad.
7. I det här skedet kan du kontrollera inställningarna för virtuella datorer och kontrollera att den är redo för redundans. Du kan köra ett programåterställningstest (testa redundans) för den virtuella datorn att kontrollera att den inte över som förväntat.
8. Efter den första replikeringen börjar deltareplikering enligt replikeringsprincipen.
9. Ändringar är loggade hrl-filerna. Varje disk som är konfigurerad för replikering har en associerad .hrl-fil.
10. Loggen skickas till kundens lagringskonto. När en logg är på väg till Azure, spåras ändringar i den primära disken i en loggfil i samma mapp.
11. Du kan övervaka den virtuella datorn i Azure-portalen under replikeringen både initial och deltareplikering.

[Läs mer](hyper-v-azure-architecture.md#replication-process) om replikeringen.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan jag replikera till Azure med ett plats-till-plats-VPN?

Site Recovery replikerar data från en lokal plats till Azure storage via en offentlig slutpunkt eller med offentlig peering i ExpressRoute. Replikering via ett plats-till-plats VPN-nätverk stöds inte.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan jag replikera till Azure med ExpressRoute?

Ja, ExpressRoute kan användas för att replikera datorer till Azure. Site Recovery replikerar data till ett Azure Storage-konto via en offentlig slutpunkt och du måste konfigurera [offentlig peering](../expressroute/expressroute-circuit-peerings.md#publicpeering) för Site Recovery-replikering. När virtuella datorer som redundansväxlar till Azure-nätverk, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan inte replikera via VPN?

När du replikerar till Azure når replikeringstrafiken de offentliga slutpunkterna för ett Azure Storage-konto. Därför kan du bara replikera via det offentliga internet med ExpressRoute (offentlig peering) och VPN fungerar inte. 

### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är kraven för replikerade virtuella datorer?

För replikering, måste en Hyper-V virtuell dator köra ett operativsystem som stöds. Dessutom kan måste den virtuella datorn uppfylla kraven för virtuella Azure-datorer. [Läs mer](hyper-v-azure-support-matrix.md#replicated-vms) i supportmatrisen.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Hyper-V-datorer kan replikeras var 30 sekunder (förutom för premium-lagring), 5 minuter eller 15 minuter.

### <a name="can-i-extend-replication"></a>Kan jag utöka replikeringen?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en offlinereplikering första?
Det stöds inte. Begär den här funktionen i den [Feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?
Ja, kan du undanta diskar från replikering. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar kan replikeras. Operativsystemdisken måste vara en standarddisk.



## <a name="security"></a>Säkerhet

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Vilken åtkomst behöver du Hyper-V-värdar i Site Recovery

Site Recovery behöver åtkomst till Hyper-V-värdar att replikera de virtuella datorerna som du väljer. Site Recovery installerar följande på Hyper-V-värdar:

- Om du inte kör är VMM, Azure Site Recovery-providern och Recovery Services-agenten installerade på varje värd.
- Om du kör VMM, är Recovery Services-agenten installerad på varje värd. Providern körs på VMM-servern.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Vad kan Site Recovery installeras i Hyper-V-datorer?

Site Recovery installerar uttryckligen inte något på Hyper-V-datorer som aktiverats för replikering.




## <a name="failover-and-failback"></a>Redundans och återställning efter fel


### <a name="how-do-i-fail-over-to-azure"></a>Hur växlar jag över till Azure?

Du kan köra en planerad eller oplanerad redundansväxling från den lokala Hyper-V-datorer till Azure.
    - Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
    - Du kan köra en oplanerad redundansväxling om den primära platsen är inte tillgänglig.
    - Du kan redundansväxla en enskild dator eller skapa återställningsplaner för att samordna redundans för flera datorer.
    - Du kör en redundansväxling. När den första fasen av redundans är klar bör du kunna se skapade virtuella datorer i Azure. Du kan tilldela en offentlig IP-adress till datorn om det behövs. Du etablerar därefter redundansen för att få åtkomst till arbetsbelastningen från den Virtuella Azure-replikdatorn.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur kommer jag åt virtuella Azure-datorer efter redundans?
Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller via Azure ExpressRoute efter redundansväxlingen. Du måste förbereda ett antal saker för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Växlas över data som är elastiska?
Azure är utformat med flexibilitet i fokus. Site Recovery är utformat för redundansväxling till en sekundär Azure-datacenter, i enlighet med serviceavtalet för Azure. Vid redundans vi Kontrollera att dina metadata och valv finns kvar i samma geografiska region som du har valt för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
[Redundans](site-recovery-failover.md) inte automatiskt. Du startar redundansväxlingar med ett enda klick i portalen eller använda [PowerShell](/powershell/module/az.recoveryservices) att utlösa redundans.

### <a name="how-do-i-fail-back"></a>Hur växlar jag tillbaka?

När din lokala infrastruktur är igång igen kan du inte återställa. Återställning efter fel sker i tre steg:

1. Du startar en planerad redundansväxling från Azure till den lokala platsen och använda ett par olika alternativ:

    - Minimera stilleståndstiden: Om du använder det här alternativet synkroniserar data före redundans i Site Recovery. Den söker efter ändrade datablock och hämtar dem till den lokala platsen, när behåller virtuell Azure-dator som kör, minimerar avbrottstid. När du anger manuellt när redundansen ska utföra, Azure-Virtuella datorn stängs av och alla slutliga deltaändringar kopieras redundansen börjar.
    - Fullständig nedladdning: Med det här alternativet synkroniseras data under en redundansväxling. Det här alternativet laddar ned hela disken. Det går snabbare eftersom ingen kontrollsummor beräknas, men det finns fler driftstopp. Använd det här alternativet om du har kört repliken virtuella Azure-datorer under en viss tid, eller om den lokala virtuella datorn har tagits bort.

2. Du kan välja för att växla tillbaka till samma virtuella dator eller till en annan virtuell dator. Du kan ange att Site Recovery ska skapa den virtuella datorn om den inte redan finns.
3. När den första synkroniseringen är klar kan välja du för att slutföra redundansen. När den är klar kan logga du in till den lokala virtuella datorn att kontrollera att allt fungerar som förväntat. Du kan se att den virtuella Azure-datorer har stoppats i Azure-portalen.
4. Du etablerar redundansen för att avsluta och få åtkomst till arbetsbelastningen från den lokala virtuella datorn igen.
5. När arbetsbelastningar har återställts, aktiverar du omvänd replikering så att replikera lokala virtuella datorer till Azure igen.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag återställa till en annan plats?
Ja, om du växlas över till Azure, du kan växla tillbaka till en annan plats om den ursprungliga som inte är tillgänglig. [Läs mer](hyper-v-azure-failback.md#failback-to-an-alternate-location-in-hyper-v-environment).
