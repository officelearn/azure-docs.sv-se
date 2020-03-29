---
title: Azure Instant Restore-funktion
description: Azure Instant Restore Capability och vanliga frågor för vm-säkerhetskopieringsstack, Resurshanterarens distributionsmodell
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7cf437c6b20ea6b688e8e93e401cf71ef0260888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705436"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Få bättre prestanda för säkerhetskopiering och återställning med Azure Backup Instant Restore

> [!NOTE]
> Baserat på feedback från användare byter vi namn på **VM-säkerhetskopieringsstack V2** till **Omedelbar återställning** för att minska förväxlingen med Azure Stack-funktioner.
> Alla Azure-säkerhetskopieringsanvändare har nu uppgraderats till **Omedelbar återställning**.

Den nya modellen för Instant Restore innehåller följande funktionsförbättringar:

* Möjlighet att använda ögonblicksbilder som tagits som en del av ett säkerhetskopieringsjobb som är tillgängligt för återställning utan att vänta på att dataöverföringen till valvet ska slutföras. Det minskar väntetiden för ögonblicksbilder att kopiera till valvet innan du utlöser återställning.
* Minskar säkerhetskopierings- och återställningstider genom att behålla ögonblicksbilder lokalt, i två dagar som standard. Det här standardvärdet för kvarhållning av ögonblicksbilder kan konfigureras till valfritt värde mellan 1 till 5 dagar.
* Stöder diskstorlekar på upp till 32 TB. Storleksändring av diskar rekommenderas inte av Azure Backup.
* Stöder standard-SSD-diskar tillsammans med vanliga hårddiskar och Premium SSD-diskar.
* Möjlighet att använda en ohanterat virtuella datorer ursprungliga lagringskonton (per disk), när du återställer. Den här möjligheten finns även när den virtuella datorn har diskar som distribueras över lagringskonton. Det snabbar upp återställningsåtgärder för en mängd olika VM-konfigurationer.
* För säkerhetskopiering av virtuella datorer som använder premiumlagring, med Omedelbar återställning, rekommenderar vi att du allokerar *50 %* ledigt utrymme för det totala tilldelade lagringsutrymmet, vilket **endast** krävs för den första säkerhetskopian. Det 50 % lediga utrymmet är inte ett krav för säkerhetskopior när den första säkerhetskopieringen är klar.

## <a name="whats-new-in-this-feature"></a>Nyheter i den här funktionen

För närvarande består säkerhetskopieringsjobbet av två faser:

1. Ta en ögonblicksbild av en virtuell dator.
2. Överföra en ögonblicksbild av en virtuell dator till Azure Recovery Services-valvet.

En återställningspunkt anses skapas först efter faserna 1 och 2 har slutförts. Som en del av den här uppgraderingen skapas en återställningspunkt så snart ögonblicksbilden är klar och den här återställningspunkten av ögonblicksbildtyp kan användas för att utföra en återställning med samma återställningsflöde. Du kan identifiera den här återställningspunkten i Azure-portalen med hjälp av "snapshot" som återställningspunkttyp, och när ögonblicksbilden har överförts till valvet ändras återställningspunkttypen till "ögonblicksbild och valv".

![Säkerhetskopieringsjobb i VM-säkerhetskopieringsstack Resource Manager-distributionsmodell – lagring och valv](./media/backup-azure-vms/instant-rp-flow.png)

Som standard behålls ögonblicksbilder i två dagar. Den här funktionen gör det möjligt att återställa åtgärder från dessa ögonblicksbilder där genom att minska återställningstiderna. Det minskar den tid som krävs för att omvandla och kopiera data tillbaka från valvet.

## <a name="feature-considerations"></a>Funktionsöverväganden

