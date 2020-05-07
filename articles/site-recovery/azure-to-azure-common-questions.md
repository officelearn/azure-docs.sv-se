---
title: Vanliga frågor om haveri beredskap för virtuella Azure-datorer med Azure Site Recovery
description: I den här artikeln besvaras vanliga frågor om haveri beredskap för virtuella Azure-datorer när du använder Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: b6f665c5b0f2fbd291d20ef21d0a447d20f7c2da
ms.sourcegitcommit: 291b2972c7f28667dc58f66bbe9d9f7d11434ec1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82738056"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Vanliga frågor: katastrof återställning från Azure till Azure

I den här artikeln besvaras vanliga frågor om haveri beredskap för virtuella Azure-datorer till en annan Azure-region när du använder [Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Allmänt

### <a name="how-is-site-recovery-priced"></a>Hur kostar det att Site Recovery?

Granska [Azure Site Recovery prissättning för virtuella datorer](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hur fungerar den kostnads fria nivån för Azure Site Recovery?

Varje instans som skyddas med Azure Site Recovery är kostnads fri under de första 31 dagars skyddet. Efter den perioden är skyddet för varje instans enligt priserna i [Azure Site Recovery priser för Azure Virtual Machines](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Under de första 31 dagarna kommer jag att debiteras andra Azure-avgifter?

Ja. Även om Azure Site Recovery är kostnads fritt under de första 31 dagarna i en skyddad instans kan du debiteras för Azure Storage, lagrings transaktioner och data överföringar. En återställd virtuell dator kan också ådra sig Azure Compute-avgifter. Få fullständig information om priser på [Azure Site Recovery priser](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Vilka är metod tips för återställning av Azure Virtual Machines haveri beredskap?

1. [Förstå Azure-till-Azure-arkitekturen](azure-to-azure-architecture.md)
1. [Granska de konfigurationer som stöds och som inte stöds](azure-to-azure-support-matrix.md)
1. [Konfigurera katastrof återställning för virtuella Azure-datorer](azure-to-azure-how-to-enable-replication.md)
1. [Kör ett redundanstest](azure-to-azure-tutorial-dr-drill.md)
1. [Redundansväxla och växla tillbaka till den primära regionen](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Hur säkerställs kapacitet i mål regionen?

Team planen för Site Recovery team och Azure Capacity Management för tillräcklig infrastruktur kapacitet. När du startar en redundansväxling hjälper teamen också till att se till att de virtuella dator instanser som skyddas av Site Recovery kommer att distribueras till mål regionen.

## <a name="replication"></a>Replikering

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Kan jag replikera virtuella datorer som är aktiverade via Azure Disk Encryption?

Ja. Site Recovery stöder haveri beredskap för virtuella datorer som har Azure Disk Encryption aktiverat. När du aktiverar replikering kopierar Azure alla nödvändiga disk krypterings nycklar och hemligheter från käll regionen till mål regionen i användar kontexten. Om du inte har rätt behörighet kan säkerhets administratören använda ett skript för att kopiera nycklar och hemligheter.

- Site Recovery stöder Azure Disk Encryption för virtuella Azure-datorer som kör Windows.
- Site Recovery stöder Azure Disk Encryption version 0,1, som har ett schema som kräver Azure Active Directory (Azure AD). Site Recovery stöder också version 1,1, som inte kräver Azure AD. [Läs mer om tilläggs schemat för Azure Disk Encryption](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - För Azure Disk Encryption version 1,1 måste du använda virtuella Windows-datorer med Managed disks.
  - [Läs mer](azure-to-azure-how-to-enable-replication-ade-vms.md) om hur du aktiverar replikering för krypterade virtuella datorer.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan jag replikera virtuella datorer till en annan prenumeration?

Ja, du kan replikera virtuella Azure-datorer till en annan prenumeration inom samma Azure AD-klient.

Konfigurera haveri beredskap [mellan prenumerationer](https://azure.microsoft.com/blog/cross-subscription-dr) genom att välja en annan prenumeration vid tidpunkten för replikeringen.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Kan jag replikera virtuella Azure-datorer i zonen till en annan region?

Ja, du kan [Replikera zon-fästa virtuella datorer](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) till en annan region.

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?

Ja, du kan utesluta diskar vid tidpunkten för skyddet med hjälp av PowerShell. Mer information finns i [så här undantar du diskar från replikering](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Kan jag lägga till nya diskar i replikerade virtuella datorer och aktivera replikering för dem?

Ja, det finns stöd för att lägga till nya diskar i replikerade virtuella datorer och aktivera replikering för dem för virtuella Azure-datorer med hanterade diskar. När du lägger till en ny disk till en virtuell Azure-dator som är aktive rad för replikering, visar replikeringstillståndet för den virtuella datorn en varning. Varningen anger att en eller flera diskar på den virtuella datorn är tillgängliga för skydd. Du kan aktivera replikering för tillagda diskar.

- Om du aktiverar skydd för de tillagda diskarna försvinner varningen efter den inledande replikeringen.
- Om du inte aktiverar replikering för disken kan du stänga av varningen.
- Om du växlar över en virtuell dator som har en tillagd disk och replikering aktive rad, finns det några replikerings punkter. På platserna visas de diskar som är tillgängliga för återställning.

Anta till exempel att en virtuell dator har en enda disk och att du lägger till en ny. Det kan finnas en plats för replikering som skapades innan du lade till disken. Den här replikerings platsen visar att den består av "1 av 2 diskar".

Site Recovery stöder inte "Hypertext Remove" på en disk från en replikerad virtuell dator. Om du tar bort en virtuell dator disk måste du inaktivera och sedan återaktivera replikering för den virtuella datorn.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Replikering är kontinuerlig när du replikerar virtuella Azure-datorer till en annan Azure-region. Mer information finns i arkitekturen för [Azure-till-Azure-replikering](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Kan jag replikera virtuella datorer inom en region? Jag behöver den här funktionen för att migrera virtuella datorer.

Du kan inte använda en Azure-till-Azure disk återställnings lösning för att replikera virtuella datorer inom en region.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Kan jag replikera VM-instanser till alla Azure-regioner?

Med hjälp av Site Recovery kan du replikera och återställa virtuella datorer mellan två regioner i samma geografiska kluster. Geografiska kluster definieras med data svars tid och suveränitet i åtanke. Mer information finns i [support mat ris](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)för Site Recovery region.

### <a name="does-site-recovery-require-internet-connectivity"></a>Kräver Site Recovery Internet anslutning?

Nej, Site Recovery kräver ingen Internet anslutning. Men det krävs åtkomst till Site Recovery URL: er och IP-intervall, som du ser i [nätverk i haveri beredskap för virtuella Azure-datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Kan jag replikera ett program som har en separat resurs grupp för separata nivåer?

Ja, du kan replikera programmet och behålla haveri beredskaps konfigurationen i en separat resurs grupp också.

Om ditt program till exempel har varje nivås program, databas och webb i en separat resurs grupp, måste du välja [guiden replikering](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) tre gånger för att skydda alla nivåer. Site Recovery kommer att replikera dessa tre nivåer till tre olika resurs grupper.

## <a name="replication-policy"></a>Replikeringsprincip

### <a name="what-is-a-replication-policy"></a>Vad är en replikeringsprincip?

En replikeringsprincip definierar inställningarna för lagrings historiken för återställnings punkter. Principen definierar också frekvensen för programkonsekventa ögonblicks bilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningar för:

- 24 timmar för Retentions historiken för återställnings punkter.
- 60 minuter för frekvensen av programkonsekventa ögonblicks bilder.

[Läs mer om replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Vad är en krasch-konsekvent återställnings punkt?

En kraschad återställnings punkt har data på disken som om du hämtade ström sladden från servern under ögonblicks bilden. Den kraschbaserade återställnings punkten innehåller inte något som fanns i minnet när ögonblicks bilden togs.

Idag kan de flesta program återställa sig väl från kraschbaserade ögonblicks bilder. En kraschad återställnings punkt är vanligt vis tillräckligt för operativ system och program som inte kommer från en databas, t. ex. fil servrar, DHCP-servrar och utskrifts servrar.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Vad är frekvensen för generering av kraschbaserade återställnings punkter?

Site Recovery skapar en kraschad återställnings punkt var femte minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Vad är en programkonsekvent återställnings punkt?

Programkonsekventa återställnings punkter skapas från programkonsekventa ögonblicks bilder. Programkonsekventa återställnings punkter fångar samma data som kraschbaserade ögonblicks bilder samtidigt som data i minnet och alla transaktioner i processen fångas.

På grund av det extra innehållet är programkonsekventa ögonblicks bilder de mest inblandade och ta den längsta. Vi rekommenderar programkonsekventa återställnings punkter för databas operativ system och program som SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Vad är effekten av programkonsekventa återställnings punkter för program prestanda?

Programkonsekventa återställnings punkter fångar alla data i minnet och i processen. Eftersom återställnings punkter fångar dessa data, kräver de ramverk som tjänsten Volume Shadow Copy i Windows för att inaktive ring av programmet. Om insamlings processen används ofta kan den påverka prestanda när arbets belastningen redan är upptagen. Vi rekommenderar inte att du använder låg frekvens för programkonsekventa återställnings punkter för arbets belastningar som inte är databas. Det räcker med 1 timme för databas arbets belastningen.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Vad är den minsta frekvensen för generering av programkonsekventa återställnings punkter?

Site Recovery kan skapa en programkonsekvent återställnings punkt med en minsta frekvens på 1 timme.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hur genereras och sparas återställnings punkter?

För att förstå hur Site Recovery genererar återställnings punkter ser vi ett exempel på en replikeringsprincip. Den här replikeringsprincipen har en återställnings punkt med en 24-timmarsperiod och en programkonsekvent frekvens ögonblicks bild på 1 timme.

Site Recovery skapar en kraschad återställnings punkt var femte minut. Du kan inte ändra den här frekvensen. Under den senaste timmen kan du välja mellan 12 kraschbaserade punkter och 1 app-konsekvent punkt. När tiden pågår Site Recovery rensar alla återställnings punkter bortom den senaste timmen och sparar bara 1 återställnings punkt per timme.

Följande skärm bild illustrerar exemplet. I skärm bilden:

- Under den senaste timmen finns återställnings punkter med en frekvens på 5 minuter.
- Under den senaste timmen behålls Site Recovery endast en återställnings punkt.

   ![Lista över genererade återställnings punkter](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?

Den äldsta återställnings punkten som du kan använda är 72 timmar.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Jag har en replikeringsprincip på 24 timmar. Vad händer om ett problem förhindrar Site Recovery att skapa återställnings punkter i mer än 24 timmar? Kommer mina tidigare återställnings punkter att gå förlorade?

Nej, Site Recovery kommer att behålla alla dina tidigare återställnings punkter. Beroende på återställnings punkternas kvarhållning fönster ersätter Site Recovery den äldsta punkten endast om den genererar nya punkter. På grund av problemet kan Site Recovery inte skapa några nya återställnings punkter. Tills det finns nya återställnings punkter är alla gamla punkter kvar när du har nått fönstret med kvarhållning.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hur ändrar jag replikeringsprincipen när replikering har Aktiver ATS på en virtuell dator?

Gå till **Site Recovery valv** > **Site Recovery Infrastructure** > **Replication policies**. Välj den princip som du vill redigera och spara ändringarna. Alla ändringar kommer också att gälla för alla befintliga replikeringar.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Är alla återställnings punkter en fullständig kopia av den virtuella datorn eller en differentiell?

Den första återställnings punkten som genereras har en fullständig kopia. Eventuella återställnings punkter har delta ändringar.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Ökar lagrings kostnaden genom att öka lagrings tiden för återställnings punkter?

Ja, om du ökar kvarhållningsperioden från 24 timmar till 72 timmar kommer Site Recovery att spara återställnings punkterna under ytterligare 48 timmar. Den tillagda tiden debiteras lagrings kostnader. En enda återställnings punkt kan till exempel ha delta ändringar på 10 GB med en kostnad per GB $0,16 per månad. Ytterligare kostnader blir $1,60 × 48 per månad.

## <a name="multi-vm-consistency"></a>Konsekvens för flera virtuella datorer

### <a name="what-is-multi-vm-consistency"></a>Vad är konsekvens för flera virtuella datorer?

Konsekvens för flera virtuella datorer säkerställer att återställnings punkten är konsekvent över alla replikerade virtuella datorer.

Site Recovery tillhandahåller konsekvens alternativ för **flera virtuella** datorer, vilket skapar en replikeringsgrupp för alla datorer.

När du växlar över de virtuella datorerna har de delade kraschbaserade och programkonsekventa återställnings punkter.

Gå igenom självstudien för att [Aktivera konsekvens för flera virtuella datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Kan jag redundansväxla en enskild virtuell dator i en konsekvens grupp för flera virtuella datorer?

När du väljer konsekvens alternativ för **flera virtuella** datorer meddelar du att programmet har ett beroende på alla virtuella datorer i en grupp. Redundans för enskild virtuell dator är inte tillåtet.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hur många virtuella datorer kan jag replikera som en del av en konsekvens grupp för flera virtuella datorer?

Du kan replikera 16 virtuella datorer tillsammans i en replikeringsgrupp.

### <a name="when-should-i-enable-multi-vm-consistency"></a>När bör jag aktivera konsekvens för flera virtuella datorer?

Eftersom konsekvens för flera virtuella datorer är processor intensivt, kan det påverka arbets Belastningens prestanda. Använd endast konsekvens för flera virtuella datorer om datorerna kör samma arbets belastning och du behöver konsekvens på flera datorer. Om du till exempel har två SQL Server instanser och två webb servrar i ett program, bör du bara ha konsekvens för flera virtuella datorer för de SQL Server instanserna.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Kan du lägga till en redan replikerad virtuell dator i en replikeringsgrupp?
Du kan lägga till en virtuell dator i en ny replikeringsgrupp samtidigt som du aktiverar replikering. Du kan också lägga till en virtuell dator i en befintlig replikeringsgrupp samtidigt som du aktiverar replikering. Du kan dock inte lägga till en redan replikerad virtuell dator till en ny replikeringsgrupp eller en befintlig replikeringsgrupp.
 
## <a name="failover"></a>Redundans


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hur säkerställs kapacitet i mål regionen för virtuella Azure-datorer?

Team planen för Site Recovery team och Azure Capacity Management för tillräcklig infrastruktur kapacitet. När du startar en redundansväxling hjälper teamen också till att se till att de virtuella dator instanser som skyddas av Site Recovery kommer att distribueras till mål regionen.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

Den sker inte automatiskt. Du kan starta redundans med ett enda klick i portalen, eller så kan du använda [PowerShell](azure-to-azure-powershell.md) för att utlösa en redundansväxling.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Kan jag behålla en offentlig IP-adress efter en redundansväxling?

Du kan inte behålla produktions programmets offentliga IP-adress efter en redundansväxling.

När du hämtar en arbets belastning som en del av redundansväxlingen måste du tilldela en offentlig Azure-resurs till arbets belastningen. Den offentliga Azure-IP-resursen måste vara tillgänglig i mål regionen. Du kan tilldela den offentliga Azure-resursen manuellt, eller så kan du automatisera den med en återställnings plan. Lär dig hur du [konfigurerar offentliga IP-adresser efter en redundansväxling](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Kan jag behålla en privat IP-adress under en redundansväxling?

Ja, du kan behålla en privat IP-adress. Som standard när du aktiverar haveri beredskap för virtuella Azure-datorer skapar Site Recovery mål resurser baserat på käll resurs inställningar. För Azure-Virtual Machines som kon figurer ATS med statiska IP-adresser försöker Site Recovery etablera samma IP-adress för den virtuella mål datorn om den inte används.
Lär dig mer om att [behålla IP-adresser under redundans](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Varför har servern tilldelats en ny IP-adress efter en redundansväxling?

Site Recovery försöker ange IP-adressen vid redundansväxlingen. Om en annan virtuell dator tar den adressen Site Recovery anger nästa tillgängliga IP-adress som mål.

Lär dig mer om [att konfigurera nätverks mappning och IP-adresser för virtuella nätverk](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Vad är de **senaste (lägsta** återställnings punkterna)?

Det **senaste (lägsta** återställnings bara alternativet) bearbetar först alla data som har skickats till Site Recovery. När tjänsten bearbetar data skapas en återställnings punkt för varje virtuell dator innan den virtuella datorn växlar över till den virtuella datorn. Det här alternativet ger lägsta återställnings punkt mål. Den virtuella datorn som skapades efter redundansväxlingen har alla data som repliker ATS till Site Recovery från När redundansväxlingen utlöstes.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Har de **senaste (lägsta** återställnings punkten) återställnings punkter inverkan på redundans RTO?

Ja. Site Recovery bearbetar alla väntande data innan de växlar över, så det här alternativet har ett högre återställnings tids mål (RTO) jämfört med andra alternativ.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Vad betyder det **senaste bearbetade** alternativet i återställnings punkter?

Det **senaste bearbetade** alternativet växlar över alla virtuella datorer i planen till den senaste återställnings punkten som Site Recovery bearbetas. Om du vill se den senaste återställnings punkten för en viss virtuell dator kontrollerar du de **senaste återställnings punkterna** i VM-inställningarna. Det här alternativet ger en låg RTO, eftersom ingen tid ägnas åt att bearbeta obearbetade data.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Vad händer om min primära region upplever ett oväntat avbrott?

Du kan utlösa en redundansväxling efter avbrottet. Site Recovery behöver inte anslutning från den primära regionen för att utföra redundansväxlingen.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Vad är en RTO för en VM-redundans?

Site Recovery har ett [RTO-SLA på 2 timmar](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Men i största delen av tiden Site Recovery växlar över virtuella datorer på några minuter. Du kan beräkna RTO genom att gå till jobben med redundans, vilket visar hur lång tid det tog att ta upp den virtuella datorn. För återställnings planens RTO, se nästa avsnitt.

## <a name="recovery-plans"></a>Återställningsplaner

### <a name="what-is-a-recovery-plan"></a>Vad är en återställnings plan?

En återställnings plan i Site Recovery dirigerar redundansväxlingen av virtuella datorer. Det hjälper dig att göra återställningen konsekvent korrekt, upprepnings bar och automatiserad. En återställnings plan hanterar följande behov:

- Definiera en grupp virtuella datorer som redundansväxlas
- Definiera beroenden mellan virtuella datorer så att programmet visas korrekt
- Automatisera återställningen tillsammans med anpassade manuella åtgärder för att uppnå andra uppgifter än redundansväxlingen av virtuella datorer

Lär dig mer [om att skapa återställnings planer](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hur uppnås ordningsföljd i en återställnings plan?

I en återställnings plan kan du skapa flera grupper för att uppnå sekvenseringen. Varje grupp växlar vid ett tillfälle. Virtuella datorer som är en del av samma grupp redundansväxlas tillsammans, följt av en annan grupp. Information om hur du modellerar ett program med hjälp av en återställnings plan finns i [om återställnings planer](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hur kan jag hitta RTO i en återställnings plan?

Om du vill kontrol lera RTO i en återställnings plan gör du ett redundanstest för återställnings planen och går till **Site Recovery jobb**.
I följande exempel går du till jobbet **SAPTestRecoveryPlan**. Jobbet tog 8 minuter och 59 sekunder att redundansväxla alla virtuella datorer och göra angivna åtgärder.

![Lista över Site Recovery jobb](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kan jag lägga till Automation-runbooks i återställnings planen?

Ja, du kan integrera Azure Automation runbooks i din återställnings plan. Lär dig mer om [att lägga till Azure Automation runbooks](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Skydd och återställning efter fel

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Jag har redundansväxlats från den primära regionen till en katastrof återställnings region. Skyddas virtuella datorer i en DR-region automatiskt?

Nej. När du [växlar över](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) virtuella Azure-datorer från en region till en annan startar de virtuella datorerna i Dr-regionen i oskyddat tillstånd. Om du vill återställa de virtuella datorerna till den primära regionen måste du [skydda](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) de virtuella datorerna i den sekundära regionen.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Vid tidpunkten för återskydd replikeras Site Recovery fullständiga data från den sekundära regionen till den primära regionen?

Det beror på situationen. Om den virtuella käll regionen finns, så synkroniseras bara ändringar mellan käll disken och mål disken. Site Recovery beräknar skillnaderna genom att jämföra diskarna och sedan överföra data. Den här processen tar vanligt vis några timmar. Mer information om vad som händer under återaktivering finns i [återaktivera skydd av virtuella Azure-instanser till den primära regionen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Hur lång tid tar det att återställa efter fel?

Efter återaktivering av återställningen tar återställning efter samma tid det tar att redundansväxla från den primära regionen till en sekundär region.

## <a name="capacity"></a><a name="capacity"></a>Kapacitet

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hur säkerställs kapacitet i mål regionen för virtuella Azure-datorer?

Team planen för Site Recovery team och Azure Capacity Management för tillräcklig infrastruktur kapacitet. När du startar en redundansväxling hjälper teamen också till att se till att de virtuella dator instanser som skyddas av Site Recovery kommer att distribueras till mål regionen.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Fungerar Site Recovery med reserverade instanser?

Ja, du kan köpa [reserverade virtuella Azure-datorer](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Disaster Recovery-regionen och Site Recovery åtgärder för redundans använder dem. Ingen ytterligare konfiguration krävs.

## <a name="security"></a>Säkerhet

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?

Nej, Site Recovery inte fångar upp replikerade data och det finns ingen information om vad som körs på dina virtuella datorer. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.

Site Recovery är ISO 27001:2013, 27018, HIPAA och DPA Certified. Tjänsten genomgår SOC2 och FedRAMP JAB-utvärderingar.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?

Ja, både kryptering och kryptering i [vila i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.

## <a name="next-steps"></a>Nästa steg

- [Granska support kraven för Azure-till-Azure](azure-to-azure-support-matrix.md).
- [Konfigurera Azure-till-Azure-replikering](azure-to-azure-tutorial-enable-replication.md).
- Om du har frågor när du har läst den här artikeln kan du publicera dem i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
