---
title: Säkerhetsfunktioner för att skydda molnarbetsbelastningar
description: Lär dig hur du använder säkerhetsfunktioner i Azure Backup för att göra säkerhetskopior säkrare.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668744"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Säkerhetsfunktioner för att skydda molnarbetsbelastningar som använder Azure Backup

Oron för säkerhetsfrågor, som skadlig kod, ransomware och intrång, ökar. Dessa säkerhetsfrågor kan bli kostsamma, både när det gäller pengar och data. För att skydda mot sådana attacker tillhandahåller Azure Backup nu säkerhetsfunktioner för att skydda säkerhetskopierade data även efter borttagning.

En sådan funktion är mjuk bort. Med mjuk borttagning, även om en skadlig aktör tar bort säkerhetskopian av en virtuell dator (eller säkerhetskopieringsdata tas bort av misstag), sparas säkerhetskopieringsdata i ytterligare 14 dagar, vilket gör att säkerhetskopieringen av det säkerhetskopierade objektet inte kan återställas utan dataförlust. Den ytterligare 14 dagars lagring av säkerhetskopierade data i tillståndet "mjuk borttagning" medför inga kostnader för kunden. Azure krypterar också alla säkerhetskopierade data i vila med [lagringstjänstkryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) för att ytterligare skydda dina data.

Soft delete protection för virtuella Azure-datorer är allmänt tillgängligt.

>[!NOTE]
>Mjuk borttagning för SQL-server i Azure VM och mjuk borttagning för SAP HANA i Azure VM-arbetsbelastningar är nu tillgänglig i förhandsversion.<br>
>För att registrera dig för förhandsgranskningen, skriv till oss påAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Mjuk borttagning

### <a name="soft-delete-for-vms"></a>Mjuk borttagning för virtuella datorer

Mjuk borttagning för virtuella datorer skyddar säkerhetskopiorna för dina virtuella datorer från oavsiktlig borttagning. Även efter att säkerhetskopiorna har tagits bort bevaras de i mjukt borttagningstillstånd i ytterligare 14 dagar.

> [!NOTE]
> Mjuk borttagning skyddar bara borttagna säkerhetskopierade data. Om en virtuell dator tas bort utan säkerhetskopiering bevaras inte data. Alla resurser bör skyddas med Azure Backup för att säkerställa fullständig återhämtning.
>

### <a name="supported-regions"></a>Regioner som stöds

Mjuk borttagning stöds för närvarande i västra centrala USA, Östra Asien, Kanada Central, Kanada Öst, Frankrike Central, Frankrike Syd, Korea Central, Korea Syd, Storbritannien Syd, Storbritannien Väst, Australien Öst, Australien Sydöstra, Norra Europa, Västra USA, Västra USA2, Centrala USA, Sydostasien, Norra centrala USA, Södra centrala USA, Japan Öst, Japan Väst, Indien Syd, Indien Central, Indien Väst, Östra USA 2 , Schweiz North, Schweiz West, Norge Väst, Norge Öst och alla nationella regioner.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Mjuk borttagning för virtuella datorer med Azure-portal

