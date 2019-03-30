---
title: Azure för omedelbar återställning
description: Säkerhetskopiera stack Resource Manager-distributionsmodellen Azure omedelbar återställning funktions- och vanliga frågor och svar för virtuell dator
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: sogup
ms.openlocfilehash: 1f96c47e993e9b3d123972aba8eefc54b1d5cdfa
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652679"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Få förbättrad säkerhetskopian och återställer prestanda med Azure Backup-omedelbar återställning kapacitet

> [!NOTE]
> Baserat på feedback från användare byter vi namn **VM-säkerhetskopieringsstack V2** till **omedelbar återställning** att minska förvirring med Azure Stack-funktioner.<br/><br/> Alla Azure backup användare har nu uppgraderats till **omedelbar återställning**.

Den nya modellen för omedelbar återställning innehåller följande funktionsförbättringar:

* Möjlighet att använda ögonblicksbilder som tas som en del av ett säkerhetskopieringsjobb som är tillgängliga för återställning utan att behöva vänta på att överföra data till valvet för att slutföra. Det minskar väntetiden för ögonblicksbilder att kopiera till valvet innan återställningen.
* Minskar tider för säkerhetskopiering och återställning genom att behålla ögonblicksbilder lokalt, i två dagar som standard. Det här standard-valvet kan konfigureras till ett värde mellan 1 till 5 dagar.
* Stöder disk storlekar upp till 4 TB.
* Stöder Standard SSD-diskar tillsammans med Standard HDD-diskar och Premium SSD-diskar.
*   Möjligheten att använda en ohanterad virtuell dator ursprungliga lagringskonton (per disk) när du återställer. Denna möjlighet finns även när den virtuella datorn har diskar som är fördelade på storage-konton. Det går snabbare återställningsåtgärder för en mängd olika VM-konfigurationer.


## <a name="whats-new-in-this-feature"></a>Vad är nytt i den här funktionen

För närvarande består säkerhetskopieringsjobbet av två faser:

1.  Ta en ögonblicksbild för virtuell dator.
2.  Överföra en VM-ögonblicksbild till Azure Recovery Services-valvet.

En återställningspunkt anses skapas endast efter faser 1 och 2 har slutförts. En återställningspunkt skapas som en del av den här uppgraderingen när ögonblicksbilden har slutförts och den här återställningspunkten för typ av ögonblicksbild som kan användas för att utföra en återställning med hjälp av samma flöde för återställning. Du kan identifiera den här återställningspunkten i Azure portal genom att använda ”ögonblicksbild” som typ av återställning och när ögonblicksbilden överförs till valvet, typ av återställning ändras till ”ögonblicksbild och valv”.

![Säkerhetskopieringsjobbet på VM säkerhetskopieringsstack distributionsmodellen Resource Manager – lagring och valv](./media/backup-azure-vms/instant-rp-flow.png)

Som standard behålls ögonblicksbilder i två dagar. Den här funktionen kan återställningen från de här ögonblicksbilderna det genom att stopptider återställning. Det minskar den tid som krävs för att omvandla och kopiera data från valvet.

## <a name="feature-considerations"></a>Överväganden för funktionernas

* Ögonblicksbilder lagras tillsammans med diskarna att öka skapa en återställningspunkt och påskynda återställningsåtgärder. Därför visas lagringskostnader för ögonblicksbilder som tas under denna period.
* Inkrementella ögonblicksbilder lagras som sidblobar. Alla användare som använder ohanterade diskar debiteras för ögonblicksbilder som lagras i kontot Lokal lagring. Eftersom återställningspunkt samlingar som används av säkerhetskopieringar för hanterade virtuella datorn använder blob-ögonblicksbilder på underliggande lagringsnivå, hanterade diskar visas kostnaderna som motsvarar blob-ögonblicksbild priser och de är inkrementell.
* Ögonblicksbilder som tas för omedelbar återställningspunkter count mot gränsen på 10 TB allokerat utrymme för premium storage-konton.
* Du får en möjlighet att konfigurera den ögonblicksbild kvarhållning av säkerhetskopior baserat på dina behov för återställning. Beroende på krav, kan du ange ögonblicksbild kvarhållning till minst en dag i bladet säkerhetskopieringspolicy som beskrivs nedan. Detta kan hjälpa dig minska kostnaderna för ögonblicksbild kvarhållning om du inte utföra återställningar ofta.
* Det här är en uppgradering för en riktad, en gång uppgraderas till omedelbar återställning, du kan inte gå tillbaka.

>[!NOTE]
>Med den här snabbmeddelanden återställa uppgradering ögonblicksbild kvarhållningsvaraktighetens för alla kunder (**nya och befintliga ingår både**) anges till ett standardvärde på två dagar. Du kan dock ange varaktighet enligt dina krav på att ett värde mellan 1 till 5 dagar.

