---
title: Vanliga frågor och svar – säkerhetskopiera virtuella Azure-datorer
description: I den här artikeln hittar du svar på vanliga frågor om hur du säkerhetskopierar virtuella Azure-datorer med tjänsten Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: b8e259c6212e9a1e81b6b0c8825287f3025f9068
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680536"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Vanliga frågor och svar – säkerhetskopiera virtuella Azure-datorer

I den här artikeln besvaras vanliga frågor om säkerhets kopiering av virtuella Azure-datorer med tjänsten [Azure Backup](backup-introduction-to-azure-backup.md) .

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Vilka VM-avbildningar kan aktive ras för säkerhets kopiering när jag skapar dem?

När du skapar en virtuell dator kan du aktivera säkerhets kopiering för virtuella datorer som kör [operativ system som stöds](backup-support-matrix-iaas.md#supported-backup-actions)

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Ingår säkerhets kopierings kostnaden i kostnaden för den virtuella datorn?

Nej. Kostnaderna för säkerhets kopiering skiljer sig från en VM-kostnad. Läs mer om [Azure Backup prissättning](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Vilka behörigheter krävs för att aktivera säkerhets kopiering för en virtuell dator?

Om du är en VM-deltagare kan du aktivera säkerhets kopiering på den virtuella datorn. Om du använder en anpassad roll behöver du följande behörigheter för att aktivera säkerhets kopiering på den virtuella datorn:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Om Recovery Services valvet och den virtuella datorn har olika resurs grupper kontrollerar du att du har Skriv behörighet i resurs gruppen för Recovery Servicess valvet.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Använder ett säkerhets kopierings jobb på begäran samma bevarande schema som schemalagda säkerhets kopieringar?

Nej. Ange kvarhållningsintervall för ett säkerhets kopierings jobb på begäran. Som standard sparas den i 30 dagar när den utlöses från portalen.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Jag nyligen har aktiverat Azure Disk Encryption på vissa virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?

Ange behörigheter för Azure Backup för att få åtkomst till Key Vault. Ange behörigheter i PowerShell enligt beskrivningen i avsnittet **Aktivera säkerhets kopiering** i [Azure Backup PowerShell](backup-azure-vms-automation.md) -dokumentationen.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Jag migrerade VM-diskar till hanterade diskar. Kommer mina säkerhetskopior att fungera även i fortsättningen?

Ja, säkerhets kopieringarna fungerar sömlöst. Du behöver inte konfigurera om något.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Varför kan jag inte se min virtuella dator i guiden Konfigurera säkerhetskopiering?

Guiden visar endast virtuella datorer i samma region som valvet och som inte redan säkerhets kopie ras.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Den virtuella datorn stängs av. Kommer en säkerhets kopiering på begäran eller en schemalagd säkerhets kopiering att fungera?

Ja. Säkerhets kopieringar körs när en dator stängs av. Återställnings punkten markeras som krasch-konsekvent.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan jag avbryta ett pågående säkerhets kopierings jobb?

Ja. Du kan avbryta säkerhets kopierings jobbet i status för **ögonblicks bilder** . Du kan inte avbryta ett jobb om data överföringen från ögonblicks bilden pågår.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Jag har aktiverat lås på resurs grupp som skapats av Azure Backups tjänsten (dvs. `AzureBackupRG_<geo>_<number>`) kommer mina säkerhets kopieringar att fortsätta att fungera?

Om du låser resurs gruppen som skapats av Azure Backup-tjänsten, kommer säkerhets kopieringarna att Miss lyckas eftersom det finns en övre gräns på 18 återställnings punkter.

Användaren måste ta bort låset och rensa återställnings punkt samlingen från den resurs gruppen för att de framtida säkerhets kopieringarna ska kunna utföras, [så följ stegen](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) nedan för att ta bort återställnings punkts samlingen.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Har Azure Backup stöd för standard SSD-hanterade diskar?

Ja, Azure Backup stöder [standard-SSD Managed disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Kan vi säkerhetskopiera en virtuell dator med en Skrivningsaccelerator (WA)-aktiverad disk?

Ögonblicks bilder kan inte utföras på den WA-aktiverade disken. Azure Backups tjänsten kan dock utesluta den WA-aktiverade disken från säkerhets kopian.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Jag har en virtuell dator med Skrivningsaccelerator-diskar (WA) och SAP HANA installerade. Hur gör jag för att säkerhetskopiera?

Azure Backup kan inte säkerhetskopiera den WA-aktiverade disken, men kan utesluta den från en säkerhets kopia. Säkerhets kopieringen ger dock inte databas konsekvens eftersom information på den WA-aktiverade disken inte säkerhets kopie ras. Du kan säkerhetskopiera diskar med den här konfigurationen om du vill ha säkerhets kopiering av operativ system diskar och säkerhets kopiering av diskar som inte är WA-aktiverade.

Vi kör privat för hands version för en SAP HANA säkerhets kopiering med en återställnings period på 15 minuter. Den är byggd på liknande sätt som SQL DB backup och använder backInt-gränssnittet för lösningar från tredje part som certifierats av SAP HANA. Om du är intresse rad kan du skicka ett e-postmeddelande till oss på `AskAzureBackupTeam@microsoft.com` med ämnet **Registrera dig för privat för hands version av SAP HANA i virtuella Azure-datorer**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Vad är den största fördröjning som jag förväntar dig i Start tiden för säkerhets kopieringen från den schemalagda säkerhets kopierings tiden jag har angett i min princip för säkerhets kopiering

Den schemalagda säkerhets kopieringen aktive ras inom 2 timmar efter den schemalagda säkerhets kopierings tiden. Om t. ex. 100-datorer har en schemalagd start tid som är schemalagd till 2:00 AM, kommer de virtuella datorerna i 100 att ha säkerhets kopierings jobb efter maximalt 4:00. Om schemalagda säkerhets kopieringar har pausats på grund av strömavbrott och återförsök, kan säkerhets kopieringen starta utanför det här schemalagda fönstret i två timmar.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Vad är det lägsta tillåtna kvarhållningsintervallet för daglig säkerhets kopierings punkt?

Säkerhets kopierings principen för virtuella Azure-datorer stöder ett minsta kvarhållningsintervall på sju dagar upp till 9999 dagar. Ändringar i en befintlig princip för säkerhets kopiering av virtuella datorer med mindre än sju dagar kräver en uppdatering för att uppfylla det lägsta kvarhållningsintervallet på sju dagar.

## <a name="restore"></a>Återställ

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hur gör jag för att bestämma om du bara vill återställa diskar eller en fullständig virtuell dator?

Tänk på en VM-återställning som ett snabb skapande alternativ för en virtuell Azure-dator. Det här alternativet ändrar disk namn, behållare som används av diskarna, offentliga IP-adresser och nätverks gränssnitts namn. Ändringen upprätthåller unika resurser när en virtuell dator skapas. Den virtuella datorn har inte lagts till i en tillgänglighets uppsättning.

Du kan använda alternativet Återställ disk om du vill:

- Anpassa den virtuella datorn som skapas. Ändra till exempel storleken.
- Lägg till konfigurations inställningar som inte fanns vid säkerhets kopierings tillfället.
- Styr namngivnings konventionen för resurser som skapas.
- Lägg till den virtuella datorn i en tillgänglighets uppsättning.
- Lägg till andra inställningar som måste konfigureras med hjälp av PowerShell eller en mall.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kan jag återställa säkerhets kopior av ohanterade VM-diskar efter att jag uppgraderat till Managed disks?

Ja, du kan använda säkerhets kopior som tagits innan diskarna migrerades från ohanterade till hanterade.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hur återställer jag en virtuell dator till en återställningspunkt innan den virtuella datorn har migrerats till hanterade diskar?

Återställnings processen förblir densamma. Om återställnings punkten är en tidpunkt då den virtuella datorn hade ohanterade diskar kan du [återställa diskarna som ohanterade](tutorial-restore-disk.md#unmanaged-disks-restore). Om den virtuella datorn hade hanterade diskar kan du [återställa diskarna som Managed disks](tutorial-restore-disk.md#managed-disk-restore). Sedan kan du [skapa en virtuell dator från dessa diskar](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Läs mer](backup-azure-vms-automation.md#restore-an-azure-vm) om hur du gör detta i PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Kan jag återställa den virtuella datorn som har tagits bort?

Ja. Även om du tar bort den virtuella datorn kan du gå till motsvarande säkerhets kopierings objekt i valvet och återställa från en återställnings punkt.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Så här återställer du en virtuell dator till samma tillgänglighets uppsättningar?

För hanterad virtuell Azure-dator är återställning till tillgänglighets uppsättningarna aktive rad genom att tillhandahålla ett alternativ i mallen när de återställs som hanterade diskar. Den här mallen har Indataparametern inheter **tillgänglighets uppsättningar**.

### <a name="how-do-we-get-faster-restore-performances"></a>Hur får vi snabbare återställning av prestanda?

[Omedelbar återställnings](backup-instant-restore-capability.md) funktion hjälper till snabbare säkerhets kopieringar och återställningar direkt från ögonblicks bilderna.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Vad händer när vi ändrar nyckel valvs inställningarna för den krypterade virtuella datorn?

När du har ändrat inställningarna för nyckel valvet för den krypterade virtuella datorn fortsätter säkerhets kopieringarna att fungera med den nya uppsättningen med information. Men efter återställningen från en återställnings punkt innan ändringen måste du återställa hemligheterna i ett nyckel valv innan du kan skapa den virtuella datorn från den. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)

Åtgärder som hemliga/viktiga överanvändning kräver inte det här steget och samma nyckel valv kan användas efter återställningen.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Kan jag få åtkomst till den virtuella datorn när den återställts på grund av en virtuell dator med en skadad relation med domänkontrollanten?

Ja, du får åtkomst till den virtuella datorn när den återställts på grund av en virtuell dator med en bruten relation med domänkontrollanten. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Hantera säkerhetskopior av virtuella datorer

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Vad händer om jag ändrar en säkerhets kopierings policy?

Den virtuella datorn säkerhets kopie ras med inställningarna för schemaläggning och kvarhållning i den ändrade eller nya principen.

- Om kvarhållning är utökad, markeras befintliga återställnings punkter i enlighet med den nya principen.
- Om kvarhållning minskas markeras återställnings punkter för rensning i nästa rensnings jobb och tas sedan bort.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hur gör jag för att flyttar du en virtuell dator som har säkerhetskopierats av Azure Backup till en annan resurs grupp?

1. Stoppa säkerhets kopieringen tillfälligt och behåll säkerhets kopierings data.
2. Flytta den virtuella datorn till mål resurs gruppen.
3. Säkerhets kopiering på nytt i samma eller nya valv.

Du kan återställa den virtuella datorn från tillgängliga återställnings punkter som skapades före flytt åtgärden.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Finns det en gräns för antalet virtuella datorer som kan associeras med samma säkerhets kopierings princip?

Ja, det finns en gräns på 100 virtuella datorer som kan kopplas till samma säkerhets kopierings princip från portalen. Vi rekommenderar att du skapar flera säkerhets kopierings principer med samma schema eller ett annat schema för fler än 100 virtuella datorer.
