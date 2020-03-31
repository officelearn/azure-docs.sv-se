---
title: Återställ key vault-nyckeln & hemlighet för krypterad virtuell dator
description: Lär dig hur du återställer Key Vault-nyckel och hemlighet i Azure Backup med PowerShell
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 35bcb919cadd46c603b1f2ad49742c5435f873d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450059"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Återställa nyckel och hemlighet för Key Vault för krypterade virtuella datorer med Azure Backup

Den här artikeln talar om hur du använder Azure VM Backup för att utföra återställning av krypterade Virtuella Azure-datorer, om din nyckel och hemlighet inte finns i nyckelvalvet. Dessa steg kan också användas om du vill behålla en separat kopia av nyckeln (nyckelkrypteringsnyckel) och hemlig (BitLocker-krypteringsnyckel) för den återställda virtuella datorn.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

* **Säkerhetskopierade krypterade virtuella datorer** – krypterade virtuella Azure-datorer har säkerhetskopierats med Azure Backup. Mer information om hur du säkerhetskopierar krypterade virtuella Azure-datorer finns i artikeln [Hantera säkerhetskopiering och återställning av virtuella Azure-datorer](backup-azure-vms-automation.md) med Hjälp av PowerShell.
* **Konfigurera Azure Key Vault** – Se till att nyckelvalvet som nycklar och hemligheter behöver återställas till redan finns. Mer information om hantering av nyckelvalv finns i artikeln [Kom igång med Azure Key Vault.](../key-vault/key-vault-get-started.md)
* **Återställ disk** - Kontrollera att du har utlöst återställningsjobb för återställning av diskar för krypterad virtuell dator med hjälp av [PowerShell-steg](backup-azure-vms-automation.md#restore-an-azure-vm). Detta beror på att det här jobbet genererar en JSON-fil i ditt lagringskonto som innehåller nycklar och hemligheter för den krypterade virtuella datorn som ska återställas.

## <a name="get-key-and-secret-from-azure-backup"></a>Hämta nyckel och hemlighet från Azure Backup

> [!NOTE]
> När disken har återställts för den krypterade virtuella datorn, se till att:
>
> * $details fylls med information om återställning av diskjobb, som nämns i [PowerShell-stegen i Avsnittet Återställ diskar](backup-azure-vms-automation.md#restore-an-azure-vm)
> * Vm ska skapas från återställda diskar först **efter att nyckeln och hemligheten har återställts till nyckelvalvet**.

Fråga de återställda diskegenskaperna för jobbinformationen.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Ange Azure-lagringskontexten och återställ JSON-konfigurationsfilen som innehåller nyckel- och hemliga detaljer för krypterad virtuell dator.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Återställ nyckel

När JSON-filen har genererats i målsökvägen som nämns ovan, generera nyckelblobbfilen från JSON och mata den för att återställa nyckel-cmdlet för att sätta tillbaka nyckeln (KEK) i nyckelvalvet.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Återställ hemlighet

Använd JSON-filen som genereras ovan för att få hemligt namn och värde och mata den för att ställa in hemlig cmdlet för att sätta den hemliga (BEK) tillbaka i nyckelvalvet.Använd dessa cmdlets om den **virtuella datorn är krypterad med BEK och KEK**.

**Använd dessa cmdlets om din virtuella Windows-dator är krypterad med BEK och KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Använd dessa cmdlets om din Virtuella Linux-dator är krypterad med BEK och KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Använd JSON-filen som genereras ovan för att få hemligt namn och värde och mata den för att ställa in hemlig cmdlet för att sätta den hemliga (BEK) tillbaka i nyckelvalvet.Använd dessa cmdlets om den virtuella datorn endast **är krypterad med BEK.**

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * Värdet för $secretname kan erhållas genom att referera till utdata från $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl och använda text efter https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 hemligheter/ t.ex.
> * Värdet för taggen DiskEncryptionKeyFileName är detsamma som det hemliga namnet.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Skapa virtuell dator från återställd disk

Om du har säkerhetskopierat krypterad virtuell dator med Azure VM Backup hjälper PowerShell-cmdlets som nämns ovan dig att återställa nyckeln och hemligheten tillbaka till nyckelvalvet. När du har återställt dem läser du artikeln [Hantera säkerhetskopiering och återställning av virtuella Azure-datorer med PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) för att skapa krypterade virtuella datorer från återställd disk, nyckel och hemlighet.

## <a name="legacy-approach"></a>Äldre tillvägagångssätt

Det tillvägagångssätt som nämns ovan skulle fungera för alla återhämtningspunkter. Den äldre metoden att få viktig och hemlig information från återställningspunkten skulle dock gälla för återställningspunkter som är äldre än den 11 juli 2017 för virtuella datorer krypterade med BEK och KEK. När återställningsdiskjobbet är klart för krypterad virtuell dator med Hjälp av [PowerShell-steg,](backup-azure-vms-automation.md#restore-an-azure-vm)se till att $rp fylls med ett giltigt värde.

### <a name="restore-key"></a>Återställ nyckel

Använd följande cmdlets för att hämta nyckelinformation (KEK) från återställningspunkten och mata in den för att återställa nyckel cmdlet för att lägga tillbaka den i nyckelvalvet.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Återställ hemlighet

Använd följande cmdlets för att få hemlig (BEK) information från återställningspunkten och mata den för att ställa in hemliga cmdlet att sätta tillbaka den i nyckelvalvet.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * Värde för $secretname kan erhållas genom att hänvisa till produktionen av $rp1. KeyAndSecretDetails.SecretUrl och använda text efter hemligheter / t.ex. https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163
> * Värdet för taggen DiskEncryptionKeyFileName är samma som hemligt namn.
> * Värde för DiskEncryptionKeyEncryptionKeyURL kan erhållas från nyckelvalvet efter återställning av nycklarna tillbaka och använda [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) cmdlet
>
>

## <a name="next-steps"></a>Nästa steg

När du har återställt nyckeln och hemligheten tillbaka till nyckelvalvet läser du artikeln [Hantera säkerhetskopiering och återställning av virtuella Azure-datorer med PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) för att skapa krypterade virtuella datorer från återställd disk, nyckel och hemlighet.