## <a name="cost-impact"></a>Kostnad påverkan

Inkrementella ögonblicksbilder lagras i Virtuella datorns lagringskonto som används för omedelbar återställning. Inkrementell ögonblicksbild innebär det utrymme som används av en ögonblicksbild är lika med det utrymme som används av sidor som har skrivits när ögonblicksbilden har skapats. Faktureringen sker fortfarande för den per GB använt utrymme som används av ögonblicksbilden och pris per GB är samma som de som nämns i den [prissättningssidan](https://azure.microsoft.com/pricing/details/managed-disks/).

>[!NOTE]
> Ögonblicksbild kvarhållning har åtgärdats till 5 dagar för principer för varje vecka.

## <a name="configure-snapshot-retention"></a>Konfigurera kvarhållning av ögonblicksbild

### <a name="using-azure-portal"></a>Använda Azure Portal

I Azure-portalen kan du se ett fält har lagts till i den **VM Backup-principen** bladet under den **omedelbar återställning** avsnittet. Du kan ändra kvarhållningsvaraktighetens ögonblicksbild från den **VM säkerhetskopieringsprincip** bladet för alla virtuella datorer som är associerade med principen för specifika säkerhetskopiering.

![Kapaciteten för omedelbar återställning](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Använda PowerShell

>[!NOTE]
> Från Az PowerShell version 1.6.0 eller senare och senare, kan du uppdatera kvarhållningsperioden för omedelbar återställning ögonblicksbild i principen med hjälp av PowerShell

```powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```
Standard ögonblicksbild kvarhållning för varje princip har angetts till 2 dagar. Användaren kan ändra värdet till minst 1 och högst fem dagar. För varje vecka principer har ögonblicksbild kvarhållning åtgärdats till 5 dagar.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Vad är kostnaden effekterna av omedelbar återställning?
Ögonblicksbilder lagras tillsammans med diskar som snabbare att skapa en återställningspunkt och återställning. Därför visas lagringskostnader som motsvarar den ögonblicksbild kvarhållningsperioden som valts som en del av principen för säkerhetskopiering av virtuell dator.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>I Premium Storage-konton ögonblicksbilder som tas för omedelbar återställningspunkt uppta 10 TB-gränsen för ögonblicksbilder?
Ja, för premium storage-konton till ögonblicksbilder som tagits för omedelbar återställningspunkt tar upp 10 TB utrymme för allokerade ögonblicksbild.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hur fungerar ögonblicksbild kvarhållning under femdagars-period?
Varje dag en ny ögonblicksbild tas, så det finns fem enskilda inkrementella ögonblicksbilder. Storleken på ögonblicksbilden beror på dataomsättning som är i de flesta fall cirka 2-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Är en omedelbar återställning ögonblicksbild en inkrementell ögonblicksbild eller en fullständig ögonblicksbild?
Ögonblicksbilder som tas som en del av kapaciteten för omedelbar återställning är inkrementella ögonblicksbilder.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hur kan jag för att beräkna den ungefärliga kostnad ökningen på grund av omedelbar återställning funktionen?
Det beror på omsättning på den virtuella datorn. I ett stabilt tillstånd, kan du anta ökning av kostnaden är = ögonblicksbild kvarhållning period dagliga dataomsättningen per VM storage-kostnad per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Om typ av återställning för en återställningspunkt är ”ögonblicksbild och valv” och jag utföra en återställning, vilken typ av återställning kommer att användas?
Om typ av återställning är ”ögonblicksbild och valv”, kommer återställning göras automatiskt från den lokala ögonblicksbilder som ska jämföras mycket snabbare att återställa från valvet.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Vad händer om jag väljer en kvarhållningsperiod för återställningspunkt (nivå 2) mindre än kvarhållningsperioden ögonblicksbild (nivå 1)?
Den nya modellen tillåter inte att ta bort återställningspunkten (– nivå 2) om inte ögonblicksbild (nivå 1) har tagits bort. Vi rekommenderar att schemalägga återställningspunkt (– nivå 2) kvarhållningsperiod är större än kvarhållningsperioden för ögonblicksbild.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Varför är min ögonblicksbild befintliga även efter den inställda kvarhållningsperioden period i principen för säkerhetskopiering?
Om återställningspunkten du har en ögonblicksbild och det är den senaste tillgängliga RP, finns den kvar tills det finns en säkerhetskopia som nästa gång. Det här är enligt utformade GC policy idag som anger att minst en senaste RP närvara alltid om alla säkerhetskopior ytterligare på misslyckas på grund av ett problem på den virtuella datorn. I normala fall rensas RPs i maximalt 24 timmar upphört att gälla.
