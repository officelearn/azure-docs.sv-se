---
title: Vanliga frågor för Hyper-V-haveriberedskap med Azure Site Recovery
description: Den här artikeln sammanfattar vanliga frågor om hur du konfigurerar haveriberedskap för lokala virtuella hyper-virtuella datorer till Azure med hjälp av azure-webbplatsen Site Recovery.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 7c5f55fbea67567ddf7a2afa6a61f6c76568d829
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498205"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Vanliga frågor – Haveriberedskap för Hyper-V till Azure

Den här artikeln innehåller svar på vanliga frågor som vi ser när du replikerar lokala virtuella virtuella hyper-virtuella datorer till Azure. 

## <a name="general"></a>Allmänt

### <a name="how-is-site-recovery-priced"></a>Hur är Site Recovery prissatta?
Granska [prisinformation för Azure Site Recovery.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="how-do-i-pay-for-azure-vms"></a>Hur betalar jag för virtuella Azure-datorer?
Under replikeringen replikeras data till Azure-lagring och du betalar inga vm-ändringar. När du kör en redundans till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer. Därefter debiteras du för beräkningsresurser som du förbrukar i Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Finns det någon skillnad i kostnad vid replikera till Allmänt ändamål v2 lagringskonto?

Du kommer vanligtvis att se en ökning av transaktionskostnaden som uppkommit på GPv2-lagringskonton eftersom Azure Site Recovery är transaktioner tunga. [Läs mer](../storage/common/storage-account-upgrade.md#pricing-and-billing) för att uppskatta förändringen.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Vad behöver jag i Hyper-V för att dirigera replikering med Site Recovery?

Vad du behöver för Hyper-V-värdservern beror på distributionsscenariot. Du hittar kraven för Hyper-V i:

* [Replikera virtuella Hyper-V-datorer (utan VMM) till Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replikera virtuella Hyper-V-datorer (med VMM) till Azure](site-recovery-vmm-to-azure.md)
* [Replikera virtuella Hyper-V-datorer till ett sekundärt datacenter](site-recovery-vmm-to-vmm.md)
* Om du replikerar till ett sekundärt datacenter läser du om [gästoperativsystem som stöds för virtuella hyper-virtuella datorer](https://technet.microsoft.com/library/mt126277.aspx).
* Om du replikerar till Azure stöder Site Recovery alla gästoperativsystem som [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan jag skydda virtuella datorer när Hyper-V körs på ett klientoperativsystem?
Nej, virtuella datorer måste finnas på en Hyper-V-värdserver som körs på en Windows-serverdator som stöds. Om du behöver skydda en klientdator kan du replikera den som en fysisk dator till [Azure](site-recovery-vmware-to-azure.md) eller ett [sekundärt datacenter](site-recovery-vmware-to-vmware.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Behöver Hyper-V-värdar vara i VMM-moln?
Om du vill replikera till ett sekundärt datacenter måste virtuella hyper-virtuella datorer finnas på Hyper-V-värdservrar som finns i ett VMM-moln. Om du vill replikera till Azure kan du replikera virtuella datorer med eller utan VMM-moln. [Läs mer](tutorial-hyper-v-to-azure.md) om Hyper-V-replikering till Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan jag replikera virtuella Hyper-V-datorer i generation 2 till Azure?
Ja. Site Recovery konverterar från generation 2 till generation 1 under redundans. Vid återställning av fel konverteras maskinen tillbaka till generation 2. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kan jag distribuera Site Recovery med VMM om jag bara har en VMM-server?

Ja. Du kan antingen replikera virtuella datorer i Hyper-V-servrar i VMM-molnet till Azure, eller så kan du replikera mellan VMM-moln på samma server. För lokal till lokal replikering rekommenderar vi att du har en VMM-server på både primära och sekundära platser. 

### <a name="what-do-i-need-in-azure"></a>Vad behöver jag i Azure?
Du behöver en Azure-prenumeration, ett Recovery Services-valv, ett lagringskonto och ett virtuellt nätverk. Valvet, lagringskontot och nätverket måste finnas i samma region.

### <a name="what-azure-storage-account-do-i-need"></a>Vilket Azure-lagringskonto behöver jag?
Du behöver ett LRS- eller GRS-lagringskonto. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Premiumlagring stöds.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver mitt Azure-konto behörigheter för att skapa virtuella datorer?
Om du är prenumerationsadministratör har du de replikeringsbehörigheter du behöver. Om du inte är det behöver du behörigheter för att skapa en Virtuell Azure-dator i resursgruppen och det virtuella nätverket som du anger när du konfigurerar Webbplatsåterställning och behörigheter att skriva till det valda lagringskontot. [Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till platsåterställning?
Nej, site recovery avlyssnar inte replikerade data och har ingen information om vad som körs på dina virtuella datorer. Replikeringsdata utbyts mellan Hyper-V-värdar och Azure-lagring. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001:2013, 27018, HIPAA, DPA-certifierad och håller på att hantera SOC2- och FedRAMP JAB-bedömningar.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Kan vi behålla lokala metadata inom en geografisk region?
Ja. När du skapar ett valv i en region ser vi till att alla metadata som används av Site Recovery ligger inom den regionens geografiska gräns.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
Ja, både kryptering-i-transit och [kryptering i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.


## <a name="deployment"></a>Distribution

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Vad kan jag göra med Hyper-V till Azure-replikering?

- **Haveriberedskap:** Du kan ställa in fullständig haveriberedskap. I det här fallet replikerar du lokala virtuella hyper-virtuella datorer till Azure-lagring:
    - Du kan replikera virtuella datorer till Azure. Om din lokala infrastruktur inte är tillgänglig växlar du över till Azure.
    - När du växlar över skapas virtuella Azure-datorer med hjälp av replikerade data. Du kan komma åt appar och arbetsbelastningar på virtuella Azure-datorer.
    - När ditt lokala datacenter är tillgängligt igen kan du växla tillbaka från Azure till din lokala webbplats.
- **Migrering:** Du kan använda Site Recovery för att migrera lokala virtuella hyper-virtuella datorer till Azure-lagring. Sedan kan du växla över från lokalt till Azure. Efter redundans är dina appar och arbetsbelastningar tillgängliga och körs på virtuella Azure-datorer.


### <a name="what-do-i-need-on-premises"></a>Vad behöver jag lokalt?

Du behöver en eller flera virtuella datorer som körs på en eller flera fristående eller klustrade Hyper-V-värdar. Du kan också replikera virtuella datorer som körs på värdar som hanteras av System Center Virtual Machine Manager (VMM).
- Om du inte kör VMM samlar du hyper-V-värdar och kluster i Hyper-V-platser under distributionen av webbplatsåterställning. Du installerar site recovery-agenter (Azure Site Recovery Provider och Recovery Services agent) på varje Hyper-V-värd.
- Om Hyper-V-värdar finns i ett VMM-moln dirigerar du replikering i VMM. Du installerar site recovery provider på VMM-servern och Recovery Services-agenten på varje Hyper-V-värd. Du mappar mellan VMM logiska/VM-nätverk och Azure virtuella nätverk.
- [Läs mer](hyper-v-azure-architecture.md) om Hyper-V till Azure-arkitektur.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Kan jag replikera virtuella datorer som finns i ett Hyper-V-kluster?

Ja, Site Recovery stöder grupperade Hyper-V-värdar. Tänk på följande:

- Alla noder i klustret ska registreras i samma valv.
- Om du inte använder VMM ska alla Hyper-V-värdar i klustret läggas till på samma Hyper-V-plats.
- Du installerar Azure Site Recovery Provider and Recovery Services-agenten på varje Hyper-V-värd i klustret och lägger till varje värd på en Hyper-V-plats.
- Inga specifika steg behöver göras i klustret.
- Om du kör distributionsplaneraren för Hyper-V samlar verktyget in profildata från noden som körs och där den virtuella datorn körs. Verktyget kan inte samla in data från en nod som är avstängd, men det spårar noden. När noden är igång börjar verktyget samla in VM-profildata från den (om den virtuella datorn är en del av profilen VM-listan och körs på noden).
- Om en virtuell dator på en Hyper-V-värd i ett site recovery-valv migrerar till en annan Hyper-V-värd i samma kluster, eller till en fristående värd, påverkas inte replikering för den virtuella datorn. Hyper-V-värden måste uppfylla [förutsättningar](hyper-v-azure-support-matrix.md#on-premises-servers)och konfigureras i ett valv för återställning av platsen. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan jag skydda virtuella datorer när Hyper-V körs på ett klientoperativsystem?
Nej, virtuella datorer måste finnas på en Hyper-V-värdserver som körs på en Windows-serverdator som stöds. Om du behöver skydda en klientdator kan du [replikera den som en fysisk dator](physical-azure-disaster-recovery.md) till Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan jag replikera virtuella Hyper-V-datorer i generation 2 till Azure?
Ja. Site Recovery konverterar från generation 2 till generation 1 under redundans. Vid återställning av fel konverteras maskinen tillbaka till generation 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan jag automatisera scenarier för webbplatsåterställning med en SDK?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Scenarier som stöds för att replikera Hyper-V till Azure med PowerShell:

- [Replikera Hyper-V utan VMM med PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replikera Hyper-V med VMM med Powershell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikering

### <a name="where-do-on-premises-vms-replicate-to"></a>Var replikeras lokala virtuella datorer?
Data replikeras till Azure-lagring. När du kör en redundans skapar Site Recovery automatiskt virtuella Azure-datorer från lagringskontot.

### <a name="what-apps-can-i-replicate"></a>Vilka appar kan jag replikera?
Du kan replikera alla appar eller arbetsbelastningar som kör en virtuell hyper-V-dator som uppfyller [replikeringskraven](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery ger stöd för programmedveten replikering, så att appar kan skickas över och återställas till ett intelligent tillstånd. Site Recovery integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och har ett nära samarbete med ledande leverantörer, inklusive Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="whats-the-replication-process"></a>Vad är replikeringsprocessen?

1. När den första replikeringen utlöses tas en ögonblicksbild av virtuella datorer med hyper-V.
2. Virtuella hårddiskar på den virtuella datorn replikeras en efter en, tills de alla kopieras till Azure. Detta kan ta ett tag, beroende på den virtuella datorns storlek och nätverksbandbredd. Läs om hur du ökar nätverksbandbredden.
3. Om diskändringar inträffar medan den första replikeringen pågår spårar Hyper-V Replica Replication Tracker ändringarna som Hyper-V-replikeringsloggar (.hrl). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad HRL-fil som skickas till sekundär lagring. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den initiala replikeringen är klar tas VM-ögonblicksbilden bort.
5. Alla diskändringar i loggen synkroniseras och slås samman med den överordnade disken.
6. När den första replikeringen är klar körs slutför skyddet för jobbet för den virtuella datorn. Den konfigurerar nätverksinställningar och andra inställningar efter replikering, så att den virtuella datorn är skyddad.
7. I det här skedet kan du kontrollera vm-inställningarna för att se till att den är redo för redundans. Du kan köra en haveriberedskapsövning (test redundans) för den virtuella datorn för att kontrollera att den växlar över som förväntat.
8. Efter den första replikeringen börjar deltareplikeringen, i enlighet med replikeringsprincipen.
9. Ändringar loggas .hrl-filer. Varje disk som är konfigurerad för replikering har en associerad .hrl-fil.
10. Loggen skickas till kundens lagringskonto. När en logg är under överföring till Azure spåras ändringarna i den primära disken i en annan loggfil i samma mapp.
11. Under både inledande och delta-replikering kan du övervaka den virtuella datorn i Azure-portalen.

[Läs mer](hyper-v-azure-architecture.md#replication-process) om replikeringsprocessen.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan jag replikera till Azure med en VPN från plats till plats?

Site Recovery replikerar data från lokalt till Azure-lagring via en offentlig slutpunkt eller med Hjälp av ExpressRoute Microsoft-peering. Replikering via ett VPN-nätverk från plats till plats stöds inte.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan jag replikera till Azure med ExpressRoute?

Ja, ExpressRoute kan användas för att replikera virtuella datorer till Azure. Site Recovery replikerar data till ett Azure Storage-konto via en offentlig slutpunkt och du måste konfigurera [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) för site recovery-replikering. När virtuella datorer har växlat över till ett virtuellt Azure-nätverk kan du komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan jag inte replikera via VPN?

När du replikerar till Azure når replikeringstrafiken de offentliga slutpunkterna för ett Azure Storage-konto. Således kan du bara replikera över det offentliga internet med ExpressRoute (Microsoft peering), och VPN fungerar inte. 

### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är kraven för replikerad virtuell dator?

För replikering måste en virtuell hyper-V-virtuell dator köra ett operativsystem som stöds. Dessutom måste den virtuella datorn uppfylla kraven för virtuella Azure-datorer. [Läs mer](hyper-v-azure-support-matrix.md#replicated-vms) i supportmatrisen.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Virtuella datorer med hyper-V kan replikeras var 30:e sekund (förutom premiumlagring), 5 minuter eller 15 minuter.

### <a name="can-i-extend-replication"></a>Kan jag förlänga replikeringen?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en första offlinereplikering?
Det stöds inte. Begär den här funktionen i [feedbackforumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?
Ja, du kan utesluta diskar från replikering. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar kan replikeras. Operativsystemets disk måste vara en enkel disk.



## <a name="security"></a>Säkerhet

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Vilken åtkomst behöver Site Recovery till Hyper-V-värdar

Site Recovery behöver åtkomst till Hyper-V-värdar för att replikera de virtuella datorer du väljer. Site Recovery installerar följande på Hyper-V-värdar:

- Om du inte kör VMM installeras Agenten för Azure Site Recovery Provider och Recovery Services på varje värd.
- Om du kör VMM installeras Recovery Services-agenten på varje värd. Providern körs på VMM-servern.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Vad installerar Site Recovery på virtuella hyper-virtuella datorer?

Site Recovery inte uttryckligen installera något på Hyper-V virtuella datorer aktiveras för replikering.




## <a name="failover-and-failback"></a>Redundans och återställning efter fel


### <a name="how-do-i-fail-over-to-azure"></a>Hur växlar jag över till Azure?

Du kan köra en planerad eller oplanerad redundansväxling från lokala Hyper-V virtuella datorer till Azure.

- Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
- Du kan köra en oplanerad redundans om din primära webbplats inte är tillgänglig.
- Du kan redundansväxla en enskild dator eller skapa återställningsplaner för att samordna redundans för flera datorer.
- Redundans är i två delar:
    - När den första fasen av redundansen är klar bör du kunna se de skapade virtuella replikerna i Azure. Du kan tilldela en offentlig IP-adress till datorn om det behövs.
    - Du genomför sedan redundansen för att börja komma åt arbetsbelastningen från repliken Azure VM.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur kommer jag åt virtuella Azure-datorer efter redundans?
Efter redundans kan du komma åt virtuella Azure-datorer via en säker Internetanslutning, via en PLATS-till-plats-VPN eller via Azure ExpressRoute. Du måste förbereda ett antal saker för att ansluta. [Läs mer](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Är det fel på data som är flexibelt?
Azure är utformat med flexibilitet i fokus. Site Recovery är konstruerad för redundans till ett sekundärt Azure-datacenter, i enlighet med Azure SLA. När redundans inträffar ser vi till att dina metadata och valv ligger inom samma geografiska region som du har valt för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
[Redundans](site-recovery-failover.md) är inte automatiskt. Du initierar redundans med ett enda klick i portalen, eller så kan du använda [PowerShell](/powershell/module/az.recoveryservices) för att utlösa en redundans.

### <a name="how-do-i-fail-back"></a>Hur misslyckas jag tillbaka?

När den lokala infrastrukturen är igång igen kan du återställa den. Återställning sker i tre steg:

1. Du startar en planerad redundans från Azure till den lokala webbplatsen med hjälp av ett par olika alternativ:

    - Minimera stilleståndstiden: Om du använder det här alternativet synkroniserar Webbplatsåterställning data före redundans. Den söker efter ändrade datablock och hämtar dem till den lokala webbplatsen, medan Den virtuella Azure-datorn fortsätter att köras, vilket minimerar driftstopp. När du anger manuellt att redundansen ska slutföras stängs Azure-virtuella datorn av, eventuella slutliga deltaändringar kopieras och redundansen startar.
    - Fullständig hämtning: Med det här alternativet synkroniseras data under redundans. Det här alternativet hämtar hela disken. Det går snabbare eftersom inga kontrollsummar beräknas, men det finns mer driftstopp. Använd det här alternativet om du har kört repliken Azure virtuella datorer under en tid, eller om den lokala virtuella datorn har tagits bort.

2. Du kan välja att växla tillbaka till samma virtuella dator eller till en alternativ virtuell dator. Du kan ange att site recovery ska skapa den virtuella datorn om den inte redan finns.
3. När den första synkroniseringen är klar väljer du att slutföra redundansen. När den är klar kan du logga in på den lokala virtuella datorn för att kontrollera att allt fungerar som förväntat. I Azure-portalen kan du se att virtuella Azure-datorer har stoppats.
4. Du genomför redundansen för att slutföra och börja komma åt arbetsbelastningen från den lokala virtuella datorn igen.
5. När arbetsbelastningarna har misslyckats kan du aktivera omvänd replikering, så att de lokala virtuella datorerna replikeras till Azure igen.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag växla tillbaka till en annan plats?
Ja, om du misslyckades med att azure kan du växla tillbaka till en annan plats om den ursprungliga inte är tillgänglig. [Läs mer](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
