---
title: Azure Instant Restore-funktion
description: Azure Instant Restore-funktion och vanliga frågor och svar om VM backup stack, Resource Manager distributions modell
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7a962ff42125b262f6ec6673f75b0afd2954de24
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806379"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Få förbättrad säkerhets kopiering och återställning av prestanda med Azure Backup omedelbar återställnings funktion

> [!NOTE]
> Baserat på feedback från användare kan vi byta namn på **VM backup stack v2** till **omedelbar återställning** för att minska förvirring med Azure Stack funktion.
> Alla Azure Backup-användare har nu uppgraderats till **omedelbar återställning**.

Den nya modellen för omedelbar återställning ger följande funktions förbättringar:

* Möjlighet att använda ögonblicks bilder som tagits som en del av ett säkerhets kopierings jobb som är tillgängligt för återställning utan att vänta på att data överförs till valvet att slutföras. Det minskar vänte tiden för ögonblicks bilder som ska kopieras till valvet innan återställningen utlöses.
* Minskar antalet säkerhets kopierings-och återställnings tider genom att behålla ögonblicks bilderna lokalt, under två dagar som standard. Standardvärdet för kvarhållning av ögonblicks bilder kan konfigureras till ett värde mellan 1 och 5 dagar.
* Stöder disk storlekar upp till 32 TB. Att ändra storlek på diskar rekommenderas inte av Azure Backup.
* Stöder Standard SSD diskar tillsammans med Standard HDD diskar och Premium SSD diskar.
* Möjlighet att använda en ohanterad virtuell dators ursprungliga lagrings konton (per disk) när du återställer. Den här funktionen finns även när den virtuella datorn har diskar som är distribuerade över lagrings konton. Den påskyndar återställnings åtgärder för en mängd olika VM-konfigurationer.
* För säkerhets kopiering av virtuella datorer som använder Premium Storage, med omedelbar återställning, rekommenderar vi att du allokerar *50%* ledigt utrymme för det totala allokerade lagrings utrymmet, vilket **endast** krävs för den första säkerhets kopieringen. Det lediga utrymmet på 50% är inte ett krav för säkerhets kopieringar när den första säkerhets kopieringen har slutförts.

## <a name="whats-new-in-this-feature"></a>Vad är nytt i den här funktionen

För närvarande består säkerhets kopierings jobbet av två faser:

1. Tar en ögonblicks bild av en virtuell dator.
2. Överför en ögonblicks bild av en virtuell dator till Azure Recovery Services-valvet.

En återställnings punkt anses bara skapas efter att steg 1 och 2 har slutförts. Som en del av den här uppgraderingen skapas en återställnings punkt så snart som ögonblicks bilden är färdig och den här återställnings punkten för ögonblicks bilds typen kan användas för att utföra en återställning med samma återställnings flöde. Du kan identifiera den här återställnings punkten i Azure Portal med "ögonblicks bild" som återställnings punkt typ, och när ögonblicks bilden har överförts till valvet ändras återställnings punktens typ till "ögonblicks bild och valv".

![Säkerhets kopierings jobb i den virtuella datorns resurs hanterare distributions modell--lagring och valv](./media/backup-azure-vms/instant-rp-flow.png)

Som standard behålls ögonblicks bilder i två dagar. Den här funktionen tillåter återställnings åtgärder från dessa ögonblicks bilder där genom att minska återställnings tiderna. Det minskar den tid som krävs för att transformera och kopiera data tillbaka från valvet.

## <a name="feature-considerations"></a>Funktions överväganden

* Ögonblicks bilder lagras tillsammans med diskarna för att förbättra skapandet av återställnings punkter och påskynda återställnings åtgärder. Därför kan du se lagrings kostnader som motsvarar ögonblicks bilder som tas under den här perioden.
* Stegvisa ögonblicks bilder lagras som Page blobbar. Alla användare som använder ohanterade diskar debiteras för ögonblicks bilderna som lagras i det lokala lagrings kontot. Eftersom de återställnings punkt samlingar som används av hanterade VM-säkerhetskopieringar använder BLOB-ögonblicksbilder på den underliggande lagrings nivån, ser du kostnader som motsvarar priserna för BLOB-ögonblicksbilder och de ökar.
* För Premium Storage-konton räknas ögonblicks bilderna för omedelbara återställnings punkter mot 10 TB-gränsen för allokerat utrymme.
* Du får möjlighet att konfigurera ögonblicks bildens kvarhållning baserat på återställnings behoven. Beroende på kravet kan du ställa in kvarhållning av ögonblicks bilder till minst en dag på bladet säkerhets kopierings policy enligt beskrivningen nedan. Detta hjälper dig att spara kostnader för kvarhållning av ögonblicks bilder om du inte utför återställningar ofta.
* Det är en vägbeskrivnings uppgradering, när du har uppgraderat till omedelbar återställning, kan du inte gå tillbaka.

