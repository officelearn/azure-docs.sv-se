---
title: Uppgradera till den Azure VM-Säkerhetskopieringsstack V2
description: Uppgradera processen och vanliga frågor och svar för säkerhetskopieringsstack för virtuell dator, Resource Manager-distributionsmodellen
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 10/3/2018
ms.author: trinadhk
ms.openlocfilehash: adb6898d9b7f4c0272085828778096816489a18d
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201798"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Få förbättrad säkerhetskopian och återställer prestanda med Azure Backup-omedelbar återställning kapacitet

> [!NOTE]
> Baserat på feedback från användare som den **VM-säkerhetskopieringsstack V2** är förvirrande med Azure stack, vi har ändrat namnet på den till **omedelbar återställning** vilket ger en uppgraderad och bättre upplevelse.

> [!IMPORTANT]
> När du har fått positivt svar från våra användare som redan har den uppgraderade prestandan, vi har bestämt dig att uppgradera alla våra användare till funktionen för omedelbar återställning. Om du vill uppgradera, det finns ingen åtgärd krävs från din sida. **Startar februari 2019**, börjar vi lansera den här funktionen region efter region.
>
>

Den nya modellen för omedelbar återställning innehåller följande funktionsförbättringar:

* Möjlighet att använda ögonblicksbilder som tas som en del av ett säkerhetskopieringsjobb som är tillgängliga för återställning utan att behöva vänta på att överföra data till valvet för att slutföra. Det minskar väntetiden för ögonblicksbilder att kopiera till valvet innan återställningen.
* Minskar tider för säkerhetskopiering och återställning genom att behålla ögonblicksbilder lokalt, i två dagar som standard. Det här standard-valvet kan konfigureras till ett värde mellan 1 till 5 dagar.
* Stöder disk storlekar upp till 4 TB.
* Stöder Standard SSD-diskar.
* Möjligheten att använda en ohanterad virtuell dator ursprungliga lagringskonton (per disk) när du återställer. Denna möjlighet finns även när den virtuella datorn har diskar som är fördelade på storage-konton. Det går snabbare återställningsåtgärder för en mängd olika VM-konfigurationer.


## <a name="whats-new-in-this-feature"></a>Vad är nytt i den här funktionen

För närvarande består säkerhetskopieringsjobbet av två faser:

1.  Ta en ögonblicksbild för virtuell dator.
2.  Överföra en VM-ögonblicksbild till Azure Recovery Services-valvet.

En återställningspunkt anses skapas endast efter faser 1 och 2 har slutförts. En återställningspunkt skapas som en del av den här uppgraderingen när ögonblicksbilden har slutförts och den här återställningspunkten för typ av ögonblicksbild som kan användas för att utföra en återställning med hjälp av samma flöde för återställning. Du kan identifiera den här återställningspunkten i Azure portal genom att använda ”ögonblicksbild” som typ av återställning och när ögonblicksbilden överförs till valvet, typ av återställning ändras till ”ögonblicksbild och valv”.

![Säkerhetskopieringsjobbet på VM säkerhetskopieringsstack distributionsmodellen Resource Manager – lagring och valv](./media/backup-azure-vms/instant-rp-flow.png)

Som standard behålls ögonblicksbilder i två dagar. Den här funktionen kan återställningar åtgärden från de här ögonblicksbilderna det genom att stopptider återställning. Det minskar den tid som krävs för att omvandla och kopiera data från valvet till användarens storage-konto för ohanterade diskar scenarier och för användare av hanterad disk, skapar den hanterade diskar från Recovery Services-data.

## <a name="feature-considerations"></a>Överväganden för funktionernas