* Ögonblicksbilder lagras tillsammans med diskarna för att öka skapandet av återställningspunkter och för att påskynda återställningsåtgärder. Därför ser du lagringskostnader som motsvarar ögonblicksbilder som tagits under den här perioden.
* Inkrementella ögonblicksbilder lagras som sidblobar. Alla användare som använder ohanterat diskar debiteras för ögonblicksbilder som lagras i deras lokala lagringskonto. Eftersom återställningspunktsamlingarna som används av Säkerhetskopierade virtuella datorer använder blob-ögonblicksbilder på den underliggande lagringsnivån, ser du kostnader som motsvarar blob-ögonblicksbildprissättning och de är inkrementella.
* För premiumlagringskonton räknas ögonblicksbilderna som tas för omedelbara återställningspunkter mot gränsen på 10 TB för allokerat utrymme.
* Du får en möjlighet att konfigurera bevarandet av ögonblicksbilder baserat på återställningsbehoven. Beroende på behovet kan du ställa in lagringen av ögonblicksbilder till minst en dag i bladet för säkerhetskopieringsprincip enligt beskrivningen nedan. Detta hjälper dig att spara kostnader för ögonblicksbildkvarhållning om du inte utför återställningar ofta.
* Det är en en riktad uppgradering, en gång uppgraderas till Omedelbar återställning, kan du inte gå tillbaka.

>[!NOTE]
>Med den här omedelbar återställningsuppgraderingen ställs lagringstiden för ögonblicksbilder för alla kunder **(nya och befintliga båda inkluderade)** in på ett standardvärde på två dagar. Du kan dock ställa in varaktigheten enligt ditt krav på valfritt värde mellan 1 till 5 dagar.

## <a name="cost-impact"></a>Kostnadspåverkan

