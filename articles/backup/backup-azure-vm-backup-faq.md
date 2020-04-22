---
title: Vanliga frågor och svar – säkerhetskopiering av virtuella Azure-datorer
description: I den här artikeln kan du hitta svar på vanliga frågor om säkerhetskopiering av virtuella Azure-datorer med Azure Backup-tjänsten.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: accfc57055f70254814c889de875f5360878bcd9
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757466"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Vanliga frågor och svar-Säkerhetskopiera virtuella Azure-datorer

Den här artikeln besvarar vanliga frågor om säkerhetskopiering av virtuella Azure-datorer med [Azure Backup-tjänsten.](backup-introduction-to-azure-backup.md)

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Vilka VM-avbildningar kan aktiveras för säkerhetskopiering när jag skapar dem?

När du skapar en virtuell dator kan du aktivera säkerhetskopiering för virtuella datorer som kör [operativsystem som stöds](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Ingår kostnaden för säkerhetskopiering i den virtuella datorn?

Nej. Säkerhetskopieringskostnader är separata från en virtuell dators kostnader. Läs mer om [prissättning för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Vilka behörigheter krävs för att aktivera säkerhetskopiering för en virtuell dator?

Om du är en VM-deltagare kan du aktivera säkerhetskopiering på den virtuella datorn. Om du använder en anpassad roll behöver du följande behörigheter för att aktivera säkerhetskopiering på den virtuella datorn:

- Microsoft.RecoveryServices/Valv/skrivning
- Microsoft.RecoveryServices/Valv/läsa
- Microsoft.RecoveryServices/platser/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Valv/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Valv/backupPolicies/read
- Microsoft.RecoveryServices/Valv/backupPolicies/write

Om valvet och den virtuella datorn för Återställningstjänster har olika resursgrupper kontrollerar du att du har skrivbehörighet i resursgruppen för valvet för Återställningstjänster.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Använder ett säkerhetskopieringsjobb på begäran samma kvarhållningsschema som schemalagda säkerhetskopior?

Nej. Ange kvarhållningsintervallet för ett säkerhetskopieringsjobb på begäran. Som standard behålls den i 30 dagar när den utlöses från portalen.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Jag nyligen har aktiverat Azure Disk Encryption på vissa virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?

Ge behörigheter för Azure Backup för att komma åt Nyckelvalvet. Ange behörigheterna i PowerShell enligt beskrivningen i avsnittet **Aktivera säkerhetskopiering** i [Azure Backup PowerShell-dokumentationen.](backup-azure-vms-automation.md)

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Jag migrerade VM-diskar till hanterade diskar. Kommer mina säkerhetskopior att fungera även i fortsättningen?

Ja, säkerhetskopior fungerar sömlöst. Du behöver inte konfigurera om nåt.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Varför kan jag inte se min virtuella dator i guiden Konfigurera säkerhetskopiering?

Guiden listar bara virtuella datorer i samma region som valvet och som inte redan säkerhetskopieras.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Min virtuella dator är avstängd. Kommer en on-demand eller en schemalagd säkerhetskopiering arbete?

Ja. Säkerhetskopior körs när en dator stängs av. Återställningspunkten markeras som kraschkonsekvent.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan jag avbryta ett pågående säkerhetskopieringsjobb?

Ja. Du kan avbryta säkerhetskopieringsjobbet i ett **ta ögonblicksbildtillstånd.** Du kan inte avbryta ett jobb om dataöverföring från ögonblicksbilden pågår.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Jag har aktiverat ett lås för resursgruppen som skapats av Azure Backup Service (till exempel `AzureBackupRG_<geo>_<number>`). Kommer mina säkerhetskopior att fungera även i fortsättningen?

Om du låser resursgruppen som skapats av Azure Backup Service, kommer säkerhetskopior att börja misslyckas eftersom det finns en maximal gräns på 18 återställningspunkter.

Ta bort låset och rensa insamlingen av återställningsplatsen från den resursgruppen för att göra framtida säkerhetskopior framgångsrika. [Följ dessa steg](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) för att ta bort samlingen återställningspunkt.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Stöder Azure backup standard-SSD-hanterade diskar?

Ja, Azure Backup stöder [vanliga SSD-hanterade diskar](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Kan vi säkerhetskopiera en virtuell dator med en Write Accelerator (WA)-aktiverad disk?

Ögonblicksbilder kan inte tas på den WA-aktiverade disken. Azure Backup-tjänsten kan dock utesluta DEN WA-aktiverade disken från säkerhetskopiering.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Jag har en virtuell dator med Write Accelerator (WA) diskar och SAP HANA installerat. Hur säkerhetskopierar jag?

Azure Backup kan inte säkerhetskopiera den WA-aktiverade disken men kan utesluta den från säkerhetskopiering. Säkerhetskopieringen ger dock inte databasen konsekvens eftersom informationen på den WA-aktiverade disken inte säkerhetskopieras. Du kan säkerhetskopiera diskar med den här konfigurationen om du vill att operativsystemets disksäkerhetskopiering och säkerhetskopiering av diskar som inte är WA-aktiverade.

Azure Backup tillhandahåller en lösning för säkerhetskopiering av direktuppspelning för SAP HANA-databaser med en RPO på 15 minuter. Det är Backint certifierat av SAP för att tillhandahålla ett inbyggt stöd för säkerhetskopiering som utnyttjar SAP HANA:s inbyggda API:er. Läs mer [om säkerhetskopiering av SAP HANA-databaser i virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-db-about).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Vad är den maximala fördröjningen jag kan förvänta mig i säkerhetskopieringsstarttid från den schemalagda säkerhetskopieringstiden som jag har angett i min princip för säkerhetskopiering av virtuella datorer?

Den schemalagda säkerhetskopieringen utlöses inom 2 timmar efter den schemalagda säkerhetskopieringstiden. Om till exempel 100 virtuella datorer har sin starttid för säkerhetskopiering schemalagd klockan 02:00, kommer senast alla 100 virtuella datorer att ha sitt säkerhetskopieringsjobb på gång. Om schemalagda säkerhetskopior har pausats på grund av ett avbrott och återupptagits eller gjorts om kan säkerhetskopieringen starta utanför det schemalagda tvåtimmarsfönstret.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Vad är det minsta tillåtna kvarhållningsintervallet för en daglig säkerhetskopieringspunkt?

Azure Virtual Machine backup princip stöder ett minsta kvarhållningsintervall från sju dagar upp till 9999 dagar. Alla ändringar av en befintlig princip för säkerhetskopiering av virtuella datorer med mindre än sju dagar kräver en uppdatering för att uppfylla det minsta kvarhållningsintervallet på sju dagar.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Vad händer om jag ändrar fallet med namnet på min virtuella dator eller min VM-resursgrupp?

Om du ändrar skiftläge (till övre eller nedre) för din vm- eller VM-resursgrupp ändras inte fallet med namnet på säkerhetskopieringsobjektet. Detta förväntas dock Azure Backup beteende. Ärendeändringen visas inte i säkerhetskopian, men uppdateras i backend.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Kan jag säkerhetskopiera eller återställa selektiva diskar som är anslutna till en virtuell dator?

Azure Backup stöder nu selektiv säkerhetskopiering och återställning av disk med hjälp av azure virtual machine-säkerhetskopieringslösningen.

Idag stöder Azure Backup säkerhetskopiering av alla diskar (operativsystem och data) i en virtuell dator tillsammans med hjälp av lösningen för säkerhetskopiering av virtuella datorer. Med exkluderingsdiskfunktioner får du möjlighet att säkerhetskopiera en eller några från de många datadiskarna i en virtuell dator. Detta ger en effektiv och kostnadseffektiv lösning för dina säkerhetskopierings- och återställningsbehov. Varje återställningspunkt innehåller data för diskarna som ingår i säkerhetskopieringen, vilket gör att du ytterligare kan få en delmängd diskar återställda från den angivna återställningspunkten under återställningen. Detta gäller för att återställa både från ögonblicksbilden och valvet.

För att registrera dig för förhandsgranskningen, skriv till oss påAskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Återställ

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hur bestämmer jag om diskar bara ska återställas eller en fullständig virtuell dator?

Tänk på en vm-återställning som ett snabbt skapa alternativ för en Azure VM. Det här alternativet ändrar disknamn, behållare som används av diskar, offentliga IP-adresser och nätverksgränssnittsnamn. Ändringen upprätthåller unika resurser när en virtuell dator skapas. Den virtuella datorn läggs inte till i en tillgänglighetsuppsättning.

Du kan använda alternativet återställ disk om du vill:

- Anpassa den virtuella datorn som skapas. Ändra till exempel storleken.
- Lägg till konfigurationsinställningar som inte fanns där vid säkerhetskopieringen.
- Styr namngivningskonventionen för resurser som skapas.
- Lägg till den virtuella datorn i en tillgänglighetsuppsättning.
- Lägg till andra inställningar som måste konfigureras med PowerShell eller en mall.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kan jag återställa säkerhetskopior av ohanterade VM-diskar efter att jag uppgraderat till hanterade diskar?

Ja, du kan använda säkerhetskopior som tagits innan diskar migrerades från ohanterade till hanterade.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hur återställer jag en virtuell dator till en återställningspunkt innan den virtuella datorn har migrerats till hanterade diskar?

Återställningsprocessen förblir densamma. Om återställningspunkten är av en tidpunkt då den virtuella datorn hade ohanterade diskar kan du [återställa diskar som ohanterade](tutorial-restore-disk.md#unmanaged-disks-restore). Om den virtuella datorn hade hanterade diskar kan du [återställa diskar som hanterade diskar](tutorial-restore-disk.md#managed-disk-restore). Sedan kan du [skapa en virtuell dator från dessa diskar](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Läs mer](backup-azure-vms-automation.md#restore-an-azure-vm) om att göra detta i PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Kan jag återställa den virtuella datorn som har tagits bort?

Ja. Även om du tar bort den virtuella datorn kan du gå till motsvarande säkerhetskopieringsobjekt i valvet och återställa från en återställningspunkt.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Hur återställer jag en virtuell dator till samma tillgänglighetsuppsättningar?

För virtuella datorer med hanterad disk Azure aktiveras återställning till tillgänglighetsuppsättningarna genom att tillhandahålla ett alternativ i mallen samtidigt som du återställer som hanterade diskar. Den här mallen har indataparametern **Tillgänglighetsuppsättningar**.

### <a name="how-do-we-get-faster-restore-performances"></a>Hur får vi snabbare återställa föreställningar?

[Instant Restore-funktionen](backup-instant-restore-capability.md) hjälper till med snabbare säkerhetskopieringar och omedelbar återställning från ögonblicksbilderna.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Vad händer när vi ändrar nyckelvalvsinställningarna för den krypterade virtuella datorn?

När du har ändrat nyckelvalvets inställningar för den krypterade virtuella datorn fortsätter säkerhetskopieringarna att fungera med den nya uppsättningen detaljer. Men efter återställningen från en återställningspunkt före ändringen måste du återställa hemligheterna i ett nyckelvalv innan du kan skapa den virtuella datorn från den. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret).

Åtgärder som hemlig/nyckel-överrullning kräver inte det här steget och samma KeyVault kan användas efter återställning.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Kan jag komma åt den virtuella datorn en gång återställd på grund av att en virtuell dator har brutit relation med domänkontrollanten?

Ja, du kommer åt den virtuella datorn när den har återställts på grund av att en virtuell dator har brutit relation med domänkontrollanten. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Hantera säkerhetskopior av virtuella datorer

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Vad händer om jag ändrar en princip för säkerhetskopiering?

Den virtuella datorn säkerhetskopieras med hjälp av schema- och kvarhållningsinställningarna i den ändrade eller nya principen.

- Om kvarhållningen utökas markeras befintliga återställningspunkter och behålls i enlighet med den nya principen.
- Om kvarhållningen minskas markeras återställningspunkter för rensning i nästa rensningsjobb och tas sedan bort.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hur flyttar jag en virtuell dator som backas upp av Azure Backup till en annan resursgrupp?

1. Stoppa säkerhetskopian tillfälligt och behåll säkerhetskopieringsdata.
2. Flytta den virtuella datorn till målresursgruppen.
3. Återaktivera säkerhetskopiering i samma eller nya valv.

Du kan återställa den virtuella datorn från tillgängliga återställningspunkter som skapades före flytten.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Finns det en gräns för antalet virtuella datorer som kan associeras med samma säkerhetskopieringsprincip?

Ja, det finns en gräns på 100 virtuella datorer som kan associeras till samma säkerhetskopieringsprincip från portalen. Vi rekommenderar att du skapar flera principer för säkerhetskopiering med samma schema eller olika schema för mer än 100 virtuella datorer.
