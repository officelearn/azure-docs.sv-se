---
title: Vanliga frågor om azure vm-haveriberedskap med Azure Site Recovery
description: Den här artikeln besvarar vanliga frågor om Azure VM-haveriberedskap när du använder Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 3d71301534d56ef8eca68951c8c9f9a1570b3a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282264"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Vanliga frågor: Azure-to-Azure-haveriberedskap

Den här artikeln besvarar vanliga frågor om haveriberedskap av virtuella Azure-datorer till en annan Azure-region för när du använder [Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Allmänt

### <a name="how-is-site-recovery-priced"></a>Hur är Site Recovery prissatta?

Granska [Azure Site Recovery-priser för virtuella datorer](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hur fungerar den kostnadsfria nivån för Azure Site Recovery?

Varje instans som är skyddad med Azure Site Recovery är gratis för de första 31 dagarna av skydd. Efter den perioden är skyddet för varje instans till priser i [Azure Site Recovery-priser för virtuella Azure-datorer](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Under de första 31 dagarna, kommer jag att ådra mig några andra Azure-avgifter?

Ja. Även om Azure Site Recovery är gratis under de första 31 dagarna av en skyddad instans kan du debiteras avgifter för Azure Storage, lagringstransaktioner och dataöverföringar. En återställd virtuell dator kan också medföra Azure-beräkningsavgifter. Få fullständig information om priser på [Azure Site Recovery priser](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Vilka är de bästa metoderna för azure virtual machines haveriberedskap?

1. [Förstå Azure-to-Azure-arkitektur](azure-to-azure-architecture.md)
1. [Granska konfigurationerna som stöds och inte stöds](azure-to-azure-support-matrix.md)
1. [Konfigurera haveriberedskap för virtuella Azure-datorer](azure-to-azure-how-to-enable-replication.md)
1. [Kör ett redundanstest](azure-to-azure-tutorial-dr-drill.md)
1. [Växla över och växla tillbaka till den primära regionen](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Hur säkerställs kapaciteten i målregionen?

Site Recovery-teamet och Azure-kapacitetshanteringsgruppen planerar för tillräcklig infrastrukturkapacitet. När du startar en redundans hjälper teamen också till att säkerställa att VM-instanser som skyddas av platsåterställning distribueras till målregionen.

## <a name="replication"></a>Replikering

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Kan jag replikera virtuella datorer som aktiverats via Azure-diskkryptering?

Ja. Site Recovery stöder haveriberedskap av virtuella datorer som har Azure Disk Encryption aktiverat. När du aktiverar replikering kopierar Azure alla nödvändiga diskkrypteringsnycklar och hemligheter från källregionen till målregionen i användarkontexten. Om du inte har rätt behörighet kan säkerhetsadministratören använda ett skript för att kopiera nycklar och hemligheter.

- Site Recovery stöder Azure Disk Encryption för virtuella Azure-datorer som kör Windows.
- Site Recovery stöder Azure Disk Encryption version 0.1, som har ett schema som kräver Azure Active Directory (Azure AD). Site Recovery stöder också version 1.1, som inte kräver Azure AD. [Läs mer om tilläggsschemat för Azure-diskkryptering](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - För Azure Disk Encryption version 1.1 måste du använda Windows virtuella datorer med hanterade diskar.
  - [Läs mer](azure-to-azure-how-to-enable-replication-ade-vms.md) om hur du aktiverar replikering för krypterade virtuella datorer.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan jag replikera virtuella datorer till en annan prenumeration?

Ja, du kan replikera virtuella Azure-datorer till en annan prenumeration inom samma Azure AD-klientorganisation.

Konfigurera haveriberedskap [över prenumerationer](https://azure.microsoft.com/blog/cross-subscription-dr) genom att välja en annan prenumeration vid tidpunkten för replikeringen.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Kan jag replikera virtuella Azure-datorer till en annan region?

Ja, du kan [replikera zon-nålas virtuella datorer](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) till en annan region.

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?

Ja, du kan utesluta diskar vid tidpunkten för skyddet med hjälp av PowerShell. Mer information finns i [hur du utesluter diskar från replikering](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Kan jag lägga till nya diskar i replikerade virtuella datorer och aktivera replikering för dem?

Ja, det stöds att lägga till nya diskar i replikerade virtuella datorer och aktivera replikering för dem för Virtuella Azure-datorer med hanterade diskar. När du lägger till en ny disk i en Azure-virtuell dator som är aktiverad för replikering, visar replikeringshälsan för den virtuella datorn en varning. I den varningen anges att en eller flera diskar på den virtuella datorn är tillgängliga för skydd. Du kan aktivera replikering för tillagda diskar.

- Om du aktiverar skydd för de tillagda diskarna försvinner varningen efter den första replikeringen.
- Om du inte aktiverar replikering för disken kan du stänga av varningen.
- Om du växlar över en virtuell dator som har en extra disk och replikering aktiverad finns replikeringspunkter. Replikeringspunkterna visar de diskar som är tillgängliga för återställning.

Anta till exempel att en virtuell dator har en enda disk och att du lägger till en ny. Det kan finnas en replikeringspunkt som skapades innan du lade till disken. Den här replikeringspunkten visar att den består av "1 av 2 diskar".

Site Recovery stöder inte "hot remove" av en disk från en replikerad virtuell dator. Om du tar bort en VM-disk måste du inaktivera och sedan återaktivera replikering för den virtuella datorn.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Replikeringen är kontinuerlig när du replikerar virtuella Azure-datorer till en annan Azure-region. Mer information finns i [Azure-to-Azure-replikeringsarkitekturen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Kan jag replikera virtuella datorer inom en region? Jag behöver den här funktionen för att migrera virtuella datorer.

Du kan inte använda en Azure-to-Azure-diskåterställningslösning för att replikera virtuella datorer inom en region.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Kan jag replikera VM-instanser till alla Azure-regioner?

Genom att använda Site Recovery kan du replikera och återställa virtuella datorer mellan två regioner inom samma geografiska kluster. Geografiska kluster definieras med datafördröjning och suveränitet i åtanke. Mer information finns i [supportmatrisen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)för området Webbplatsåterställning .

### <a name="does-site-recovery-require-internet-connectivity"></a>Kräver Site Recovery internetanslutning?

Nej, Site Recovery kräver inte internetanslutning. Men det kräver åtkomst till url:er för webbplatsåterställning och IP-intervall, som nämns i [nätverk i Azure VM-haveriberedskap](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Kan jag replikera ett program som har en separat resursgrupp för separata nivåer?

Ja, du kan replikera programmet och behålla konfigurationen för haveriberedskap i en separat resursgrupp också.

Om programmet till exempel har varje nivås program, databas och webb i en separat resursgrupp måste du välja [replikeringsguiden](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) tre gånger för att skydda alla nivåer. Site Recovery replikerar dessa tre nivåer i tre olika resursgrupper.

## <a name="replication-policy"></a>Replikeringsprincip

### <a name="what-is-a-replication-policy"></a>Vad är en replikeringsprincip?

En replikeringsprincip definierar inställningarna för bevarandehistoriken för återställningspunkter. Principen definierar också frekvensen av appkonsekventa ögonblicksbilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningar för:

- 24 timmar för bevarandehistoriken för återställningspunkter.
- 60 minuter för frekvensen av appkonsekventa ögonblicksbilder.

[Läs mer om replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Vad är en kraschkonsekvent återställningspunkt?

En kraschkonsekvent återställningspunkt har data på disk som om du drog nätsladden från servern under ögonblicksbilden. Återställningspunkten för kraschkonsekvent innehåller inte något som fanns i minnet när ögonblicksbilden togs.

Idag kan de flesta program återhämta sig väl från kraschkonsekventa ögonblicksbilder. En kraschkonsekvent återställningspunkt är vanligtvis tillräckligt för operativsystem och program utan databas som filservrar, DHCP-servrar och utskriftsservrar.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Vad är frekvensen av kraschkonsekvent återställningspunktgenerering?

Site Recovery skapar en kraschkonsekvent återställningspunkt var 5:e minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Vad är en programkonsekvent återställningspunkt?

Programkonsekventa återställningspunkter skapas från programkonsekventa ögonblicksbilder. Programkonsekventa återställningspunkter samlar in samma data som kraschkonsekventa ögonblicksbilder samtidigt som data fångas in i minnet och alla transaktioner som pågår.

På grund av deras extra innehåll är programkonsekventa ögonblicksbilder de mest involverade och tar längst. Vi rekommenderar programkonsekventa återställningspunkter för databasoperativsystem och program som SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Hur påverkar programkonsekventa återställningspunkter på programmets prestanda?

Programkonsekventa återställningspunkter samlar in alla data i minnet och pågår. Eftersom återställningspunkter samlar in dessa data kräver de ramverk som Volume Shadow Copy Service i Windows för att aktivera programmet. Om insamlingsprocessen är frekvent kan det påverka prestanda när arbetsbelastningen redan är upptagen. Vi rekommenderar inte att du använder låg frekvens för appkonsekventa återställningspunkter för arbetsbelastningar som inte är databaser. Även för databasarbetsbelastning räcker det med 1 timme.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Vilken är den minsta frekvensen för generering av programkonsekvent återställningspunkt?

Site Recovery kan skapa en programkonsekvent återställningspunkt med en minsta frekvens på 1 timme.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hur genereras och sparas återställningspunkter?

Om du vill förstå hur webbplatsåterställning genererar återställningspunkter ska vi se ett exempel på en replikeringsprincip. Den här replikeringsprincipen har en återställningspunkt med ett 24-timmars kvarhållningsfönster och en appkonsekvent frekvensögonblicksbild på 1 timme.

Site Recovery skapar en kraschkonsekvent återställningspunkt var 5:e minut. Du kan inte ändra den här frekvensen. Under den senaste timmen kan du välja mellan 12 kraschkonsekventa punkter och 1 appkonsekvent punkt. Allteftersom tiden går, Site Recovery katrinplommon alla återställningspunkter utöver den senaste timmen och sparar endast 1 återställningspunkt per timme.

Följande skärmbild illustrerar exemplet. I skärmdumpen:

- Inom den senaste timmen finns det återställningspunkter med en frekvens på 5 minuter.
- Utöver den senaste timmen håller Site Recovery endast 1 återställningspunkt.

   ![Lista över genererade återställningspunkter](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återhämta mig?

Den äldsta återställningspunkten som du kan använda är 72 timmar.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Jag har en replikeringspolicy på 24 timmar. Vad händer om ett problem förhindrar att webbplatsåterställning genererar återställningspunkter i mer än 24 timmar? Kommer mina tidigare återställningspoäng att gå förlorade?

Nej, Site Recovery behåller alla dina tidigare återställningspunkter. Beroende på återställningspunkternas kvarhållningsfönster ersätter Site Recovery den äldsta punkten endast om den genererar nya punkter. På grund av problemet kan site recovery inte generera några nya återställningspunkter. Tills det finns nya återställningspunkter, kommer alla gamla punkter kvar när du når fönstret för kvarhållning.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>När replikeringen är aktiverad på en virtuell dator, hur ändrar jag replikeringsprincipen?

Gå till principer för > platsåterställningsvalvsplatsåterställningsinfrastrukturreplikeringsprinciper**Site Recovery Infrastructure** > **Replication policies**. **Site Recovery Vault** Markera den princip som du vill redigera och spara ändringarna. Alla ändringar gäller även för alla befintliga replikeringar.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Är alla återställningspunkter en fullständig kopia av den virtuella datorn eller en skillnad?

Den första återställningspunkten som genereras har hela kopian. Alla efterföljande återställningspunkter har deltaändringar.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Ökar lagringstiden för återställningspunkter lagringskostnaden?

Ja, om du ökar kvarhållningsperioden från 24 timmar till 72 timmar sparar Site Recovery återställningspunkterna i ytterligare 48 timmar. Den extra tiden medför lagringsavgifter. En enda återställningspunkt kan till exempel ha deltaändringar på 10 GB med en kostnad per GB på 0,16 USD per månad. Ytterligare avgifter skulle vara $ 1,60 × 48 per månad.

## <a name="multi-vm-consistency"></a>Konsekvens för flera virtuella datorer

### <a name="what-is-multi-vm-consistency"></a>Vad är konsekvens för flera virtuella datorer?

Multi-VM konsekvens säkerställer att återställningspunkten är konsekvent över alla replikerade virtuella datorer.

Site Recovery ger ett **konsekvensalternativ för flera virtuella datorer,** vilket skapar en replikeringsgrupp av alla datorer.

När du växlar över de virtuella datorerna har de delade kraschkonsekventa och appkonsekventa återställningspunkter.

Gå igenom självstudien för att [aktivera konsekvens för flera virtuella datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Kan jag växla över en enskild virtuell dator i en konsekvensreplikeringsgrupp för flera virtuella datorer?

När du väljer konsekvensalternativet **för flera virtuella datorer** anger du att programmet har ett beroende av alla virtuella datorer i en grupp. Redundans för en enda virtuell dator är inte tillåtet.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hur många virtuella datorer kan jag replikera som en del av en konsekvensreplikeringsgrupp för flera virtuella datorer?

Du kan replikera 16 virtuella datorer tillsammans i en replikeringsgrupp.

### <a name="when-should-i-enable-multi-vm-consistency"></a>När ska jag aktivera konsekvens för flera virtuella datorer?

Eftersom konsekvens för flera virtuella datorer är processorkrävande kan det påverka arbetsbelastningens prestanda. Använd konsekvens för flera virtuella datorer endast om datorer kör samma arbetsbelastning och du behöver konsekvens på flera datorer. Om du till exempel har två SQL Server-instanser och två webbservrar i ett program bör du ha konsekvens för flera virtuella datorer endast för SQL Server-instanserna.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Kan du lägga till en redan replikerande virtuell dator i en replikeringsgrupp?

Du kan lägga till en virtuell dator i en ny replikeringsgrupp när du aktiverar replikering. Du kan också lägga till en virtuell dator i en befintlig replikeringsgrupp när du aktiverar replikering. Du kan dock inte lägga till en redan replikerande virtuell dator till en ny replikeringsgrupp eller befintlig replikeringsgrupp.

## <a name="failover"></a>Redundans

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hur säkerställs kapaciteten i målregionen för virtuella Azure-datorer?

Site Recovery-teamet och Azure-kapacitetshanteringsgruppen planerar för tillräcklig infrastrukturkapacitet. När du startar en redundans hjälper teamen också till att säkerställa att VM-instanser som skyddas av platsåterställning distribueras till målregionen.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

Den sker inte automatiskt. Du kan starta redundans med ett enda klick i portalen eller använda [PowerShell](azure-to-azure-powershell.md) för att utlösa en redundans.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Kan jag behålla en offentlig IP-adress efter en redundans?

Du kan inte behålla den offentliga IP-adressen för produktionsprogrammet efter en redundans.

När du tar upp en arbetsbelastning som en del av redundansprocessen måste du tilldela en offentlig Azure-IP-resurs till arbetsbelastningen. Azure public IP-resurs måste vara tillgänglig i målregionen. Du kan tilldela Azure public IP-resurs manuellt eller så kan du automatisera den med en återställningsplan. Lär dig hur du [konfigurerar offentliga IP-adresser efter redundans](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Kan jag behålla en privat IP-adress under en redundans?

Ja, du kan behålla en privat IP-adress. När du aktiverar haveriberedskap för virtuella Azure-datorer skapar site recovery som standard målresurser baserat på källresursinställningar. För virtuella Azure-datorer som konfigurerats med statiska IP-adresser försöker Site Recovery etablera samma IP-adress för måldatorn om den inte används.
Läs mer om hur du [behåller IP-adresser under redundans.](site-recovery-retain-ip-azure-vm-failover.md)

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Varför tilldelas servern en ny IP-adress efter en redundans?

Site Recovery försöker ange IP-adressen vid tidpunkten för redundans. Om en annan virtuell dator tar den adressen anger Site Recovery nästa tillgängliga IP-adress som mål.

Läs mer om [hur du konfigurerar nätverksmappning och IP-adressering för virtuella nätverk](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Vad är **senaste (lägsta RPO) återställningspunkter?**

Det **senaste (lägsta RPO)-alternativet** bearbetar först alla data som har skickats till site recovery. När tjänsten bearbetar data skapas en återställningspunkt för varje virtuell dator innan den går över till den virtuella datorn. Det här alternativet ger det lägsta återställningspunktsmålet (RPO). Den virtuella datorn som skapades efter redundans har alla data replikerade till Site Recovery från när redundansen utlöstes.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Har **senaste (lägsta RPO)** återställningspunkter har en inverkan på redundans RTO?

Ja. Site Recovery bearbetar alla väntande data innan de misslyckas över, så det här alternativet har ett högre återställningstidsmål (RTO) jämfört med andra alternativ.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Vad betyder det senaste bearbetade alternativet i **återställningspunkter?**

Det **senaste bearbetade** alternativet misslyckas över alla virtuella datorer i planen till den senaste återställningspunkten som Site Recovery bearbetade. Om du vill se den senaste återställningspunkten för en viss virtuell dator kontrollerar du **de senaste återställningspunkterna** i vm-inställningarna. Det här alternativet ger en låg RTO, eftersom ingen tid går åt till att bearbeta obearbetade data.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Vad händer om min primära region drabbas av ett oväntat avbrott?

Du kan utlösa en redundans efter avbrottet. Platsåterställning behöver inte anslutningen från den primära regionen för att göra redundansen.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Vad är en RTO för en vm-redundans?

Site Recovery har en [RTO SLA på 2 timmar](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Men för det mesta misslyckas Site Recovery över virtuella datorer inom några minuter. Du kan beräkna RTO genom att gå till redundansjobb, som visar den tid det tog att ta upp den virtuella datorn. För återställningsplan RTO, se nästa avsnitt.

## <a name="recovery-plans"></a>Återställningsplaner

### <a name="what-is-a-recovery-plan"></a>Vad är en återhämtningsplan?

En återställningsplan i Site Recovery dirigerar redundansk återställning av virtuella datorer. Det hjälper till att göra återställningen konsekvent korrekt, repeterbar och automatiserad. En återställningsplan åtgärdar följande behov:

- Definiera en grupp virtuella datorer som växlar över tillsammans
- Definiera beroenden mellan virtuella datorer så att programmet kommer upp korrekt
- Automatisera återställningen tillsammans med anpassade manuella åtgärder för att uppnå andra uppgifter än redundans för virtuella datorer

Läs mer [om hur du skapar återställningsplaner](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hur uppnås sekvensering i en återhämtningsplan?

I en återställningsplan kan du skapa flera grupper för att uppnå sekvensering. Varje grupp misslyckas över på en gång. Virtuella datorer som ingår i samma grupp växlar över tillsammans, följt av en annan grupp. Mer information om hur du modellerar ett program med hjälp av en återställningsplan finns i [Om återställningsplaner](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hur hittar jag RTO för en återhämtningsplan?

Om du vill kontrollera RTO för en återställningsplan gör du en testväxling för återställningsplanen och går till **site recovery-jobb**.
I följande exempel läser du jobbet **SAPTestRecoveryPlan**. Jobbet tog 8 minuter och 59 sekunder att växla över alla virtuella datorer och utföra angivna åtgärder.

![Lista över jobb för webbplatsåterställning](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kan jag lägga till automatiseringskörningsböcker i återställningsplanen?

Ja, du kan integrera Azure Automation-runbooks i återställningsplanen. Läs mer om [att lägga till Azure Automation-runbooks](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Återbeskydd och återställning

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Jag misslyckades över från den primära regionen till en katastrofåterställning region. Skyddas virtuella datorer i en DR-region automatiskt?

Nej. När du [växlar över](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) virtuella Azure-datorer från en region till en annan startar de virtuella datorerna i DR-regionen i ett oskyddat tillstånd. Om du vill återställa de virtuella datorerna till den primära regionen måste du [återrotera](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) de virtuella datorerna i den sekundära regionen.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Replikerar site recovery fullständiga data från den sekundära regionen till den primära regionen vid återbeskyddning?

Det beror på situationen. Om den virtuella källregionens virtuella dator finns synkroniseras endast ändringar mellan källdisken och måldisken. Site Recovery beräknar skillnaderna genom att jämföra diskarna och sedan överför data. Denna process tar vanligtvis några timmar. Mer information om vad som händer under återbeskydd finns i [Reprotect misslyckades över Azure VM-instanser till den primära regionen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Hur lång tid tar det att återställa efter fel?

Efter återbeskydd tar återställningen ungefär lika lång tid som tar det att växla över från den primära regionen till en sekundär region.

## <a name="capacity"></a><a name="capacity"></a>Kapacitet

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hur säkerställs kapaciteten i målregionen för virtuella Azure-datorer?

Site Recovery-teamet och Azure-kapacitetshanteringsgruppen planerar för tillräcklig infrastrukturkapacitet. När du startar en redundans hjälper teamen också till att säkerställa att VM-instanser som skyddas av platsåterställning distribueras till målregionen.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Fungerar Site Recovery med reserverade instanser?

Ja, du kan köpa [reserverade virtuella Azure-datorer](https://azure.microsoft.com/pricing/reserved-vm-instances/) i regionen för haveriberedskap och redundansåtgärder för platsåterställning använder dem. Ingen ytterligare konfiguration behövs.

## <a name="security"></a>Säkerhet

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?

Nej, site recovery avlyssnar inte replikerade data och den har ingen information om vad som körs på dina virtuella datorer. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.

Site Recovery är ISO 27001:2013, 27018, HIPAA och DPA-certifierad. Tjänsten genomgår SOC2- och FedRAMP JAB-bedömningar.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?

Ja, både kryptering under överföring och [kryptering i vila i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.

## <a name="next-steps"></a>Nästa steg

- [Granska Azure-to-Azure-supportkrav](azure-to-azure-support-matrix.md).
- [Konfigurera Azure-to-Azure-replikering](azure-to-azure-tutorial-enable-replication.md).
- Om du har frågor efter att ha läst den här artikeln kan du publicera dem på [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
