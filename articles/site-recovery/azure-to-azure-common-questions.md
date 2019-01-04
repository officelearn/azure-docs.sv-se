---
title: Vanliga frågor – Azure till Azure-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln sammanfattas vanliga frågor när du konfigurerar haveriberedskap för virtuella Azure-datorer till aonther med hjälp av Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969955"
---
# <a name="common-questions---azure-to-azure-replication"></a>Vanliga frågor – Azure till Azure replikering

Den här artikeln innehåller svar på vanliga frågor som vi kan se när du distribuerar haveriberedskap för virtuella Azure-datorer till en annan Azure-region. Om du har frågor när du har läst den här artikeln kan publicera dem på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Allmänt
### <a name="how-is-site-recovery-priced"></a>Hur prissätts Site Recovery?
Granska [priserna för Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) information.

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Så här konfigurerar du Site Recovery på Azure Virtual Machines. Vad är bästa praxis?
1. [Förstå Azure till Azure-arkitektur](azure-to-azure-architecture.md)
2. [Granska konfigurationerna som stöds och stöds inte](azure-to-azure-support-matrix.md)
3. [Konfigurera haveriberedskap för virtuella Azure-datorer](azure-to-azure-how-to-enable-replication.md)
4. [Kör ett redundanstest](azure-to-azure-tutorial-dr-drill.md)
5. [Redundans och återställning efter fel till primär region](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replikering

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>Kan jag replikera Azure disk kryptering aktiverat virtuella datorer?
Ja, kan du replikera dem. Se [artikeln](azure-to-azure-how-to-enable-replication-ade-vms.md) att aktivera replikering av Azure-diskkryptering (ADE) aktiverade virtuella datorer. Observera att endast virtuella Azure-datorer som kör Windows OS och aktiverad för kryptering med Azure AD-app för närvarande stöds av Azure Site Recovery.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan jag replikera virtuella datorer till en annan prenumeration?
Ja, kan du replikera virtuella Azure-datorer till en annan prenumeration med i samma Azure Active Directory-klient.
Konfigurera DR [mellan prenumerationer](https://azure.microsoft.com/blog/cross-subscription-dr) är enkel. Du kan välja en annan prenumeration vid tidpunkten för replikering.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Jag kan replikera zonlåsta virtuella Azure-datorer till en annan region.
Ja, du kan [replikera zonlåsta datorer](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) till en annan region.

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?

Ja, kan du utesluta diskar vid tidpunkten för skydd med hjälp av PowerShell. Se [powershell-riktlinjer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) att undanta diskar

###<a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?
Replikeringen är kontinuerlig vid replikering av virtuella Azure-datorer till en annan Azure-region. Kontrollera den [Azure till Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) replikeringsarkitektur att förstå informationen.

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>Kan jag replikera virtuella datorer i samma region? Jag behöver detta för att migrera virtuella datorer?
Du kan inte använda Azure till Azure DR-lösning för att replikera virtuella datorer i samma region.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Kan jag replikera virtuella datorer till alla Azure-regioner?
Med Site Recovery kan du replikera och återställa virtuella datorer mellan alla två regioner inom samma geografiska kluster. Geografisk kluster definieras att hålla datafördröjning och datasuveränitet i åtanke. Mer information finns i Site Recovery [region stöd matrix](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Kräver Internetanslutning i Site Recovery?

Nej, Site Recovery kräver inte ansluten till internet men åtkomsten till Site Recovery-webbadresser och IP-intervall som anges i detta [artikel](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

## <a name="replication-policy"></a>Replikeringsprincip

### <a name="what-is-a-replication-policy"></a>Vad är en replikeringsprincip?
Den definierar inställningarna för recovery point kvarhållning och frekvensen för programkonsekventa ögonblicksbilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningarna för ”24 timmars för kvarhållning av återställningspunkt och” 60 minuters för frekvens för appkonsekvent ögonblicksbild.

### <a name="what-is-crash-consistent-recovery-point"></a>Vad är kraschkonsekvent återställningspunkt?
Kraschkonsekvent återställningspunkt representerar data på disken som om den virtuella datorn har kraschat eller kontakten hämtades från servern när ögonblicksbilden togs. De omfattar inte något som fanns i minnet när ögonblicksbilden togs. Idag är kan de flesta program återställa från krascher programkonsekventa ögonblicksbilder. En kraschkonsekvent återställningspunkt är vanligtvis tillräckligt för ingen databas operativsystem och program, t.ex filservrar, DHCP-servrar, utskriftsservrar och så vidare.

### <a name="what-is-application-consistent-recovery-point"></a>Vad är programkonsekvent återställningspunkt? 
Programkonsekventa återställningspunkter skapas från appkonsekventa ögonblicksbilder som samlar in samma data som kraschkonsekventa ögonblicksbilder, och Lägg till alla data i minnet och alla pågående transaktioner. På grund av deras extra innehåll programkonsekventa ögonblicksbilder är den vanligaste och tar den längsta för att utföra. Programkonsekventa återställningspunkter rekommenderas för databas-operativsystem och program, till exempel SQL.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hur återställningspunkter genereras och sparas?
För att förstå kan hur Site Recovery genererar återställningspunkter ta ett exempel på den replikeringsprincip som har återställningspunkten kvarhållningsperiod på 24 timmar och App frekvens ögonblicksbild av 1 timme.
Krascher konsekvent återställningspunkt varje 5 minuter och användaren inte har någon kontroll för att ändra den här ofta skapar site Recovery. Därför under senaste har timslångt användaren 12 kraschkonsekvent punkter och 1 app konsekvent återställningspunkt att välja bland. Under tiden gång Site Recovery prunes alla återställningspunkter utöver senaste timmen för att endast spara ett recovery peka per timme.
Illustration på skärmbilden nedan:


1. Det finns återställningspunkter med frekvens på 5 minuter för tid mindre än senaste timmen.
2. Vi kan se att endast en återställningspunkt per timme förblir gången utöver senaste timmen.

  ![Recovery generations-poäng](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?
Den äldsta återställningspunkten som du kan använda är 72 timmar.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>Vad händer om jag har en replikeringsprincip på 24 timmar och det finns inga recovery punkter genereringen av på grund av vissa problem för mer än 24 timmar. Fungerar min tidigare recovery punkter rensas?
Nej, Site Recovery kommer att behålla dina alla tidigare återställningspunkter i det här fallet. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hur ändrar jag replikeringsprincipen när replikering har aktiverats på en virtuell dator? 
Gå till Site Recovery-valv > Site Recovery-infrastruktur > replikeringsprinciper. Välj princip som du vill redigera och spara ändringarna. Ändringar gäller för alla befintliga replikeringar för. 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>Är alla återställningspunkter fullständig kopia av den virtuella datorn eller differentiell?
Vid inledande replikering den första återställningspunkten som hämtar genererat har fullständig kopia och alla efterföljande återställningspunkter har deltaändringar.

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>Kan du ökar den lagringskostnad genom att öka recovery punkter kvarhållningsfönstret?
Ja, om du ökar kvarhållningsperioden från 24 timmar till 72 timmar och sedan Site Recovery sparar återställningspunkterna för tillägg 48 timmar som tillkommer du lagringskostnader. Till exempel om en enda återställningspunkt har deltaändringar på 10 GB och per GB kostnaden är $0.16 per månad, och sedan det skulle vara $1.6 * 48 ytterligare kostnader per månad.

## <a name="failover"></a>Redundans

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

Den sker inte automatiskt. Du startar redundansväxlingar med ett enda klick i portalen eller använda Site Recovery [PowerShell](azure-to-azure-powershell.md) att utlösa redundans. 

### <a name="can-i-retain-public-ip-address-after-failover"></a>Kan jag behålla offentlig IP-adress efter redundans?

Offentliga IP-adressen för produktionsprogram **kan inte hållas kvar på redundans**. Arbetsbelastningar som visas som en del av redundans måste tilldelas en resurs för Azure offentlig IP-adress som är tillgängliga i målregionen. Det här steget kan göras antingen manuellt eller automatiserade med återställningsplaner. Se [artikeln](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan) tilldela offentliga IP-adress med hjälp av Återställningsplanen.  

### <a name="can-i-retain-private-ip-address-during-failover"></a>Kan jag behålla privat IP-adress vid redundans?
Ja, kan du behålla privat IP-adress. Som standard skapar Site Recovery target-resurser utifrån resurs-inställningar för datakälla när du aktiverar haveriberedskap för virtuella Azure-datorer. Site Recovery försöker etablera samma IP-adress för den Virtuella, måldatorn om den inte används för Azure-datorer som har konfigurerats med statiska IP-adresser. Se [artikeln](site-recovery-retain-ip-azure-vm-failover.md) att behålla privata IP-adressen under olika förhållanden.

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>Efter redundansväxlingen har servern inte samma IP-adress som virtuella datorn. Varför tilldelas den med en ny IP-adress?

Site Recovery försöker ange IP-adressen efter bästa förmåga vid tidpunkten för redundans. Om det som hämtas av en virtuell dator, har nästa tillgängliga IP-adress angetts som mål. En fullständig förklaring på hur Site Recovery hanterar adressering, [läsa den här artikeln.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms)

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>Vad gör senaste (lägsta Återställningspunktmål) recovery pekar sätt?
Det här alternativet bearbetar först alla data som har skickats till Site Recovery-tjänsten för att skapa en återställningspunkt för varje virtuell dator innan växling till den. Det här alternativet ger den lägsta RPO (mål för återställningspunkt), eftersom den virtuella datorn skapas efter redundans har alla data som replikeras till Site Recovery när redundansen utlöstes.

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>Har senaste (lägsta Återställningspunktmål) återställningspunkter påverkan på Failover RTO?
Ja, eftersom Site Recovery kommer att bearbeta alla data som väntar innan inte körs, har högre RTO jämfört med andra i det här alternativet.

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>Vad gör senaste bearbetade alternativ i recovery punkter medelvärdet?
Det här alternativet redundansväxlar alla virtuella datorer i planerna på att den senaste återställningspunkten som bearbetats av Site Recovery. Om du vill se den senaste återställningspunkten för en specifik virtuell dator, kontrollera senaste återställningspunkter i inställningarna för virtuella datorer. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Om jag replikerar mellan två Azure-regioner vad händer om min primära region uppstår ett oväntat avbrott?
Du kan utlösa redundans efter avbrottet. Site Recovery behöver ingen anslutning från den primära regionen att utföra redundansväxlingen.

## <a name="recovery-plan"></a>Återställningsplan

### <a name="what-is-a-recovery-plan-"></a>Vad är en Återställningsplanen?
Återställningsplaner i Site Recovery dirigera redundansåterställning av virtuella datorer. Det hjälper till att göra återställningen konsekvent korrekt, upprepningsbara och automatiserade. En återställningsplan löser följande behoven för användaren:

- Ange en grupp med virtuella datorer tillsammans att redundansväxlingen.
- Definiera beroenden mellan de virtuella datorerna så att programmet visas korrekt.
- Automatisera återställning tillsammans med anpassade manuella åtgärder, så att andra aktiviteter än redundans för virtuella datorer kan även uppnås.

[Läs mer](site-recovery-create-recovery-plans.md) om återställningsplaner.

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>Hur gör sekvensering uppnås i en Återställningsplanen?

I Återställningsplanen, kan du skapa flera grupper för att uppnå ordningsföljd. Varje grupp-redundans på en gång, vilket innebär att virtuella datorer som ingår i samma grupp redundansväxlas tillsammans följt av en annan grupp. Kontrollera hur du [modellen program med hjälp av återställningsplanen.](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps) 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hur hittar jag RTO av en återställningsplan?
Om du vill kontrollera RTO av en återställningsplan redundanstest återställningsplanen och gå till Site Recovery-jobb.
Enligt nedan, SAP testplan för återställning till exempel tog 8 minuter 59 sekunder att redundansväxla alla virtuella datorer från och utföra några åtgärder som har angetts.

  ![COM-fel](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kan jag lägga till automation-runbooks i Återställningsplanen?
Ja, kan du integrera Azure automation-runbooks i din plan. [Läs mer](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>Återaktivering av skydd och återställning efter fel 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>När du gör en redundansväxling från den primära regionen till disaster recovery region har virtuella datorer i en DR-regionen skyddas automatiskt?
Nej, när du [redundans](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) Azure virtuella datorer från en region till en annan, virtuella datorer som startas i regionen för Haveriberedskap oskyddad. Om du vill återställa dit de virtuella datorerna till den primära regionen, måste du [återaktivera skyddet av](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) de virtuella datorerna i den sekundära regionen.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>Vid tidpunkten för återaktivering av skydd Site Recovery replikerar kompletta data från sekundär region till primär region?
Det beror på situationen, till exempel om källregionen VM finns och sedan synkroniseras endast ändringar mellan källdisken och måldisken. Skillnader i beräknas genom att jämföra båda diskarna och sedan överföras. Detta tar vanligtvis några timmar att slutföra. Se [artikeln]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) att få mer detaljerad information om vad som händer vid återaktiveringen av skyddet.

### <a name="how-much-time-does-it-take-to-failback"></a>Hur mycket tid har det tar att återställning efter fel?
När återaktiveringen av skyddet har är vanligtvis det tidsperiod som liknar redundans från den primära regionen till sekundär region. 

## <a name="security"></a>Säkerhet
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?
Nej, Site Recovery komma åt inte replikerade data och har inte någon information om vad som körs på dina virtuella datorer. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  
Site Recovery är ISO 27001: 2013, 27018, HIPAA, DPA certifierade och håller på att SOC2 och FedRAMP JAB-utvärderingar.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
Ja, både kryptering under överföring och [kryptering i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.

## <a name="next-steps"></a>Nästa steg
* [Granska](azure-to-azure-support-matrix.md) supportkrav.
* [Konfigurera](azure-to-azure-tutorial-enable-replication.md) replikering från Azure till Azure.