1. Om du vill ta bort säkerhetskopierade data för en virtuell dator måste säkerhetskopian stoppas. Gå till ditt återhämtningstjänster-valv i Azure-portalen, högerklicka på säkerhetskopian och välj **Stoppa säkerhetskopiering**.

   ![Skärmbild av säkerhetsobjekt för Azure Portal](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. I följande fönster får du välja att ta bort eller behålla säkerhetskopierade data. Om du väljer **Ta bort säkerhetskopierade data** och sedan Stoppa **säkerhetskopiering**tas inte säkerhetskopian av den virtuella datorn bort permanent. I stället kommer säkerhetskopieringsdata att behållas i 14 dagar i mjukt borttaget tillstånd. Om **Ta bort säkerhetskopierade data** väljs skickas en avisering om borttagning av e-post till det konfigurerade e-post-ID:t som informerar användaren om att 14 dagar återstår av utökad kvarhållning för säkerhetskopieringsdata. Dessutom skickas en e-postavisering den 12:e dagen med information om att det finns ytterligare två dagar kvar att återuppliva de borttagna uppgifterna. Borttagningen skjuts upp till den 15:e dagen, när permanent radering kommer att ske och en slutlig e-postavisering skickas med information om permanent radering av data.

   ![Skärmbild av Azure Portal, Stoppa säkerhetskopieringsskärmen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Under dessa 14 dagar, i Recovery Services Vault, visas den mjuka borttagna virtuella datorn med en röd "soft-delete"-ikon bredvid den.

   ![Skärmbild av Azure Portal, VM i mjukt borttagningstillstånd](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Om det finns några borttagna säkerhetskopieringsobjekt i valvet kan valvet inte tas bort vid den tidpunkten. Försök att ta bort valv när säkerhetskopieringsobjekten har tagits bort permanent och det finns inget objekt i mjukt borttaget tillstånd kvar i valvet.

4. Om du vill återställa den borttagna virtuella datorn måste den först tas bort. Om du vill ta bort om e-post väljer du den mjuka borttagna virtuella datorn och väljer sedan alternativet **Ta bort.**

   ![Skärmbild av Azure Portal, Undelete VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Ett fönster visas som varnar för att om om du väljer om inteeleter väljs kommer alla återställningspunkter för den virtuella datorn att tas bort och vara tillgängliga för att utföra en återställningsåtgärd. Den virtuella datorn kommer att behållas i ett "stoppskydd med behålla data" tillstånd med säkerhetskopior pausas och säkerhetskopieringsdata lagras för alltid utan backup princip effektiv.

   ![Skärmbild av Azure Portal, Bekräfta omende av vm](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Nu kan du också återställa den virtuella datorn genom att välja **Återställ virtuell dator** från den valda återställningspunkten.  

   ![Skärmbild av Azure Portal, Alternativet Återställ virtuell dator](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Skräpinsamlaren körs och rensar återställningspunkter som har upphört att gälla först när användaren har säkerhetskopiering av **återupptagning.**

5. När processen för att avslutas återgår statusen till "Stoppa säkerhetskopiering med lagringsdata" och sedan kan du välja **Återuppta säkerhetskopiering**. Återuppta **säkerhetskopieringsåtgärden** återför säkerhetskopieringsobjektet i aktivt tillstånd, associerat med en princip för säkerhetskopiering som valts av användaren som definierar säkerhetskopierings- och kvarhållningsscheman.

   ![Skärmbild av Azure Portal, alternativet Återuppta säkerhetskopiering](./media/backup-azure-security-feature-cloud/resume-backup.png)

Det här flödesschemat visar de olika stegen och lägena för ett säkerhetskopieringsobjekt när Mjuk borttagning är aktiverat:

![Livscykel för säkerhetskopieringsobjekt som tagits bort för mjukt](./media/backup-azure-security-feature-cloud/lifecycle.png)

Mer information finns i avsnittet [Vanliga frågor](backup-azure-security-feature-cloud.md#frequently-asked-questions) nedan.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Mjuk borttagning för virtuella datorer med Azure PowerShell

> [!IMPORTANT]
> Den Az.RecoveryServices-version som krävs för att använda mjuk borttagning med Azure PS är min 2.2.0. Används ```Install-Module -Name Az.RecoveryServices -Force``` för att få den senaste versionen.

Som beskrivs ovan för Azure-portalen är sekvensen av steg samma när du använder Azure PowerShell också.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Ta bort säkerhetskopian med Azure PowerShell

Ta bort säkerhetskopian med hjälp av [filen Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

DeleteState-objektets "DeleteState" ändras från "NotDeleted" till "ToBeDeleted". Säkerhetskopieringsdata kommer att behållas i 14 dagar. Om du vill återställa borttagningsåtgärden ska ångra-borttagning utföras.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Ångra borttagningsåtgärden med Azure PowerShell

Hämta först det relevanta säkerhetskopieringsobjektet som är i mjukt borttagningsläge (det vill säga på väg att tas bort).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Utför sedan ångra-borttagningen med hjälp av cmdleten [Ångra-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

DeleteState för säkerhetskopieringsobjektet återgår till NotDeleted. Men skyddet är fortfarande stoppat. [Återuppta säkerhetskopian](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) för att återaktivera skyddet.

### <a name="soft-delete-for-vms-using-rest-api"></a>Mjuk borttagning för virtuella datorer med REST API

- Ta bort säkerhetskopior med REST API som nämns [här](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Om användaren vill ångra dessa borttagningsåtgärder läser du stegen som nämns [här](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Inaktivera mjuk borttagning

Mjuk borttagning är aktiverat som standard på nyskapade valv för att skydda säkerhetskopierade data från oavsiktliga eller skadliga borttagningar.  Det rekommenderas inte att inaktivera den här funktionen. Den enda omständighet där du bör överväga att inaktivera mjuk borttagning är om du planerar att flytta dina skyddade objekt till ett nytt valv och inte kan vänta de 14 dagar som krävs innan du tar bort och skyddar igen (till exempel i en testmiljö.) Endast valvägaren kan inaktivera den här funktionen. Om du inaktiverar den här funktionen kommer alla framtida borttagningar av skyddade objekt att resultera i omedelbar borttagning, utan att du kan återställa dem. Säkerhetskopierade data som finns i mjukt borttaget läge innan den här funktionen inaktiveras förblir i mjukt borttaget tillstånd under en period av 14 dagar. Om du vill ta bort dessa permanent omedelbart, måste du ta bort och ta bort dem igen för att få permanent bort.

### <a name="disabling-soft-delete-using-azure-portal"></a>Inaktivera mjuk borttagning med Azure-portal

Så här inaktiverar du mjuk borttagning:

1. Gå till valvet i Azure-portalen och gå sedan till **Egenskaper för Inställningar** -> **.**
2. Välj**Uppdatering**av **säkerhetsinställningar** -> i egenskapsfönstret .  
3. Välj **Inaktivera**under Mjuk borttagning under **Mjuk borttagning**i fönstret Säkerhetsinställningar .

![Inaktivera mjuk borttagning](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Inaktivera mjuk borttagning med Azure PowerShell

> [!IMPORTANT]
> Den Az.RecoveryServices-version som krävs för att använda mjuk borttagning med Azure PS är min 2.2.0. Används ```Install-Module -Name Az.RecoveryServices -Force``` för att få den senaste versionen.

Om du vill inaktivera använder du cmdleten [Set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Inaktivera mjuk borttagning med REST API

Om du vill inaktivera funktionen för mjuk borttagning med REST API läser du stegen som nämns [här](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Ta bort mjuka borttagna säkerhetskopieringsobjekt permanent

Säkerhetskopierade data i mjukt borttaget tillstånd innan du inaktiverar den här funktionen förblir i mjukt borttaget tillstånd. Om du vill ta bort dessa permanent omedelbart, sedan ångra och ta bort dem igen för att få permanent bort.

### <a name="using-azure-portal"></a>Använda Azure Portal

Följ de här stegen:

1. Följ stegen för att [inaktivera mjuk borttagning](#disabling-soft-delete).
2. Gå till valvet i Azure-portalen, gå till **säkerhetskopieringsobjekt**och välj den mjuka borttagna virtuella datorn.

   ![Välj mjuk borttagen virtuell dator](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Välj alternativet **Avmarkera**.

   ![Välj Ta bort ereta](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Ett fönster visas. Välj **Ta bort omet .**

   ![Välj Ta bort ereta](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Välj **Ta bort säkerhetskopierade data** om du vill ta bort säkerhetskopierade data permanent.

   ![Välj Ta bort säkerhetskopierade data](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Skriv namnet på säkerhetskopian för att bekräfta att du vill ta bort återställningspunkterna.

   ![Skriv namnet på säkerhetskopian](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Om du vill ta bort säkerhetskopierade data för objektet väljer du **Ta bort**. Ett meddelande visar att säkerhetskopieringsdata har tagits bort.

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Om objekt togs bort innan mjuk borttagning inaktiverades, kommer de att vara i ett mjukt borttaget tillstånd. Om du vill ta bort dem omedelbart måste borttagningen återföras och sedan utföras igen.

Identifiera de objekt som är i mjukt borttaget tillstånd.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Återför sedan borttagningsåtgärden som utfördes när mjuk borttagning aktiverades.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Eftersom den mjuka borttagningen nu är inaktiverad, kommer borttagningen att resultera i omedelbar borttagning av säkerhetskopierade data.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Använda REST-API:et

Om objekt togs bort innan mjuk borttagning inaktiverades, kommer de att vara i ett mjukt borttaget tillstånd. Om du vill ta bort dem omedelbart måste borttagningen återföras och sedan utföras igen.

1. Ångra först borttagningsåtgärderna med stegen som nämns [här](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Inaktivera sedan funktionen för mjuk borttagning med REST API med hjälp av stegen som nämns [här](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Ta sedan bort säkerhetskopior med REST API som nämns [här](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Kryptering

Alla dina säkerhetskopierade data krypteras automatiskt när de lagras i molnet med Azure Storage-kryptering, vilket hjälper dig att uppfylla dina säkerhets- och efterlevnadsåtaganden. Dessa data i vila krypteras med 256-bitars AES-kryptering, en av de starkaste blockchiffer tillgängliga, och är FIPS 140-2-kompatibel.

Förutom kryptering i vila överförs alla säkerhetskopierade data under överföring via HTTPS. Den finns alltid kvar i Azure-stamnätet.

Mer information finns i [Azure Storage-kryptering för data i vila](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Se [vanliga frågor och svar](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) på Azure Backup för att svara på eventuella frågor som du kan ha om kryptering.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Kryptering av säkerhetskopierade data med hjälp av plattformshanterade nycklar

Som standard krypteras alla dina data med plattformshanterade nycklar. Du behöver inte vidta några explicita åtgärder från din sida för att aktivera den här krypteringen och det gäller för alla arbetsbelastningar som säkerhetskopieras till ditt Recovery Services-valv.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Kryptering av säkerhetskopierade data med kundhanterade nycklar

När du säkerhetskopierar dina virtuella Azure-datorer kan du nu kryptera dina data med hjälp av nycklar som ägs och hanteras av dig. Med Azure Backup kan du använda dina RSA-nycklar som lagras i Azure Key Vault för att kryptera dina säkerhetskopior. Krypteringsnyckeln som används för att kryptera säkerhetskopior kan skilja sig från den som används för källan. Data skyddas med hjälp av en AES 256-baserad datakrypteringsnyckel (DEK), som i sin tur är skyddad med hjälp av dina nycklar. Detta ger dig full kontroll över data och nycklar. För att tillåta kryptering krävs att Recovery Services-valvet beviljas åtkomst till krypteringsnyckeln i Azure Key Vault. Du kan inaktivera nyckeln eller återkalla åtkomsten när det behövs. Du måste dock aktivera kryptering med hjälp av dina nycklar innan du försöker skydda objekt till valvet.

>[!NOTE]
>Den här funktionen är för närvarande i begränsad tillgänglighet. Fyll i [denna undersökning](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) och AskAzureBackupTeam@microsoft.com maila oss på om du vill kryptera dina säkerhetskopierade data med hjälp av kundhanterade nycklar. Observera att möjligheten att använda den här funktionen är föremål för godkännande från Azure Backup-tjänsten.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Säkerhetskopiering av hanterade disk-virtuella datorer krypterade med kundhanterade nycklar

Med Azure Backup kan du också säkerhetskopiera dina virtuella Azure-datorer som använder nyckeln för kryptering på serversidan. Nyckeln som används för att kryptera diskarna lagras i Azure Key Vault och hanteras av dig. Server-side kryptering med hjälp av kundhanterade nycklar skiljer sig från Azure Disk Encryption, eftersom ADE utnyttjar BitLocker (för Windows) och DM-Crypt (för Linux) för att utföra gästkryptering, SSE krypterar data i lagringstjänsten, så att du kan använda alla operativsystem eller avbildningar för dina virtuella datorer. Mer information finns i [Kryptering av hanterade diskar med kundhanterade nycklar.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)

### <a name="backup-of-vms-encrypted-using-ade"></a>Säkerhetskopiering av virtuella datorer krypterade med ADE

Med Azure Backup kan du också säkerhetskopiera dina Virtuella Azure-datorer som har sina operativsystem eller datadiskar krypterade med Azure Disk Encryption. ADE använder BitLocker för Virtuella Windows-datorer och DM-Crypt för virtuella Linux-datorer för att utföra gästkryptering. Mer information finns i [Säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="private-endpoints"></a>Privata slutpunkter

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Andra säkerhetsfunktioner

### <a name="protection-of-azure-backup-recovery-points"></a>Skydd av återställningspunkter för Azure Backup

Lagringskonton som används av återställningstjänster är isolerade och kan inte nås av användare för skadliga ändamål. Åtkomsten tillåts endast via Azure Backup-hanteringsåtgärder, till exempel återställning. Dessa hanteringsåtgärder styrs genom rollbaserad åtkomstkontroll (RBAC).

Mer information finns i [Använda rollbaserad åtkomstkontroll för att hantera återställningspunkter för Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="for-soft-delete"></a>För Mjuk borttagning

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Måste jag aktivera funktionen för mjuk borttagning i varje valv?

Nej, den är byggd och aktiverad som standard för alla återställningstjänster valv.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Kan jag konfigurera hur många dagar mina data ska lagras i mjukt borttaget tillstånd när borttagningen är klar?

Nej, den är fast till 14 dagars ytterligare kvarhållning efter borttagningen.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Måste jag betala kostnaden för denna extra 14-dagars kvarhållning?

Nej, den här ytterligare kvarhållningen på 14 dagar kostar kostnadsfritt som en del av mjukborttagningsfunktioner.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kan jag utföra en återställningsåtgärd när mina data är i mjukt borttagningsläge?

Nej, du måste ta bort den mjuka borttagna resursen för att återställa den. Den undelete åtgärden kommer att föra resursen tillbaka till **stoppskydd med behålla datatillstånd** där du kan återställa till någon tidpunkt. Sophämtaren förblir pausad i det här tillståndet.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Kommer mina ögonblicksbilder att följa samma livscykel som mina återställningspunkter i valvet?

Ja.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hur kan jag utlösa schemalagda säkerhetskopior igen för en mjuk borttagen resurs?

Om du tar bort den följt av återupptar åtgärden skyddas resursen igen. Återuppta åtgärden associerar en säkerhetskopieringsprincip för att utlösa schemalagda säkerhetskopior med den valda kvarhållningsperioden. Dessutom körs skräpuppsamlaren så fort återuppta-åtgärden är klar. Om du vill utföra en återställning från en återställningspunkt som är förbi utgångsdatumet, rekommenderas du att göra det innan du utlöser återuppta-åtgärden.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kan jag ta bort valvet om det finns mjuka borttagna objekt i valvet?

Valvet för Återställningstjänster kan inte tas bort om det finns säkerhetskopieringsobjekt i mjukt borttaget tillstånd i valvet. De borttagna objekten tas bort permanent 14 dagar efter borttagningen. Om du inte kan vänta i 14 dagar inaktiverar du [mjuk borttagning](#disabling-soft-delete), tar bort de mjuka borttagna objekten och tar bort dem igen för att tas bort permanent. När du har sett till att det inte finns några skyddade objekt och inga mjuka borttagna objekt kan valvet tas bort.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kan jag ta bort data tidigare än den 14 dagar lång tid som inte tagits bort efter borttagningen?

Nej. Du kan inte tvinga bort de borttagna objekten, de tas bort automatiskt efter 14 dagar. Den här säkerhetsfunktionen är aktiverad för att skydda säkerhetskopierade data från oavsiktliga eller skadliga borttagningar.  Du bör vänta i 14 dagar innan du utför någon annan åtgärd på den virtuella datorn.  Mjuka borttagna objekt debiteras.  Om du behöver återbeskydda de virtuella datorerna som är markerade för att ta bort dem inom 14 dagar till ett nytt valv kontaktar du Microsoft-supporten.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kan mjuka borttagningsåtgärder utföras i PowerShell eller CLI?

Mjuka borttagningsåtgärder kan utföras med [PowerShell](#soft-delete-for-vms-using-azure-powershell). För närvarande stöds inte CLI.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Stöds mjuk borttagning för andra molnarbetsbelastningar, till exempel SQL Server i virtuella Azure-datorer och SAP HANA i virtuella Azure-datorer?

Nej. För närvarande stöds mjuk borttagning endast för virtuella Azure-datorer.

## <a name="next-steps"></a>Nästa steg

- Läs om [säkerhetskontroller för Azure Backup](backup-security-controls.md).
