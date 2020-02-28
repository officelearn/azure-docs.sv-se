---
title: Säkerhetsfunktioner som hjälper dig att skydda moln arbets belastningar
description: Lär dig hur du använder säkerhetsfunktioner i Azure Backup för att göra säkerhets kopieringar säkrare.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 3435b9455af3362cdce2dceb20e183a8b05a15dd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660843"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Säkerhetsfunktioner som hjälper dig att skydda moln arbets belastningar som använder Azure Backup

Problem med säkerhets problem, t. ex. skadlig kod, utpressnings program vara och intrång, ökar. De här säkerhets problemen kan vara dyra, i termer av både Money och data. För att skydda mot sådana angrepp tillhandahåller Azure Backup nu säkerhetsfunktioner som hjälper dig att skydda säkerhets kopierings data även efter borttagning.

En sådan funktion är mjuk borttagning. Med mjuk borttagning, även om en skadlig aktör tar bort säkerhets kopian av en virtuell dator (eller säkerhets kopierings data tas bort av misstag), bevaras säkerhets kopierings data i ytterligare 14 dagar, vilket innebär att det inte går att återställa säkerhets kopierings objekt utan data förlust. De ytterligare 14 dagars kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar ingen kostnad för kunden. Azure krypterar också alla säkerhetskopierade data i vila med hjälp av [kryptering för lagringstjänst](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) för att skydda dina data ytterligare.

Mjuk borttagnings skydd för virtuella Azure-datorer är allmänt tillgängligt.

>[!NOTE]
>Mjuk borttagning för SQL Server i Azure VM och mjuk borttagning för SAP HANA i Azure VM-arbetsbelastningar finns nu i för hands version.<br>
>Registrera dig för för hands versionen genom att skriva till oss på AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Mjuk borttagning

### <a name="soft-delete-for-vms"></a>Mjuk borttagning för virtuella datorer

Mjuk borttagning för virtuella datorer skyddar de virtuella datorerna från oavsiktlig borttagning. Även efter att säkerhets kopiorna har tagits bort bevaras de i läget för mjuk borttagning under ytterligare 14 dagar.

> [!NOTE]
> Mjuk borttagning skyddar bara borttagna säkerhets kopierings data. Om en virtuell dator tas bort utan säkerhets kopiering bevaras inte data av funktionen för mjuk borttagning. Alla resurser bör skyddas med Azure Backup för att garantera fullständig återhämtning.
>

### <a name="supported-regions"></a>Regioner som stöds

Mjuk borttagning stöds för närvarande i USA, västra centrala, Asien, östra, centrala Kanada, östra Frankrike, centrala Frankrike, centrala Korea, södra, Storbritannien, södra, Storbritannien, västra, östra Australien, södra Australien, norra Europa, västra USA, västra 2; USA, centrala USA, södra Asien, östra, norra centrala USA, södra centrala USA, Östra Japan, västra Japan, södra Indien, centrala Indien, västra Indien, östra USA 2, Schweiz, norra, Schweiz, västra och alla nationella regioner.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Mjuk borttagning för virtuella datorer som använder Azure Portal

