---
title: Återställa Key Vault-nyckel och hemlighet för krypterade virtuella datorer med Azure Backup
description: Lär dig att återställa Key Vault-nyckel och hemlighet i Azure Backup med hjälp av PowerShell
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: geetha
ms.openlocfilehash: 4ecf8b0808b1eea3af8997caecfdd942f1ff7418
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793692"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Återställa Key Vault-nyckel och hemlighet för krypterade virtuella datorer med Azure Backup

Den här artikeln berättar om hur du använder Azure VM Backup för att utföra återställning av krypterade virtuella Azure-datorer om din nyckel och hemlighet inte finns i nyckelvalvet. De här stegen kan också användas om du vill upprätthålla en separat kopia av nyckel (Nyckelkrypteringsnyckel) och en hemlighet (BitLocker-krypteringsnyckel) för den återställda virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Säkerhetskopiera krypterade virtuella datorer** – krypterade virtuella Azure-datorer har säkerhetskopierats med Azure Backup. Se artikeln [hantera säkerhetskopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md) mer information om hur du säkerhetskopierar krypterade virtuella Azure-datorer.
* **Konfigurera Azure Key Vault** – se till att det nyckelvalv som nycklar och hemligheter måste återställas finns redan. Se artikeln [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md) mer information om hantering av nyckelvalv.
* **Återställa disk** – se till att du har utlöst återställningsjobbet för att återställa diskar för krypterade virtuella datorn med [steg för PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Det beror på att det här jobbet genererar en JSON-fil i ditt storage-konto som innehåller nycklar och hemligheter för den krypterade virtuella datorn som ska återställas.

## <a name="get-key-and-secret-from-azure-backup"></a>Hämta nyckel och hemlighet från Azure Backup

> [!NOTE]
> När disken har återställts för den krypterade virtuella datorn, kan du se till att:
> * $details fylls med återställning för jobbet diskinformation som vi nämnde i [PowerShell steg i Restore-avsnittet diskar](backup-azure-vms-automation.md#restore-an-azure-vm)
> * Virtuell dator bör skapas från återställda diskar **när nyckel och hemlighet har återställts till nyckelvalvet**.
>
>

Fråga återställda disken egenskaperna för Jobbinformationen.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Konfigurera Azure storage-kontext och återställa JSON-konfigurationsfil som innehåller nyckeln och hemliga information för krypterade virtuella datorn.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Återställ nyckeln

När JSON-filen har genererats i den målsökväg som nämns ovan, generera nyckel blob-fil från JSON-filen och skicka för att återställa viktiga cmdlet om du vill placera nyckel (KEK) tillbaka i nyckelvalvet.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Återställa hemlighet

Använd JSON-fil som genererats ovan för att hämta hemliga namn och värde och skicka om du vill ange hemlig cmdlet för att placera hemlighet (BEK) tillbaka i nyckelvalvet. Använd dessa cmdletar om din **virtuell dator har krypterats med BEK och KEK**.

**Använd dessa cmdletar om din virtuella Windows-dator har krypterats med BEK och KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Använd dessa cmdletar om din Linux-VM är krypterat med BEK och KEK.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Använd JSON-fil som genererats ovan för att hämta hemliga namn och värde och skicka om du vill ange hemlig cmdlet för att placera hemlighet (BEK) tillbaka i nyckelvalvet. Använd dessa cmdletar om din **virtuell dator har krypterats med BEK** endast.

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
> * Värdet för $secretname kan erhållas genom refererar till utdatan från $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl och använda text efter hemligheter / t.ex. utdata hemliga URL: en är https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 och hemliga namn är B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Värdet för taggen DiskEncryptionKeyFileName är samma som namn på hemlighet.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Skapa virtuell dator från återställda disken

Om du har säkerhetskopierat krypterade virtuella datorn med hjälp av Azure VM Backup PowerShell-cmdlets som nämns ovan kan du återställa nyckel och hemliga tillbaka till nyckelvalvet. Efter återställning dem, finns i artikeln [hantera säkerhetskopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) skapar krypterade virtuella datorer från återställda disken, nyckel och hemlighet.

## <a name="legacy-approach"></a>Äldre metoden

Den metod som nämns ovan skulle fungera för alla återställningspunkter. Den äldre metoden för att få nyckel och Hemlig information från återställningspunkten, skulle dock vara giltigt för återställningspunkter som är äldre än den 11 juli 2017 för virtuella datorer som har krypterats med BEK och KEK. När disken Återställningsjobbet har slutförts för krypterade virtuella datorn med [steg för PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), se till att $rp fylls med ett giltigt värde.

### <a name="restore-key"></a>Återställ nyckeln

Du kan använda följande cmdletar för att hämta nyckel (KEK) information från återställningspunkten och skicka om du vill återställa viktiga cmdlet om du vill placera den i nyckelvalvet.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Återställa hemlighet

Du kan använda följande cmdletar för att hämta hemlig (BEK) information från återställningspunkten och skicka om du vill ange hemlig cmdlet för att placera den i nyckelvalvet.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> * Värdet för $secretname kan hämtas genom att referera till utdata för $rp1. KeyAndSecretDetails.SecretUrl och använda text efter hemligheter / t.ex. utdata hemlighet URL: en är https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 och hemliga namn är B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * Värdet för taggen DiskEncryptionKeyFileName är samma som namn på hemlighet.
> * Värdet för DiskEncryptionKeyEncryptionKeyURL kan hämtas från key vault efter återställning av nycklar tillbaka och använder [Get-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) cmdlet
>
>

## <a name="next-steps"></a>Nästa steg

När du återställer nyckel och hemliga tillbaka till nyckelvalvet, finns i artikeln [hantera säkerhetskopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) skapar krypterade virtuella datorer från återställda disken, nyckel och hemlighet.