* Ögonblicksbilder lagras tillsammans med diskarna att öka skapa en återställningspunkt och påskynda återställningsåtgärder. Därför visas lagringskostnader för ögonblicksbilder som tas under denna period.
* Inkrementella ögonblicksbilder lagras som sidblobar. Alla användare som använder ohanterade diskar debiteras för ögonblicksbilder som lagras i kontot Lokal lagring. Eftersom återställningspunkt samlingar som används av säkerhetskopieringar för hanterade virtuella datorn använder blob-ögonblicksbilder på underliggande lagringsnivå, hanterade diskar visas kostnaderna som motsvarar blob-ögonblicksbild priser och de är inkrementell.
* Ögonblicksbilder som tas för omedelbar återställningspunkter count mot gränsen på 10 TB allokerat utrymme för premium storage-konton.
* Du får en möjlighet att konfigurera den ögonblicksbild kvarhållning av säkerhetskopior baserat på dina behov för återställning. Beroende på krav, kan du ange ögonblicksbild kvarhållning till minst en dag i bladet säkerhetskopieringspolicy som beskrivs nedan. Detta kan hjälpa dig minska kostnaderna för kvarhållning av ögonblicksbild.

> [!NOTE]
>
Med den här snabbmeddelanden återställa uppgradering ögonblicksbild kvarhållningsvaraktighetens för alla kunder **(nya och befintliga båda ingår)** anges till ett standardvärde på två dagar. Du kan dock ange varaktighet enligt dina krav på att ett värde mellan 1-5 dagar.
>
>

## <a name="cost-impact"></a>Kostnad påverkan

