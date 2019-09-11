---
title: Vanliga frågor om haveri beredskap från Azure till Azure med Azure Site Recovery
description: I den här artikeln besvaras vanliga frågor om haveri beredskap för virtuella Azure-datorer till en annan Azure-region med Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 04/29/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: cd1c6cf0ff5a963720df7420a5d983d24e7b4d3e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861386"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Vanliga frågor: Azure till Azure-haveriberedskap

Den här artikeln innehåller svar på vanliga frågor om haveri beredskap för virtuella Azure-datorer till en annan Azure-region med hjälp av [Site Recovery](site-recovery-overview.md). 


## <a name="general"></a>Allmänt

### <a name="how-is-site-recovery-priced"></a>Hur kostar det att Site Recovery?
Granska [Azure Site Recovery pris](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) information.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hur fungerar den kostnadsfria nivån för Azure Site Recovery?
Varje instans som skyddas med Azure Site Recovery är kostnadsfri under de första 31 dagarna med skydd. Från och med den 32:a dagen debiteras skydd för instansen med de priser som anges ovan.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Förekommer det några andra avgifter för Azure under de första 31 dagarna?
Ja, även om Azure Site Recovery är kostnadsfritt under de första 31 dagarna för en skyddad instans kan du komma att debiteras för Azure Storage, lagringstransaktioner och dataöverföring. En återställd virtuell dator kan också debiteras för Azure-beräkningsavgifter. Få fullständig information om prissättning [här](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Var hittar jag metod tips för haveri beredskap för virtuella Azure-datorer? 
1. [Förstå Azure-till-Azure-arkitekturen](azure-to-azure-architecture.md)
2. [Granska de konfigurationer som stöds och som inte stöds](azure-to-azure-support-matrix.md)
3. [Konfigurera katastrof återställning för virtuella Azure-datorer](azure-to-azure-how-to-enable-replication.md)
4. [Kör ett redundanstest](azure-to-azure-tutorial-dr-drill.md)
5. [Redundansväxla och växla tillbaka till den primära regionen](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Hur garanteras kapaciteten i mål regionen?
Site Recoverys teamet arbetar med Azure Capacity Management-teamet för att planera tillräcklig infrastruktur kapacitet och för att se till att de virtuella datorer som skyddas av Site Recovery för har distribuerats till mål regionen När redundansväxlingen initieras.

## <a name="replication"></a>Replikering

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Kan jag replikera virtuella datorer som är aktiverade via Azure Disk Encryption?

Ja, Site Recovery stöder haveri beredskap för virtuella datorer med aktive rad Azure Disk Encryption (ADE). När du aktiverar replikering kopieras alla nödvändiga disk krypterings nycklar och hemligheter från käll regionen till mål regionen i användar kontexten. Om du inte har rätt behörighet kan du använda ett färdigt skript för säkerhets administratören för att kopiera nycklar och hemligheter.

- Site Recovery stöder ADE för virtuella Azure-datorer som kör Windows.
- Site Recovery stöder ADE version 0,1, med ett schema som använder Azure Active Directory (AAD) och version 1,1, utan AAD. [Läs mer](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata).
- ADE version 1,1, de virtuella Windows-datorerna måste användas med hanterade diskar.
- [Läs mer](azure-to-azure-how-to-enable-replication-ade-vms.md) om hur du aktiverar replikering för krypterade virtuella datorer.



### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan jag replikera virtuella datorer till en annan prenumeration?
Ja, du kan replikera virtuella Azure-datorer till en annan prenumeration inom samma Azure AD-klient.
Det är enkelt att konfigurera DR [över prenumerationer](https://azure.microsoft.com/blog/cross-subscription-dr) . Du kan välja en annan prenumeration vid tidpunkten för replikeringen.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Kan jag replikera virtuella Azure-datorer i zonen till en annan region?
Ja, du kan [Replikera zon-fästa virtuella datorer](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) till en annan region.

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?

Ja, du kan utesluta diskar vid tidpunkten för skyddet med hjälp av PowerShell. Mer information finns i [artikeln](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Kan jag lägga till nya diskar i replikerade virtuella datorer och aktivera replikering för dem?

Ja, detta stöds för virtuella Azure-datorer med hanterade diskar. När du lägger till en ny disk till en virtuell Azure-dator som är aktive rad för replikering, visar replikeringstillståndet för den virtuella datorn en varning med en anteckning som anger att en eller flera diskar på den virtuella datorn är tillgängliga för skydd. Du kan aktivera replikering för tillagda diskar.
- Om du aktiverar skydd för de tillagda diskarna försvinner varningen efter den inledande replikeringen.
- Om du väljer att inte aktivera replikering för disken kan du välja att ignorera varningen.
- När du växlar över en virtuell dator där du lägger till en disk och aktiverar replikering för den, visar replikerings platserna de diskar som är tillgängliga för återställning. Om en virtuell dator till exempel har en enskild disk och du lägger till en ny, kommer de replikerings punkter som skapades innan du lade till disken att visa att replikerings platsen består av "1 av 2 diskar".

Site Recovery stöder inte "Hypertext Remove" på en disk från en replikerad virtuell dator. Om du tar bort en virtuell dator disk måste du inaktivera och sedan återaktivera replikering för den virtuella datorn.


### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?
Replikering är kontinuerlig när du replikerar virtuella Azure-datorer till en annan Azure-region. Mer information finns i arkitekturen för [Azure-till-Azure-replikering](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Kan jag replikera virtuella datorer inom en region? Jag behöver detta för att migrera virtuella datorer.
Du kan inte använda en Azure-till-Azure DR-lösning för att replikera virtuella datorer inom en region.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Kan jag replikera virtuella datorer till valfri Azure-region?
Med Site Recovery kan du replikera och återställa virtuella datorer mellan två regioner i samma geografiska kluster. Geografiska kluster definieras med data svars tid och suveränitet i åtanke. Mer information finns i [support mat ris](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)för Site Recovery region.

### <a name="does-site-recovery-require-internet-connectivity"></a>Kräver Site Recovery Internet anslutning?

Nej, Site Recovery kräver ingen Internet anslutning. Men det krävs åtkomst till Site Recovery URL: er och IP-intervall, enligt vad som anges i [den här artikeln](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Kan jag replikera programmet med separata resurs grupper för separata nivåer?
Ja, du kan replikera programmet och behålla haveri beredskaps konfigurationen i separat resurs grupp också.
Om du till exempel har ett program med varje nivå app, DB och webb i separat resurs grupp måste du klicka [på tre gånger om för](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) att skydda alla nivåer. Site Recovery kommer att replikera dessa tre nivåer i tre olika resurs grupper.

## <a name="replication-policy"></a>Replikeringsprincip

### <a name="what-is-a-replication-policy"></a>Vad är en replikeringsprincip?
Den definierar inställningarna för Retentions historiken för återställnings punkter och frekvensen för programkonsekventa ögonblicks bilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningar för:

* 24 timmar för Retentions historiken för återställnings punkter.
* 60 minuter för frekvensen av programkonsekventa ögonblicks bilder.

[Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Vad är en krasch-konsekvent återställnings punkt?
En kraschad återställnings punkt representerar data på disken som om den virtuella datorn kraschade eller om ström sladden hämtades från servern vid tidpunkten för ögonblicks bilden togs. Det innehåller inte något som fanns i minnet när ögonblicks bilden togs.

Idag kan de flesta program återställa sig väl från kraschbaserade ögonblicks bilder. En kraschad återställnings punkt är vanligt vis tillräckligt för operativ system och program som inte kommer från en databas, t. ex. fil servrar, DHCP-servrar och utskrifts servrar.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Vad är frekvensen för generering av kraschbaserade återställnings punkter?
Site Recovery skapar en kraschad återställnings punkt var femte minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Vad är en programkonsekvent återställnings punkt?
Programkonsekventa återställnings punkter skapas från programkonsekventa ögonblicks bilder. Programkonsekventa återställnings punkter fångar samma data som kraschbaserade ögonblicks bilder, med tillägg av alla data i minnet och alla pågående transaktioner.
På grund av det extra innehållet är programkonsekventa ögonblicks bilder de mest inblandade och ta den längsta som ska utföras. Vi rekommenderar programkonsekventa återställnings punkter för databas operativ system och program som SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Vad är effekten av programkonsekventa återställnings punkter för program prestanda?
Om du överväger programkonsekventa återställnings punkter insamlas alla data i minnet och i processen krävs det ramverk som VSS i Windows för att ta programmet ur beaktande. Detta, om det har gjorts mycket ofta, kan prestanda påverkas om arbets belastningen redan är mycket upptagen. Det rekommenderas vanligt vis inte att använda låg frekvens för programkonsekventa återställnings punkter för arbets belastningar som inte är databas och även för databas arbets belastning 1 timme.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Vad är den minsta frekvensen för generering av programkonsekventa återställnings punkter?
Site Recovery kan skapa en programkonsekvent återställnings punkt med en minsta frekvens på om 1 timme.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hur genereras och sparas återställnings punkter?
För att förstå hur Site Recovery genererar återställnings punkter tar vi ett exempel på en replikeringsprincip som har ett fönster för kvarhållning av återställnings punkter i 24 timmar och en programkonsekvent frekvens ögonblicks bild av 1 timme.

Site Recovery skapar en kraschad återställnings punkt var femte minut. Användaren kan inte ändra den här frekvensen. För den senaste timmen får användaren 12 kraschbaserade punkter och 1 app-konsekvent punkt för att välja bland. När tiden pågår Site Recovery rensar alla återställnings punkter utöver den senaste timmen och sparar bara 1 återställnings punkt per timme.

Följande skärm bild illustrerar exemplet. I skärm bilden:

1. För tiden under den senaste timmen finns återställnings punkter med en frekvens på 5 minuter.
2. Vid tiden efter den senaste timmen har Site Recovery endast 1 återställnings punkt.

   ![Lista över genererade återställnings punkter](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?
Den äldsta återställnings punkten som du kan använda är 72 timmar.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Vad händer om jag har en replikeringsprincip på 24 timmar och ett problem förhindrar Site Recovery från att skapa återställnings punkter i mer än 24 timmar? Kommer mina tidigare återställnings punkter att gå förlorade?
Nej, Site Recovery kommer att behålla alla dina tidigare återställnings punkter. Beroende på vilka återställnings punkter som kvarhålls, 24 timmar i det här fallet Site Recovery ersätter den äldsta punkten endast om det finns en generering av nya punkter. I det här fallet, eftersom det inte finns någon ny återställnings punkt som genereras på grund av problem, kommer alla gamla punkter att förbli intakta när vi når fönstret med kvarhållning.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hur ändrar jag replikeringsprincipen när replikering har Aktiver ATS på en virtuell dator?
Gå till **Site Recovery valv** > **Site Recovery Infrastructure** > **Replication policies**. Välj den princip som du vill redigera och spara ändringarna. Alla ändringar kommer också att gälla för alla befintliga replikeringar.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Är alla återställnings punkter en fullständig kopia av den virtuella datorn eller en differentiell?
Den första återställnings punkten som genereras har en fullständig kopia. Eventuella återställnings punkter har delta ändringar.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Ökar lagrings kostnaden genom att öka lagrings tiden för återställnings punkter?
Ja. Om du ökar Retentions perioden från 24 timmar till 72 timmar sparar Site Recovery återställnings punkterna för ytterligare 48 timmar. Den tillagda tiden debiteras lagrings kostnader. Om till exempel en enda återställnings punkt har delta ändringar på 10 GB och kostnaden per GB är $0,16 per månad, blir ytterligare kostnader $1,6 * 48 per månad.

## <a name="multi-vm-consistency"></a>Konsekvens för flera virtuella datorer

### <a name="what-is-multi-vm-consistency"></a>Vad är konsekvens för flera virtuella datorer?
Det innebär att se till att återställnings punkten är konsekvent över alla replikerade virtuella datorer.
Site Recovery ger ett alternativ för konsekvens för flera virtuella datorer, som, när du väljer den, skapar en replikeringsgrupp för att replikera alla datorer tillsammans som ingår i gruppen.
Alla virtuella datorer har delade krasch-konsekventa och programkonsekventa återställnings punkter när de har redundansväxlats.
Gå igenom självstudien för att [Aktivera konsekvens för flera virtuella datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Kan jag redundansväxla en enskild virtuell dator i en konsekvens grupp för flera virtuella datorer?
Genom att välja alternativet "Multi-VM-konsekvens" anger du att programmet har ett beroende på alla virtuella datorer i en grupp. Det är därför inte tillåtet att redundansväxla en enskild virtuell dator.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hur många virtuella datorer kan jag replikera som en del av en konsekvens grupp för flera virtuella datorer?
Du kan replikera 16 virtuella datorer tillsammans i en replikeringsgrupp.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>När bör jag aktivera konsekvens för flera virtuella datorer?
Eftersom det är processor intensivt, kan det påverka arbets Belastningens prestanda genom att aktivera konsekvens för flera virtuella datorer. Den bör endast användas om datorer kör samma arbets belastning och du behöver konsekvens på flera datorer. Om du till exempel har två SQL Server instanser och två webb servrar i ett program, bör du bara ha konsekvens för flera virtuella datorer för de SQL Server instanserna.


## <a name="failover"></a>Redundans

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Hur är kapaciteten säkerställd i mål regionen för virtuella Azure-datorer?
Site Recoverys teamet arbetar med Azure Capacity Management-teamet för att planera för tillräcklig infrastruktur kapacitet för att se till att de virtuella datorer som är aktiverade för haveri beredskap distribueras korrekt i mål regionen När redundansväxlingen initieras.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

Den sker inte automatiskt. Du startar redundans med ett enda klick i portalen, eller så kan du använda [PowerShell](azure-to-azure-powershell.md) för att utlösa en redundansväxling.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Kan jag behålla en offentlig IP-adress efter redundansväxlingen?

Den offentliga IP-adressen för produktions programmet kan inte behållas efter redundansväxlingen.
- Arbets belastningar som ingår i redundansväxlingen måste tilldelas en offentlig Azure-resurs som är tillgänglig i mål regionen.
- Du kan göra detta manuellt eller automatisera det med en återställnings plan.
- Lär dig hur du [konfigurerar offentliga IP-adresser efter en redundansväxling](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Kan jag behålla en privat IP-adress under redundansväxlingen?
Ja, du kan behålla en privat IP-adress. Som standard när du aktiverar haveri beredskap för virtuella Azure-datorer skapar Site Recovery mål resurser baserat på käll resurs inställningar. – För virtuella Azure-datorer som kon figurer ATS med statiska IP-adresser försöker Site Recovery etablera samma IP-adress för den virtuella mål datorn, om den inte används.
Lär dig mer om att [behålla IP-adresser under redundans](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Varför har servern tilldelats en ny IP-adress efter redundansväxlingen?

Site Recovery försöker ange IP-adressen vid redundansväxlingen. Om en annan virtuell dator tar den adressen Site Recovery anger nästa tillgängliga IP-adress som mål.
Lär dig mer om [att konfigurera nätverks mappning och IP-adresser för virtuella nätverk](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Vad är de **senaste (lägsta** återställnings punkterna)?
Det **senaste (lägsta** återställnings alternativet) bearbetar först alla data som har skickats till Site Recovery-tjänsten för att skapa en återställnings punkt för varje virtuell dator innan den växlar över till den. Det här alternativet ger det lägsta återställnings punkt målet, eftersom den virtuella datorn som skapades efter redundansväxlingen har alla data som repliker ATS till Site Recovery När redundansväxlingen utlöstes.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Har de **senaste (lägsta** återställnings punkten) återställnings punkter inverkan på redundans RTO?
Ja. Site Recovery bearbetar alla väntande data innan de växlar över, så det här alternativet har ett högre återställnings tids mål (RTO) jämfört med andra alternativ.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Vad betyder det **senaste** bearbetade alternativet i återställnings punkter?
Det **senast bearbetade** alternativet växlar över alla virtuella datorer i planen till den senaste återställnings punkten som Site Recovery bearbetas. Om du vill se den senaste återställnings punkten för en viss virtuell dator kontrollerar du de **senaste återställnings punkterna** i VM-inställningarna. Det här alternativet ger en låg RTO, eftersom ingen tid ägnas åt att bearbeta obearbetade data.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Vad händer om min primära region upplever ett oväntat avbrott?
Du kan utlösa en redundansväxling efter avbrottet. Site Recovery behöver inte anslutning från den primära regionen för att utföra redundansväxlingen.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>Vad är en RTO för en VM-redundans?
Site Recovery har ett [RTO-SLA på 2 timmar](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Men det mesta av tiden Site Recovery redundansväxla virtuella datorer på några minuter. Du kan beräkna RTO genom att gå till jobben med redundans som visar hur lång tid det tog att ta upp den virtuella datorn. För återställnings planens RTO, se avsnittet nedan.

## <a name="recovery-plans"></a>Återställningsplaner

### <a name="what-is-a-recovery-plan"></a>Vad är en återställnings plan?
En återställnings plan i Site Recovery dirigerar redundansväxlingen av virtuella datorer. Det hjälper dig att göra återställningen konsekvent korrekt, upprepnings bar och automatiserad. En återställnings plan hanterar följande behov för användaren:

- Definiera en grupp virtuella datorer som redundansväxlas
- Definiera beroenden mellan virtuella datorer så att programmet visas korrekt
- Automatisera återställningen tillsammans med anpassade manuella åtgärder för att uppnå andra uppgifter än redundansväxlingen av virtuella datorer

[Läs mer](site-recovery-create-recovery-plans.md) om återställnings planer.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hur uppnås ordningsföljd i en återställnings plan?

I en återställnings plan kan du skapa flera grupper för att uppnå sekvenseringen. Varje grupp växlar vid ett tillfälle. Virtuella datorer som är en del av samma grupp växlar över till varandra, följt av en annan grupp. Information om hur du modellerar ett program med hjälp av en återställnings plan finns i [om återställnings planer](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hur kan jag hitta RTO i en återställnings plan?
Om du vill kontrol lera RTO i en återställnings plan gör du ett redundanstest för återställnings planen och går till **Site Recovery jobb**.
I följande exempel tog jobbet SAPTestRecoveryPlan 8 minuter och 59 sekunder att redundansväxla alla virtuella datorer och utföra angivna åtgärder.

![Lista över Site Recovery jobb](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kan jag lägga till Automation-runbooks i återställnings planen?
Ja, du kan integrera Azure Automation runbooks i din återställnings plan. [Läs mer](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Skydd och återställning efter fel

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Är virtuella datorer i en DR-region automatiskt skyddade efter en redundansväxling från den primära regionen till en katastrof återställnings region?
Nej. När du [växlar över](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) virtuella Azure-datorer från en region till en annan startar de virtuella datorerna i Dr-regionen i oskyddat tillstånd. Om du vill återställa de virtuella datorerna till den primära regionen måste du [skydda](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) de virtuella datorerna i den sekundära regionen.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Vid tidpunkten för återskydd replikeras Site Recovery fullständiga data från den sekundära regionen till den primära regionen?
Det beror på situationen. Om käll regionen VM finns, till exempel, är det bara ändringar mellan käll disken och mål disken som synkroniseras. Site Recovery beräknar skillnaderna genom att jämföra diskarna och sedan överföra data. Den här processen tar vanligt vis några timmar. Mer information om vad som händer under återaktivering finns i [återaktivera skydd av virtuella Azure-datorer till den primära regionen]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Hur lång tid tar det att återställa?
Efter återaktivering är tiden för återställning av återställnings tid vanligt vis densamma som den tid som krävdes för redundans från den primära regionen till en sekundär region.

## <a name="capacity"></a>Kapaciteten

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Hur är kapaciteten säkerställd i mål regionen för virtuella Azure-datorer?
Site Recoverys teamet arbetar med Azure Capacity Management-teamet för att planera för tillräcklig infrastruktur kapacitet för att se till att de virtuella datorer som är aktiverade för haveri beredskap distribueras i mål regionen När redundansväxlingen initieras.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Fungerar Site Recovery med reserverade instanser?
Ja, du kan köpa [reserv instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Disaster Recovery-regionen och Site Recovery åtgärder för redundansväxling använder dem. </br> Ingen ytterligare konfiguration krävs.


## <a name="security"></a>Säkerhet

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?
Nej, Site Recovery inte fångar upp replikerade data och har inte någon information om vad som körs på dina virtuella datorer. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  
Site Recovery är ISO 27001:2013, 27018, HIPAA, DPA Certified och är i processen för SOC2-och FedRAMP JAB-utvärderingar.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
Ja, både kryptering under överföring och kryptering i [-vila i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.

## <a name="next-steps"></a>Nästa steg
* [Granska](azure-to-azure-support-matrix.md) support kraven.
* [Konfigurera](azure-to-azure-tutorial-enable-replication.md) Azure till Azure-replikering.
- Om du har frågor när du har läst den här artikeln kan du publicera dem i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
