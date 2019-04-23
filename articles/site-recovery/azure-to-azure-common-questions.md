---
title: 'Vanliga frågor: Azure till Azure-haveriberedskap med Azure Site Recovery | Microsoft Docs'
description: Den här artikeln sammanfattas vanliga frågor när du konfigurerar haveriberedskap för virtuella Azure-datorer till en annan Azure-region med hjälp av Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 03/29/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 52a5022b49bac990321c3cf8661aa2a04e93b39a
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149741"
---
# <a name="common-questions-azure-to-azure-replication"></a>Vanliga frågor: Azure till Azure replikering

Den här artikeln innehåller svar på vanliga frågor om hur du distribuerar haveriberedskap (DR) för virtuella Azure-datorer till en annan Azure-region med hjälp av Azure Site Recovery. Om du har frågor när du har läst den här artikeln kan publicera dem på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Allmänt

### <a name="how-is-site-recovery-priced"></a>Hur prissätts Site Recovery?
Granska [priserna för Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) information.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hur fungerar den kostnadsfria nivån för Azure Site Recovery?
Varje instans som skyddas med Azure Site Recovery är kostnadsfri under de första 31 dagarna med skydd. Från och med den 32:a dagen debiteras skydd för instansen med de priser som anges ovan.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Förekommer det några andra avgifter för Azure under de första 31 dagarna?
Ja, även om Azure Site Recovery är kostnadsfritt under de första 31 dagarna för en skyddad instans kan du komma att debiteras för Azure Storage, lagringstransaktioner och dataöverföring. En återställd virtuell dator kan också debiteras för Azure-beräkningsavgifter. Hämta fullständig information om priser [här](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="what-are-the-best-practices-for-configuring-site-recovery-on-azure-vms"></a>Vad är bästa praxis för att konfigurera Site Recovery på Azure Virtual Machines?
1. [Förstå Azure till Azure-arkitektur](azure-to-azure-architecture.md)
2. [Granska konfigurationerna som stöds och stöds inte](azure-to-azure-support-matrix.md)
3. [Konfigurera haveriberedskap för virtuella Azure-datorer](azure-to-azure-how-to-enable-replication.md)
4. [Kör ett redundanstest](azure-to-azure-tutorial-dr-drill.md)
5. [Redundansväxla och återställa till den primära regionen](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-target-region-for-azure-vms"></a>Hur kapacitet garanteras i målregionen för virtuella Azure-datorer?
Azure Site Recovery (ASR)-teamet arbetar med Azure-kapacitet management-teamet att planera för tillräckligt med infrastrukturkapacitet för, i ett försök att se till att virtuella datorer som skyddas av ASR för disaster recovery distribueras har i regionen disaster recovery (DR) När åtgärder för ASR-redundans initieras.

## <a name="replication"></a>Replikering

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Kan jag replikera virtuella datorer aktiveras via Azure-diskkryptering?
Ja, kan du replikera dem. Finns i artikeln [replikera Azure disk encryption aktiverade virtuella datorer till en annan](azure-to-azure-how-to-enable-replication-ade-vms.md). Azure Site Recovery stöder för närvarande endast virtuella Azure-datorer som kör ett Windows-operativsystem och aktiverad för kryptering med Azure Active Directory (Azure AD)-appar.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan jag replikera virtuella datorer till en annan prenumeration?
Ja, kan du replikera virtuella Azure-datorer till en annan prenumeration inom samma Azure AD-klienten.
Konfigurera DR [mellan prenumerationer](https://azure.microsoft.com/blog/cross-subscription-dr) är enkel. Du kan välja en annan prenumeration vid tidpunkten för replikering.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Kan jag replikera zon fästa Azure virtuella datorer till en annan region?
Ja, du kan [replikera zon fästa datorer](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) till en annan region.

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?

Ja, kan du utesluta diskar vid tidpunkten för skydd med hjälp av PowerShell. Mer information finns [artikel](azure-to-azure-exclude-disks.md)

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?
Replikeringen är kontinuerlig när du replikerar virtuella Azure-datorer till en annan Azure-region. Mer information finns i den [arkitektur för Azure till Azure-replikering](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Kan jag replikera virtuella datorer inom en region? Jag behöver detta för att migrera virtuella datorer.
Du kan inte använda en katastrofåterställningslösning med Azure till Azure för att replikera virtuella datorer inom en region.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Kan jag replikera virtuella datorer till alla Azure-regioner?
Med Site Recovery kan du replikera och återställa virtuella datorer mellan alla två regioner inom samma geografiska kluster. Geografisk kluster har definierats med datafördröjning och datasuveränitet i åtanke. Mer information finns i Site Recovery [region stöd matrix](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Kräver Internetanslutning i Site Recovery?

Site Recovery kräver Nej, inte ansluten till internet. Men det kräver åtkomst till Site Recovery-webbadresser och IP-intervall som anges i [i den här artikeln](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Kan jag replikera program med separat resursgrupp för separata nivåer?
Ja, kan du replikera programmet och hålla katastrofberedskapskonfigurationen i separat resursgrupp för.
Till exempel om du har ett program med var och en nivåerna, db och webb i separat resursgrupp och du måste klicka på den [guiden replikering](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) tre gånger om för att skydda alla nivåer. ASR replikeras dessa tre nivåer i tre olika resursgrupp.

## <a name="replication-policy"></a>Replikeringsprincip

### <a name="what-is-a-replication-policy"></a>Vad är en replikeringsprincip?
Den definierar inställningarna för kvarhållningshistoriken återställningspunkter och frekvensen för appkonsekventa ögonblicksbilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningarna för:

* 24 timmar innan kvarhållningshistoriken återställningspunkter.
* 60 minuter för frekvensen för appkonsekventa ögonblicksbilder.

[Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Vad är en kraschkonsekvent återställningspunkt?
En kraschkonsekvent återställningspunkt representerar data på disken som om den virtuella datorn har kraschat eller kontakten hämtades från servern när ögonblicksbilden togs. De omfattar inte något som fanns i minnet när ögonblicksbilden togs.

Idag är kan de flesta program återställas från kraschkonsekventa ögonblicksbilder. En kraschkonsekvent återställningspunkt är vanligtvis tillräckligt för no database operativsystem och program som filservrar, DHCP-servrar och utskriftsservrar.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Vad är frekvensen för kraschkonsekventa recovery point generation?
Skapar site Recovery en kraschkonsekvent återställningspunkt var femte minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Vad är en programkonsekvent återställningspunkt?
Programkonsekventa återställningspunkter skapas från programkonsekventa ögonblicksbilder. Programkonsekventa återställningspunkter avbilda samma data som kraschkonsekventa ögonblicksbilder, och Lägg till alla data i minnet och alla pågående transaktioner.
På grund av deras extra innehåll programkonsekventa ögonblicksbilder är den vanligaste och tar den längsta för att utföra. Vi rekommenderar programkonsekventa återställningspunkter för databas-operativsystem och program som SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Vad är effekten av programkonsekventa återställningspunkter på programmets prestanda?
Överväger programkonsekventa återställningspunkter samlar in alla data i minnet och pågående kräver den framework som VSS i windows för att inaktivera programmet. Detta, kan om gjort ofta ha prestandapåverkan om arbetsbelastningen är redan mycket upptagen. Vanligtvis rekommenderas inte för att använda låg frekvens för appkonsekvent återställningspunkter för icke-databasarbetsbelastningar och även för databas-arbetsbelastning 1 timme räcker.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Vad är den lägsta frekvensen för programkonsekventa recovery point generation?
Skapar site Recovery kan en programkonsekvent återställningspunkt med en minimifrekvens på under timmen.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hur återställningspunkter genereras och sparas?
För att förstå hur Site Recovery genererar återställningspunkter, låt oss ta ett exempel på en replikeringsprincip som har en återställningspunkten kvarhållningsperiod på 24 timmar och en frekvens för appkonsekvent ögonblicksbild 1 timme.

Skapar site Recovery en kraschkonsekvent återställningspunkt var femte minut. Användaren kan inte ändra denna frekvens. Så för den senaste timmen, kommer användaren ha 12 kraschkonsekventa programkatalog återställningspunkter och 1 programkonsekvent om du vill välja från. Under tiden gång Site Recovery prunes alla återställningspunkter längre än den senaste timmen och sparar endast 1 recovery pekar per timme.

Följande skärmbild illustrerar exemplet. I skärmbilden:

1. För tid som är mindre än den senaste timmen, finns det återställningspunkter med en frekvens på 5 minuter.
2. För tiden efter den senaste timmen behåller Site endast 1 återställningspunkt.

   ![Lista över genererade återställningspunkter](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?
Den äldsta återställningspunkten som du kan använda är 72 timmar.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Vad händer om jag har en replikeringsprincip på 24 timmar och ett problem hindrar Site Recovery från skapar återställningspunkter för mer än 24 timmar? Kommer min tidigare återställningspunkterna att gå förlorade?
Nej, Site Recovery behålls alla tidigare återställningspunkter. Beroende på recovery punkter kvarhållningsperioden, ersätter 24 timmar i det här fallet Site Recovery äldsta återställningspunkt endast om det finns en generation av nya punkter. I det här fallet eftersom det inte finnas någon ny återställningspunkt som genererats på grund av vissa problem, förblir de gamla punkterna intakta när vi nå fönstret kvarhållning.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hur ändrar jag replikeringsprincipen när replikering har aktiverats på en virtuell dator?
Gå till **Site Recovery-valv** > **Site Recovery-infrastruktur** > **replikeringsprinciper**. Välj den princip som du vill redigera och spara ändringarna. Ändringar gäller för alla befintliga replikeringar för.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Är alla återställningspunkter som en fullständig kopia av den virtuella datorn eller en differentiell?
Den första återställningspunkten som skapas har fullständig kopia. Alla efterföljande återställningspunkter har deltaändringar.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Kan du öka lagringskostnaderna genom att öka kvarhållningsperioden för återställningspunkter?
Ja. Om du ökar kvarhållningsperioden från 24 timmar till 72 timmar, sparar Site Recovery återställningspunkterna för en ytterligare 48 timmar. Extra tid tillkommer lagringskostnader. Till exempel om en enda återställningspunkt har deltaändringar på 10 GB och kostnaden per GB är $0.16 per månad, blir ytterligare avgifter $1.6 * 48 per månad.

## <a name="multi-vm-consistency"></a>Multi-VM-konsekvens

### <a name="what-is-multi-vm-consistency"></a>Vad är konsekvens?
Det innebär att se till att återställningspunkten är konsekvent för alla replikerade virtuella datorer.
Site Recovery tillhandahåller ett alternativ för ”konsekvens”, som när du väljer det, skapar du en replikeringsgrupp för att replikera alla datorer tillsammans som ingår i gruppen.
Alla virtuella datorer har delade kraschkonsekventa och appkonsekventa återställningspunkter när de har redundansväxlats.
Gå igenom självstudiekursen och [aktivera konsekvens](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Kan jag redundans enskild virtuell dator i en replikeringsgrupp för Multi-VM-konsekvens?
Genom att välja alternativet ”konsekvens”, du talar om att programmet har ett beroende på alla virtuella datorer i en grupp. Därför är redundans för virtuell dator inte tillåtet.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hur många virtuella datorer som en del av en replikeringsgrupp för Multi-VM-konsekvens kan jag replikera?
Du kan replikera 16 virtuella datorer tillsammans i en replikeringsgrupp.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>När ska jag aktivera konsekvens?
Eftersom det är Processorintensiva, kan aktivering av konsekvens för flera datorer påverka arbetsbelastningens prestanda. Den bör endast användas om datorer kör samma arbetsbelastning och konsekvens mellan flera datorer. Om du har två instanser av SQL Server och två servrar i ett program kan ha du till exempel konsekvens för SQL Server-instanserna.


## <a name="failover"></a>Redundans

### <a name="how-is-capacity-guaranteed-in-target-region-for-azure-vms"></a>Hur kapacitet garanteras i målregionen för virtuella Azure-datorer?
Azure Site Recovery (ASR)-teamet arbetar med Azure-kapacitet management-teamet att planera för tillräckligt med infrastrukturkapacitet för, i ett försök att se till att virtuella datorer som skyddas av ASR för disaster recovery distribueras har i regionen disaster recovery (DR) När åtgärder för ASR-redundans initieras.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

Den sker inte automatiskt. Du startar redundansväxlingar med ett enda klick i portalen eller använda [PowerShell](azure-to-azure-powershell.md) att utlösa redundans.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Kan jag behålla en offentlig IP-adress efter redundans?

Offentliga IP-adressen för produktionsprogram *kan inte hållas kvar på redundans*. Arbetsbelastningar som visas som en del av redundansprocessen måste tilldelas en Azure offentlig IP-resurs som är tillgänglig i målregionen. Du kan göra det här steget manuellt eller automatisera via en återställningsplan. Om du vill tilldela en offentlig IP-adress med hjälp av en återställningsplan, se [konfigurera offentliga IP-adresser efter redundansväxling](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Kan jag behålla en privat IP-adress vid redundans?
Ja, kan du behålla en privat IP-adress. Som standard skapar Site Recovery target-resurser utifrån resurs-inställningar för datakälla när du aktiverar Haveriberedskap för virtuella Azure-datorer. Site Recovery försöker etablera samma IP-adress för den Virtuella, måldatorn om den inte används för Azure-datorer som har konfigurerats med statiska IP-adresser. Om du vill behålla privata IP-adressen under olika förhållanden, se [behålla IP-adresser under redundansväxlingen](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-the-server-doesnt-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-a-new-ip-address"></a>Servern har inte samma IP-adress som virtuella datorn efter redundansväxlingen. Varför är det tilldelade en ny IP-adress?

Site Recovery försöker ange IP-adress vid tidpunkten för redundans. Om en annan virtuell dator tar den här adressen kan anger Site Recovery nästa tillgängliga IP-adressen som mål.
En fullständig förklaring på hur Site Recovery hanterar adressering Se [konfigurera nätverksmappning och IP-adresser för virtuella nätverk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Vad är **senaste (lägsta Återställningspunktmål)** återställningspunkter?
Den **senaste (lägsta Återställningspunktmål)** alternativet bearbetar först alla data som har skickats till Site Recovery-tjänsten för att skapa en återställningspunkt för varje virtuell dator innan växling till den. Det här alternativet ger lägsta mål för återställningspunkt (RPO), eftersom den virtuella datorn skapas efter att redundansen har fått alla data som replikeras till Site Recovery när redundansen utlöstes.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Gör **senaste (lägsta Återställningspunktmål)** återställningspunkter kan påverka redundans RTO?
Ja. Site Recovery bearbetar alla data som väntar innan du växlar över, så det här alternativet har en högre återställningstid (RTO) jämfört med andra alternativ.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Vad gör den **senaste bearbetade** alternativet i recovery pekar medelvärdet?
Den **bearbetades senast** alternativet växlar över alla virtuella datorer i planerna på att den senaste återställningspunkten peka den Site Recovery som bearbetas. Om du vill se den senaste återställningspunkten för en specifik virtuell dator, kontrollera **senaste återställningspunkter** i inställningarna för virtuella datorer. Det här alternativet ger ett lågt RTO eftersom läggs ingen tid på bearbetning av data.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Om jag replikerar mellan två Azure-regioner, vad händer om min primära region uppstår ett oväntat avbrott?
Du kan utlösa redundans efter avbrottet. Site Recovery behöver ingen anslutning från den primära regionen att utföra redundansväxlingen.

### <a name="what-is-a-rto-of-a-virtual-machine-failover-"></a>Vad är en RTO av redundans för en virtuell dator?
Site Recovery har en [RTO serviceavtalet för 2 timmar](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Men växla i de flesta fall, Site Recovery över virtuella datorer på några minuter. Du kan beräkna RTO genom att gå till redundans jobb som visar tid det tog för att ta fram den virtuella datorn. Planera RTO för återställning, finns under avsnitt.

## <a name="recovery-plans"></a>Återställningsplaner

### <a name="what-is-a-recovery-plan"></a>Vad är en återställningsplan?
En återställningsplan i Site Recovery samordnar redundansåterställning av virtuella datorer. Det hjälper till att göra återställningen konsekvent korrekt, upprepningsbara och automatiserade. En återställningsplan löser följande behoven för användaren:

- Ange en grupp med virtuella datorer som redundansväxlar tillsammans
- Definiera beroenden mellan virtuella datorer så att programmet visas korrekt
- Automatisera återställning tillsammans med anpassade manuella åtgärder du utför uppgifter än redundansväxling för virtuella datorer

[Läs mer](site-recovery-create-recovery-plans.md) om återställningsplaner.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hur uppnås ordningsföljd i en återställningsplan?

Du kan skapa flera grupper för att uppnå ordningsföljd i en återställningsplan. Varje grupp växlas över i taget. Virtuella datorer som ingår i samma grupp misslyckas över tillsammans, följt av en annan grupp. Om du vill lära dig mer om att utforma ett program med hjälp av en återställningsplan, se [om återställningsplaner](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hur hittar jag RTO av en återställningsplan?
Om du vill kontrollera RTO av en återställningsplan gör ett redundanstest för återställningsplanen och gå till **Site Recovery-jobb**.
I följande exempel tog det jobb som heter SAPTestRecoveryPlan 8 minuter och 59 sekunder att redundansväxla alla virtuella datorer och utföra angivna åtgärder.

![Lista över Site Recovery-jobb](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kan jag lägga till automation-runbooks i en återställningsplan?
Ja, kan du integrera Azure Automation-runbooks i din plan. [Läs mer](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Återaktivering av skydd och återställning efter fel

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Efter en redundansväxling från den primära regionen till en disaster recovery region finns virtuella datorer i en DR-regionen som skyddas automatiskt?
Nej. När du [redundansväxla](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) virtuella Azure-datorer från en region till en annan de virtuella datorerna startas i regionen för Haveriberedskap oskyddad. Om du vill återställa dit de virtuella datorerna till den primära regionen, måste du [återaktivera skyddet av](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) de virtuella datorerna i den sekundära regionen.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Vid tidpunkten för återaktivering av skydd, Site Recovery replikerar kompletta data från den sekundära regionen till den primära regionen?
Det beror på situationen. Till exempel om källregionen VM finns synkroniseras endast ändringar mellan källdisken och måldisken. Site Recovery beräknar skillnader genom att jämföra diskarna och överför den sedan data. Den här processen tar vanligtvis några timmar. Läs mer om vad som händer vid återaktiveringen av skyddet, [återaktivering av skydd redundansväxlade virtuella Azure-datorer till den primära regionen]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Hur mycket tid har det tar att återställa efter felet?
När återaktiveringen av skyddet liknar tiden för återställning efter fel vanligtvis tid för redundans från den primära regionen till en sekundär region.

## <a name="capacity"></a>Kapacitet

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Hur kan kapacitet garanteras i målregionen för virtuella Azure-datorer?
Azure Site Recovery (ASR)-teamet arbetar med Azure-kapacitet management-teamet att planera för tillräckligt med infrastrukturkapacitet för, i ett försök att se till att virtuella datorer som skyddas av ASR för disaster recovery distribueras har i regionen disaster recovery (DR) När åtgärder för ASR-redundans initieras.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Fungerar Site Recovery med reserverade instanser?
Ja, du kan köpa [reservera instanser](https://azure.microsoft.com/pricing/reserved-vm-instances/) i DR region och ASR redundans åtgärder använder dem för. </br> Det krävs ingen ytterligare konfiguration från kunderna.


## <a name="security"></a>Säkerhet
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?
Nej, Site Recovery komma åt inte replikerade data och den har inte någon information om vad som körs på dina virtuella datorer. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  
Site Recovery är ISO 27001: 2013, 27018, HIPAA, DPA certifierade och håller på att SOC2 och FedRAMP JAB-utvärderingar.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
Ja, både kryptering under överföring och [kryptering i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.

## <a name="next-steps"></a>Nästa steg
* [Granska](azure-to-azure-support-matrix.md) supportkrav.
* [Konfigurera](azure-to-azure-tutorial-enable-replication.md) replikering från Azure till Azure.