1. Om du vill ta bort säkerhets kopierings data för en virtuell dator måste säkerhets kopian stoppas. I Azure Portal går du till Recovery Services-valvet, högerklickar på säkerhets kopierings objekt och väljer **stoppa säkerhets kopiering**.

   ![Skärm bild av Azure Portal säkerhets kopierings objekt](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. I följande fönster får du ett alternativ för att ta bort eller behålla säkerhetskopierade data. Om du väljer **ta bort säkerhetskopierade data** och sedan **stoppar säkerhets kopieringen**tas inte den virtuella datorns säkerhets kopia bort permanent. I stället behålls säkerhets kopierings data i 14 dagar i läget Soft Deleted. Om du väljer **ta bort säkerhets kopierings data** skickas en e-postavisering till det konfigurerade e-postmeddelandet som informerar användaren om att 14 dagar fortfarande är av utökad kvarhållning för säkerhetskopierade data. Dessutom skickas en e-postavisering den 12: e dagen på att det finns två dagar kvar för att Resurrect borttagna data. Borttagningen skjuts upp fram till den femtonde dagen, när permanent borttagning kommer att ske och en slutgiltig e-postavisering skickas för att informera om permanent borttagning av data.

   ![Skärm bild av Azure Portal, stoppa säkerhets kopierings skärmen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Under dessa 14 dagar visas den mjuka borttagna virtuella datorn i Recovery Services-valvet med en röd "mjuk borttagning"-ikon bredvid.

   ![Skärm bild av Azure Portal, VM i läget för mjuk borttagning](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Om det finns mjuka, borttagna säkerhets kopierings objekt i valvet kan valvet inte tas bort vid denna tidpunkt. Försök att ta bort valvet när säkerhets kopierings objekten har tagits bort permanent och att det inte finns något objekt i läget Soft Deleted kvar i valvet.

4. Om du vill återställa den mjuk-borttagna virtuella datorn måste den först tas bort. Om du vill ångra borttagningen väljer du den mjuk-borttagna virtuella datorn och väljer sedan alternativet **ta bort**.

   ![Skärm bild av Azure Portal, ta bort virtuell dator](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Ett fönster visas där du får ett varnings meddelande om att ångra borttagning har valts och att alla återställnings punkter för den virtuella datorn tas bort och är tillgängliga för att utföra en återställnings åtgärd. Den virtuella datorn kommer att behållas i status "stoppa skydd med kvarhållning" med säkerhets kopior som pausats och säkerhets kopierings data kvarhålls för alltid med ingen säkerhets kopierings princip gällande.

   ![Skärm bild av Azure Portal, bekräfta borttagning av virtuell dator](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   I det här läget kan du också återställa den virtuella datorn genom att välja **Återställ virtuell dator** från den valda återställnings punkten.  

   ![Skärm bild av Azure Portal, Återställ VM-alternativ](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Skräp insamlaren kommer att köra och rensa återställnings punkter som har upphört att gälla först när användaren utför **säkerhets kopierings åtgärden återuppta** .

5. När du har slutfört borttagnings processen återgår statusen till "stoppa säkerhets kopiering med kvar data" och sedan kan du välja **återuppta säkerhets kopiering**. Åtgärden **återuppta säkerhets kopiering** kommer tillbaka till säkerhets kopierings objektet i det aktiva läget, associerat med en säkerhets kopierings princip som valts av användaren som definierar säkerhets kopierings-och bevarande scheman.

   ![Skärm bild av Azure Portal, återuppta säkerhets kopierings alternativ](./media/backup-azure-security-feature-cloud/resume-backup.png)

Det här Flow-diagrammet visar de olika stegen och tillstånden för ett säkerhets kopierings objekt när mjuk borttagning är aktiverat:

![Livs cykel för mjuk borttagning av säkerhets kopierings objekt](./media/backup-azure-security-feature-cloud/lifecycle.png)

Mer information finns i avsnittet [vanliga frågor och svar](backup-azure-security-feature-cloud.md#frequently-asked-questions) nedan.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Mjuk borttagning för virtuella datorer med Azure PowerShell

> [!IMPORTANT]
> AZ. RecoveryServices-versionen som krävs för att använda mjuk borttagning med Azure PS är min 2.2.0. Använd ```Install-Module -Name Az.RecoveryServices -Force``` för att hämta den senaste versionen.

Som beskrivs ovan för Azure Portal är sekvensen av steg densamma när du använder Azure PowerShell även.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Ta bort säkerhets kopierings objekt med Azure PowerShell

Ta bort säkerhets kopierings objekt med hjälp av cmdleten [disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

DeleteState för säkerhets kopierings objekt ändras från ' NotDeleted ' till ' ToBeDeleted '. Säkerhetskopierade data sparas i 14 dagar. Om du vill återställa borttagnings åtgärden ska du utföra Undo-Delete.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Ångra borttagnings åtgärden med Azure PowerShell

Börja med att hämta det relevanta säkerhets kopierings objekt som är i läget för mjuk borttagning (det vill säga tas bort).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Utför sedan åtgärden ångra borttagning med hjälp av cmdleten [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

DeleteState för säkerhets kopierings objekt återgår till ' NotDeleted '. Men skyddet har fortfarande stoppats. Du måste [återuppta säkerhets kopieringen](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) för att återaktivera skyddet.

### <a name="soft-delete-for-vms-using-rest-api"></a>Mjuk borttagning för virtuella datorer som använder REST API

- Ta bort säkerhets kopiorna med REST API som anges [här](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Om användaren vill ångra dessa borttagnings åtgärder, se de steg som beskrivs [här](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Inaktiverar mjuk borttagning

Mjuk borttagning är aktiverat som standard på nyligen skapade valv för att skydda säkerhets kopierings data från oavsiktliga eller skadliga borttagningar.  Du bör inte inaktivera den här funktionen. Den enda omständigheterna där du bör inaktivera mjuk borttagning är om du planerar att flytta dina skyddade objekt till ett nytt valv. det går inte att vänta 14 dagar innan du tar bort och återskyddar (till exempel i en test miljö). Endast valvets ägare kan inaktivera den här funktionen. Om du inaktiverar den här funktionen kommer alla framtida borttagningar av skyddade objekt att leda till omedelbar borttagning, utan möjlighet att återställa. Säkerhets kopierings data som finns i läget Soft Deleted innan funktionen inaktive ras fortsätter att vara i tyst borttaget läge under perioden 14 dagar. Om du vill ta bort dessa omedelbart permanent måste du ångra borttagningen och ta bort dem igen för att ta bort dem permanent.

### <a name="disabling-soft-delete-using-azure-portal"></a>Inaktivera mjuk borttagning med Azure Portal

Följ dessa steg om du vill inaktivera mjuk borttagning:

1. Gå till ditt valv i Azure Portal och gå sedan till **inställningar** -> **Egenskaper**.
2. I fönstret Egenskaper väljer du **säkerhets inställningar** -> **uppdatering**.  
3. I rutan säkerhets inställningar under **mjuk borttagning**väljer du **inaktivera**.

![Inaktivera mjuk borttagning](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Inaktivera mjuk borttagning med Azure PowerShell

> [!IMPORTANT]
> AZ. RecoveryServices-versionen som krävs för att använda mjuk borttagning med Azure PS är min 2.2.0. Använd ```Install-Module -Name Az.RecoveryServices -Force``` för att hämta den senaste versionen.

Om du vill inaktivera använder du cmdleten [set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Inaktivera mjuk borttagning med REST API

Om du vill inaktivera funktionen för mjuk borttagning med REST API, se de steg som beskrivs [här](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Ta bort mjuka borttagna säkerhets kopierings objekt permanent

Säkerhets kopierings data i läget Soft Deleted tidigare inaktive ring av den här funktionen kommer att vara i läget Soft Deleted Om du vill ta bort dessa omedelbart tar du bort dem igen och tar bort dem permanent.

### <a name="using-azure-portal"></a>Använda Azure Portal

Följ de här stegen:

1. Följ stegen för att [inaktivera mjuk borttagning](#disabling-soft-delete).
2. Gå till ditt valv i Azure Portal, gå till **säkerhets kopierings objekt**och välj den mjuka BORTTAGNA virtuella datorn.

![Välj Mjuk borttagning av virtuell dator](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Välj alternativet **ångra borttagning**.

![Välj ångra borttagning](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. Ett fönster visas. Välj **ångra borttagning**.

![Välj ångra borttagning](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Välj **ta bort säkerhetskopierade data** om du vill ta bort säkerhetskopierade data permanent.

![Välj Ta bort säkerhets kopierings data](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Ange namnet på det säkerhetskopierade objektet för att bekräfta att du vill ta bort återställnings punkterna.

![Ange namnet på det säkerhetskopierade objektet](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Om du vill ta bort säkerhetskopierade data för objektet väljer du **ta bort**. Ett meddelande visas där du vet att säkerhets kopierings data har tagits bort.

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Om objekten togs bort innan mjuk borttagning har inaktiverats, kommer de att vara i ett mjukt borttaget tillstånd. Om du vill ta bort dem omedelbart måste borttagnings åtgärden ångras och sedan utföras igen.

Identifiera objekt som är i läget Soft-Deleted.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ångra sedan borttagnings åtgärden som utfördes när mjuk borttagning har Aktiver ATS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Eftersom den mjuka borttagningen nu är inaktive rad kommer borttagnings åtgärden att leda till omedelbar borttagning av säkerhets kopierings data.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Använda REST-API:et

Om objekten togs bort innan mjuk borttagning har inaktiverats, kommer de att vara i ett mjukt borttaget tillstånd. Om du vill ta bort dem omedelbart måste borttagnings åtgärden ångras och sedan utföras igen.

1. Börja med att ångra borttagnings åtgärderna med stegen som anges [här](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Inaktivera sedan funktionerna för mjuk borttagning med REST API med hjälp av stegen som beskrivs [här](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Ta sedan bort säkerhets kopiorna med REST API som anges [här](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Kryptering

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>Kryptering av säkerhets kopierings data med hjälp av Microsoft-hanterade nycklar

Säkerhetskopierade data krypteras automatiskt med Azure Storage kryptering. Kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Data krypteras och dekrypteras transparent med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

I Azure skyddas data i överföring mellan Azure Storage och valvet av HTTPS. Dessa data finns kvar i Azure stamnät nätverket.

Mer information finns i [Azure Storage kryptering för vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Läs [Azure Backup vanliga frågor](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) och svar om du vill besvara eventuella frågor om kryptering.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Kryptering av säkerhets kopierings data med hjälp av Kundhanterade nycklar

När du säkerhetskopierar Azure-Virtual Machines har du också möjlighet att kryptera dina säkerhets kopierings data i Recovery Services valvet med hjälp av dina krypterings nycklar som lagras i Azure Key Vault.

>[!NOTE]
>Den här funktionen är för närvarande under tidig användning. Fyll i [den här undersökningen](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) om du vill kryptera dina säkerhets kopierings data med hjälp av Kundhanterade nycklar. Observera att möjligheten att använda den här funktionen omfattas av godkännande från Azure Backups tjänsten.

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>Säkerhets kopiering av den virtuella datorn med hanterade diskar med Kundhanterade nycklar

Med Azure Backup kan du säkerhetskopiera Azure-Virtual Machines som innehåller diskar som har krypterats med hjälp av Kundhanterade nycklar. Mer information finns i [kryptering av hanterade diskar med Kundhanterade nycklar](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

### <a name="backup-of-encrypted-vms"></a>Säkerhetskopiera krypterade virtuella datorer

Du kan säkerhetskopiera och återställa virtuella Windows-eller Linux Azure-datorer (VM: ar) med krypterade diskar med hjälp av tjänsten Azure Backup. Instruktioner finns i [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="other-security-features"></a>Andra säkerhetsfunktioner

### <a name="protection-of-azure-backup-recovery-points"></a>Skydd av Azure Backup återställnings punkter

Lagrings konton som används av Recovery Services-valven är isolerade och kan inte användas av användare i något skadligt syfte. Åtkomst tillåts endast via Azure Backup hanterings åtgärder, till exempel Restore. Dessa hanterings åtgärder styrs via rollbaserad Access Control (RBAC).

Mer information finns i [använda rollbaserad Access Control för att hantera Azure Backup återställnings punkter](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="for-soft-delete"></a>För mjuk borttagning

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Måste jag aktivera funktionen för mjuk borttagning i varje valv?

Nej, den skapas och aktive ras som standard för alla Recovery Services-valv.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Kan jag konfigurera det antal dagar för vilka mina data ska bevaras i läget Soft-Deleted när borttagningen har slutförts?

Nej, det har åtgärd ATS till 14 dagar efter ytterligare kvarhållning efter borttagnings åtgärden.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Behöver jag betala kostnaden för ytterligare 14-dagars kvarhållning?

Nej, den här 14-dagars ytterligare kvarhållning kommer kostnads fritt som en del av funktionen för mjuk borttagning.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kan jag utföra en återställnings åtgärd när mina data är i läget för tyst Borttagning?

Nej, du måste ta bort den mjuka borttagna resursen för att kunna återställa. Åtgärden ta bort tar tillbaka resursen i **stopp skyddet med Behåll data tillstånd** där du kan återställa till en viss tidpunkt. Skräp insamlaren förblir pausad i det här läget.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Kommer mina ögonblicks bilder att följa samma livs cykel som mina återställnings punkter i valvet?

Ja.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hur kan jag aktivera schemalagda säkerhets kopieringar igen för en mjuk borttagen resurs?

Undelete följt av Resume-åtgärden skyddar resursen igen. Återuppta åtgärd associerar en säkerhets kopierings princip för att utlösa de schemalagda säkerhets kopieringarna med den valda kvarhållningsperioden. Dessutom körs skräp insamlaren så fort återställnings åtgärden har slutförts. Om du vill utföra en återställning från en återställnings punkt som har passerat förfallo datumet, rekommenderar vi att du gör det innan du utlöser återställnings åtgärden.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kan jag ta bort mitt valv om det finns mjuka borttagna objekt i valvet?

Det går inte att ta bort Recovery Services valvet om det finns säkerhets kopierings objekt i läget Soft-Deleted i valvet. De mjuka borttagna objekten tas bort permanent 14 dagar efter borttagnings åtgärden. Om du inte kan vänta i 14 dagar [inaktiverar du mjuk borttagning](#disabling-soft-delete), tar bort de mjuka borttagna objekten och tar bort dem igen för att ta bort dem permanent. När du har kontrollerat att det inte finns några skyddade objekt och inga mjuka borttagna objekt, kan valvet tas bort.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kan jag ta bort data som är tidigare än de 14 dagarna mjuk borttagnings perioden efter borttagningen?

Nej. Du kan inte framtvinga borttagning av borttagna objekt, de tas bort automatiskt efter 14 dagar. Den här säkerhetsfunktionen är aktive rad för att skydda säkerhetskopierade data från oavsiktliga eller skadliga borttagningar.  Du bör vänta i 14 dagar innan du utför någon annan åtgärd på den virtuella datorn.  Objekt som inte kan tas bort debiteras inte.  Kontakta Microsoft-supporten om du behöver återskydda de virtuella datorerna som marker ATS för mjuk borttagning inom 14 dagar till ett nytt valv.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kan mjuka borttagnings åtgärder utföras i PowerShell eller CLI?

Mjuk borttagnings åtgärder kan utföras med hjälp av [PowerShell](#soft-delete-for-vms-using-azure-powershell). CLI stöds för närvarande inte.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Stöds mjuk borttagning för andra moln arbets belastningar, t. ex. SQL Server i virtuella Azure-datorer och SAP HANA i virtuella Azure-datorer?

Nej. För tillfället mjuk borttagning stöds bara för virtuella Azure-datorer.

## <a name="next-steps"></a>Nästa steg

- Läs om [säkerhets kontroller för Azure Backup](backup-security-controls.md).