Inkrementella ögonblicksbilder lagras i Virtuella datorns lagringskonto som används för omedelbar återställning. Inkrementell ögonblicksbild innebär det utrymme som används av en ögonblicksbild är lika med det utrymme som används av sidor som har skrivits när ögonblicksbilden har skapats. Faktureringen sker fortfarande för den per GB använt utrymme som används av ögonblicksbilden och pris per GB är samma som de som nämns i den [prissättningssidan](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="upgrading-to-instant-restore"></a>Uppgradera till omedelbar återställning
Om du använder Azure-portalen kan se du ett meddelande på instrumentpanelen för valvet. Det här meddelandet relaterar till stöd för stora diskar och förbättringar för säkerhetskopiering och återställning hastighet. Du kan också gå till egenskapssidan för valvet för att hämta uppgraderingsalternativet.

![Säkerhetskopieringsjobbet på VM säkerhetskopieringsstack Resource Manager-modellen – stöd för meddelande](./media/backup-azure-vms/instant-rp-banner.png)

Välj banderollen för att öppna en skärm för att uppgradera till omedelbar återställning.

![Säkerhetskopieringsjobbet på säkerhetskopieringsstack för virtuell dator distributionsmodellen för Resource Manager – uppgradera](./media/backup-azure-vms/instant-rp.png)

## <a name="upcoming-changes"></a>Kommande ändringar

### <a name="configure-snapshot-retention-using-azure-portal"></a>Konfigurera ögonblicksbild kvarhållning av säkerhetskopior med hjälp av Azure portal

I Azure-portalen för de uppgraderade användarna, visas ett fält har lagts till i den **VM Backup-principen** bladet under den **omedelbar återställning** avsnittet. Du kan ändra kvarhållningsvaraktighetens ögonblicksbild från den **VM säkerhetskopieringsprincip** bladet för alla virtuella datorer som är associerade med principen för specifika säkerhetskopiering.


![Säkerhetskopieringsjobbet i omedelbar återställning distribution](./media/backup-azure-vms/instant-rp-banner1.png)


## <a name="upgrade-to-instant-restore-using-powershell"></a>Uppgradera till omedelbar återställning med hjälp av PowerShell

Om du inte uppgraderas automatiskt ännu och vill självbetjäning kör du följande cmdlets från en upphöjd PowerShell-terminalen:

1.  Logga in på ditt Azure-konto:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Välj den prenumeration som du vill registrera:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Registrera den här prenumerationen:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="upgrade-to-instant-restore-using-cli"></a>Uppgradera till omedelbar återställa med hjälp av CLI

Kör följande kommandon från ett gränssnitt:

1.  Logga in på ditt Azure-konto:

    ```
    az login
    ```

2.  Välj den prenumeration som du vill registrera:

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Registrera den här prenumerationen:

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>Kontrollera att uppgraderingen har utförts

### <a name="powershell"></a>PowerShell
Kör följande cmdlet från en upphöjd PowerShell-terminalen:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
Kör följande kommando från ett gränssnitt:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Om det står ”Registered” har din prenumeration uppgraderats till omedelbar återställning.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Vad är kostnaden effekterna av omedelbar återställning?
Ögonblicksbilder lagras tillsammans med diskar som snabbare att skapa en återställningspunkt och återställning. Därför visas lagringskostnader som motsvarar den ögonblicksbild kvarhållningsperioden som valts som en del av principen för säkerhetskopiering av virtuell dator.

### <a name="does-snapshot-retention-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>Ökar ögonblicksbild kvarhållning gränsen för premium storage-ögonblicksbilder av 10 TB?
Nej, gränsen för totalt antal ögonblicksbilder per storage-konto finns kvar på 10 TB.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>I Premium Storage-konton ögonblicksbilder som tas för omedelbar återställningspunkt uppta gränsen för ögonblicksbilder på 10 TB?
Ja, för premium storage-konton, ögonblicksbilder som tas för omedelbar återställningspunkt tar upp de allokerade 10 TB utrymme.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hur fungerar ögonblicksbild kvarhållning under femdagars-period?
Varje dag en ny ögonblicksbild tas, så det finns fem enskilda inkrementella ögonblicksbilder. Storleken på ögonblicksbilden beror på dataomsättning som är i de flesta fall cirka 2-5%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Är en omedelbar återställning ögonblicksbild en inkrementell ögonblicksbild eller en fullständig ögonblicksbild?
Ögonblicksbilder som tas som en del av kapaciteten för omedelbar återställning är inkrementella ögonblicksbilder.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hur kan jag för att beräkna den ungefärliga kostnad ökningen på grund av omedelbar återställning funktionen?
Det beror på omsättning på den virtuella datorn. I ett stabilt tillstånd, kan du anta att ökade kostnader är = ögonblicksbild kvarhållningsperiod * dataomsättning per dag per virtuell dator * storage-kostnad per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Om typ av återställning för en återställningspunkt är ”ögonblicksbild och valv” och jag utföra en återställning, vilken typ av återställning kommer att användas?
Om typ av återställning är ”ögonblicksbild och valv”, kommer återställning göras automatiskt från den lokala ögonblicksbilder som ska jämföras mycket snabbare att återställa från valvet.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Vad händer om jag väljer en kvarhållningsperiod för återställningspunkt (nivå 2) mindre än kvarhållningsperioden ögonblicksbild (nivå 1)?
Den nya modellen tillåter inte att ta bort återställningspunkten (– nivå 2) om inte ögonblicksbild (nivå 1) har tagits bort. För närvarande stöder vi sju dagar kvarhållningsperiod för borttagning av ögonblicksbilder (nivå 1), så att återställningspunkten (– nivå 2) kvarhållningsperioden för mindre än sju dagar lösts in inte. Vi rekommenderar att schemalägga återställningspunkt (– nivå 2) kvarhållningsperiod är större än sju dagar.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Varför är min ögonblicksbild befintliga även efter den inställda kvarhållningsperioden period i principen för säkerhetskopiering?
Om återställningspunkten du har en ögonblicksbild och det är den senaste tillgängliga RP, finns den kvar tills det finns en säkerhetskopia som nästa gång. Det här är enligt utformade GC policy idag som anger att minst en senaste RP närvara alltid om alla säkerhetskopior ytterligare på misslyckas på grund av ett problem på den virtuella datorn. I normala fall rensas RPs i upp till 48 timmar upphört att gälla.