De inkrementella ögonblicksbilderna lagras i den virtuella datorns lagringskonto, som används för omedelbar återställning. Inkrementell ögonblicksbild innebär att det utrymme som upptas av en ögonblicksbild är lika med det utrymme som upptas av sidor som skrivs efter att ögonblicksbilden skapades. Fakturering är fortfarande för det per GB-använda utrymmet som upptas av ögonblicksbilden, och priset per GB är detsamma som på [prissidan](https://azure.microsoft.com/pricing/details/managed-disks/). För virtuella datorer som använder ohanterat diskar kan ögonblicksbilderna ses på menyn för VHD-filen för varje disk. För hanterade diskar lagras ögonblicksbilder i en återställningsplatssamlingsresurs i en angiven resursgrupp och själva ögonblicksbilderna är inte direkt synliga.

>[!NOTE]
> Ögonblicksbild kvarhållning är fast till 5 dagar för veckovisa principer.

## <a name="configure-snapshot-retention"></a>Konfigurera kvarhållning av ögonblicksbilder

### <a name="using-azure-portal"></a>Använda Azure Portal

I Azure-portalen kan du se ett fält som lagts till i bladet **Säkerhetskopiering av virtuella datorer** under avsnittet **Omedelbar återställning.** Du kan ändra lagringstiden för ögonblicksbilder från bladet **Säkerhetskopiering** av virtuella datorer för alla virtuella datorer som är associerade med den specifika säkerhetskopieringsprincipen.

![Möjlighet till omedelbar återställning](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Använda PowerShell

>[!NOTE]
> Från Az PowerShell version 1.6.0 och framåt kan du uppdatera lagringsperioden för ögonblicksbilder för omedelbar återställning i principen med PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Standardlagring av ögonblicksbilder för varje princip är inställd på två dagar. Användaren kan ändra värdet till minst 1 och högst fem dagar. För veckovisa principer är bevarandet av ögonblicksbilden fast till fem dagar.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Vilka är kostnaderna konsekvenserna av Omedelbar återställning?

Ögonblicksbilder lagras tillsammans med diskarna för att påskynda skapandet av återställningspunkter och återställningsåtgärder. Därför ser du lagringskostnader som motsvarar den ögonblicksbildkvarhållning som valts som en del av principen för säkerhetskopiering av virtuella datorer.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Upptar ögonblicksbilderna som tagits för omedelbar återställningspunkt på 10 TB i Premium Storage-konton?

Ja, för premiumlagringskonton upptar ögonblicksbilder som tagits för omedelbar återställningspunkt 10 TB allokerat ögonblicksbildutrymme.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hur fungerar lagring av ögonblicksbilder under femdagarsperioden?

Varje dag tas en ny ögonblicksbild, sedan finns det fem enskilda inkrementella ögonblicksbilder. Storleken på ögonblicksbilden beror på dataomsättningen, som i de flesta fall ligger runt 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Är en ögonblicksbild av omedelbar återställning en inkrementell ögonblicksbild eller fullständig ögonblicksbild?

Ögonblicksbilder som tas som en del av direkt återställning är inkrementella ögonblicksbilder.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hur beräknar jag den ungefärliga kostnadsökningen på grund av funktionen för omedelbar återställning?

Det beror på den virtuella datorns omsättning. I ett stabilt tillstånd kan du anta att kostnadsökningen är = Ögonblicksbildkvarhållningsperiod daglig omsättning per vm-lagringskostnad per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Om återställningstypen för en återställningspunkt är "Snapshot and vault" och jag utför en återställningsåtgärd, vilken återställningstyp som ska användas?

Om återställningstypen är "ögonblicksbild och valv", kommer återställningen automatiskt att göras från den lokala ögonblicksbilden, vilket kommer att vara mycket snabbare jämfört med återställningen som gjorts från valvet.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Vad händer om jag väljer kvarhållningsperiod för återställningspunkt (Nivå 2) mindre än lagringsperioden för ögonblicksbilder (nivå 1)?

Den nya modellen tillåter inte att ta bort återställningspunkten (Nivå 2) om inte ögonblicksbilden (nivå 1) tas bort. Vi rekommenderar schemaläggning återställningspunkt (Nivå2) kvarhållningsperiod större än ögonblicksbildkvarhållningsperioden.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Varför finns min ögonblicksbild även efter den inställda kvarhållningsperioden i säkerhetskopieringsprincipen?

Om återställningspunkten har ögonblicksbild och det är den senaste RP tillgängliga, behålls den tills den tid det finns en nästa lyckad säkerhetskopiering. Detta är enligt den utformade "skräpinsamling" (GC) politik idag som mandat minst en senaste RP att alltid vara närvarande om alla säkerhetskopior ytterligare misslyckas på grund av ett problem i den virtuella datorn. I normala scenarier rensas RPs upp högst 24 timmar efter att de har upphört att gälla.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Jag behöver inte direktåterställningsfunktioner. Kan det inaktiveras?

Funktionen För omedelbar återställning är aktiverad för alla och kan inte inaktiveras. Du kan minska bevarandet av ögonblicksbilder till minst en dag.

>[!NOTE]
> **Azure Backup stöder nu selektiv säkerhetskopiering och återställning av disk med hjälp av azure virtual machine-säkerhetskopieringslösningen.**
>
>Idag stöder Azure Backup säkerhetskopiering av alla diskar (operativsystem och data) i en virtuell dator tillsammans med hjälp av lösningen för säkerhetskopiering av virtuella datorer. Med exkluderingsdiskfunktioner får du möjlighet att säkerhetskopiera en eller några från de många datadiskarna i en virtuell dator. Detta ger en effektiv och kostnadseffektiv lösning för dina säkerhetskopierings- och återställningsbehov. Varje återställningspunkt innehåller data för diskarna som ingår i säkerhetskopieringen, vilket gör att du ytterligare kan få en delmängd diskar återställda från den angivna återställningspunkten under återställningen. Detta gäller för att återställa både från ögonblicksbilden och valvet.
>
>**För att registrera dig för förhandsgranskningen, skriv till oss påAskAzureBackupTeam@microsoft.com**