>[!NOTE]
>Med den här uppgraderingen av återställnings perioden kommer varaktigheten för ögonblicks bilder av alla kunder (**nya och befintliga båda**) att ställas in på standardvärdet två dagar. Du kan dock ange varaktigheten enligt ditt krav till ett värde mellan 1 och 5 dagar.

## <a name="cost-impact"></a>Kostnads påverkan

De stegvisa ögonblicks bilderna lagras i den virtuella datorns lagrings konto, som används för omedelbar återställning. Stegvisa ögonblicks bilder innebär att det utrymme som används av en ögonblicks bild motsvarar det utrymme som används av sidor som skrivs efter att ögonblicks bilden skapades. Faktureringen är fortfarande för det utrymme som används av ögonblicks bilden och priset per GB är detsamma som på [sidan prissättning](https://azure.microsoft.com/pricing/details/managed-disks/).

>[!NOTE]
> Kvarhållning av ögonblicks bilder har åtgärd ATS till 5 dagar för vecko principer.

## <a name="configure-snapshot-retention"></a>Konfigurera kvarhållning av ögonblicks bilder

### <a name="using-azure-portal"></a>Använda Azure Portal

I Azure Portal kan du se ett fält som lagts till i bladet **säkerhets kopierings princip för virtuell dator** under avsnittet **omedelbar återställning** . Du kan ändra varaktighet för kvarhållning av ögonblicks bilder från bladet **princip för säkerhets kopiering** för alla virtuella datorer som är associerade med den aktuella säkerhets kopierings principen.

![Omedelbar återställnings funktion](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Använda PowerShell

>[!NOTE]
> Från AZ PowerShell version 1.6.0 och senare kan du uppdatera kvarhållningsperioden för ögonblicks bilder av ögonblicks bilder i principen med hjälp av PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Standard kvarhållning av ögonblicks bilder för varje princip är inställd på två dagar. Användaren kan ändra värdet till minst 1 och högst fem dagar. För vecko Visa-principer är kvarhållning av ögonblicks bilder åtgärdat till fem dagar.

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Vilka är kostnads konsekvenserna för omedelbar återställning?

Ögonblicks bilder lagras tillsammans med diskarna för att påskynda skapandet och återställnings åtgärderna för återställnings punkter. Därför kan du se de lagrings kostnader som motsvarar den kvarhållna ögonblicks bilder som valts som en del av principen för säkerhets kopiering av virtuella datorer.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Gör ögonblicks bilderna för omedelbar återställnings punkt i Premium Storage konton med gränsen på 10 TB ögonblicks bilder?

Ja, för Premium Storage-konton är ögonblicks bilderna som tas för omedelbar återställnings punkt 10 TB allokerat ögonblicks bild utrymme.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hur fungerar kvarhållning av ögonblicks bilder under fem dagar?

Varje dag kommer en ny ögonblicks bild att tas, och det finns fem enskilda ögonblicks bilder. Storleken på ögonblicks bilden beror på data omsättningen, som är i de flesta fall cirka 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Är en ögonblicks bild av en ögonblicks bild eller fullständig ögonblicks bild?

Ögonblicks bilder som tas som en del av omedelbar återställnings funktion är stegvisa ögonblicks bilder.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hur kan jag beräkna den ungefärliga kostnads ökningen på grund av omedelbar återställnings funktion?

Det beror på den virtuella datorns omsättning. I ett stabilt tillstånd kan du anta att kostnaden är = den dagliga lagrings tiden för kvarhållning per virtuell dator per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Om återställnings typen för en återställnings punkt är "ögonblicks bild och valv" och jag utför en återställnings åtgärd kommer vilken återställnings typ att användas?

Om återställnings typen är "ögonblicks bild och valv" görs återställningen automatiskt från den lokala ögonblicks bilden, vilket kommer att vara mycket snabbare jämfört med återställningen som görs från valvet.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Vad händer om jag väljer kvarhållningsperiod för återställnings punkt (nivå 2) mindre än lagrings perioden för ögonblicks bilder (1)?

Den nya modellen tillåter inte borttagning av återställnings punkten (– nivå 2) om inte ögonblicks bilden (1) tas bort. Vi rekommenderar att du schemalägger – nivå 2-kvarhållning (schema för återställnings punkt) som är större än kvarhållningsperioden för ögonblicks bilder.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Varför är min ögonblicks bild befintlig även efter den angivna kvarhållningsperioden i säkerhets kopierings policyn?

Om återställnings punkten har ögonblicks bilder och det är den senaste tillgängliga RP-filen, behålls den tills tiden det finns en nästa lyckade säkerhets kopiering. Detta är enligt den designade principen "skräp insamling" (GC) idag som bestämmer att minst en senaste RP alltid är tillgänglig om alla säkerhets kopieringar är misslyckade på grund av ett problem på den virtuella datorn. I normala scenarier rensas RPs på högst 24 timmar efter att de gått ut.
