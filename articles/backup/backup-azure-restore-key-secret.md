---
title: "Återställa Key Vault-nyckel och Hemlig för krypterade virtuella datorer med Azure Backup | Microsoft Docs"
description: "Lär dig hur du återställer Key Vault-nyckel och hemlig i Azure Backup med hjälp av PowerShell"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Återställa Key Vault-nyckel och Hemlig för krypterade virtuella datorer med Azure Backup
Den här artikeln handlar om att använda Azure VM säkerhetskopiering för att utföra återställning av krypterade virtuella Azure-datorer om din nyckel och Hemlig inte finns i nyckelvalvet. De här stegen kan också användas om du vill upprätthålla en separat kopia av nyckel (nyckel krypteringsnyckeln) och hemlighet (BitLocker-krypteringsnyckeln) för den återställda virtuella datorn.

## <a name="prerequisites"></a>Krav
* **Säkerhetskopiering krypterade VMs** - krypterade virtuella Azure-datorer har säkerhetskopierats med Azure Backup. Läs artikeln [hantera säkerhetskopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md) mer information om hur du säkerhetskopierar krypterade virtuella Azure-datorer.
* **Konfigurera Azure Key Vault** – Kontrollera att nyckelvalvet som nycklar och hemligheter ska återställas finns redan. Läs artikeln [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md) mer information om hantering av nyckelvalvet.
* **Återställa disk** -Kontrollera att du har utlösts återställningsjobbet för återställning av diskar där krypterade VM [PowerShell steg](backup-azure-vms-automation.md#restore-an-azure-vm). Det beror på att jobbet genererar en JSON-fil i ditt lagringskonto som innehåller nycklar och hemligheter för den kryptera virtuella datorn ska återställas.

## <a name="get-key-and-secret-from-azure-backup"></a>Hämta nyckel och Hemlig från Azure Backup

> [!NOTE]
> När disken har återställts av krypterade VM, kan du se till att:
> 1. $details fylls med jobbinformation för återställning disk som anges i [PowerShell steg för återställning avsnittet diskar](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. VM ska skapas från återställda diskar **när nyckel och Hemlig har återställts i nyckelvalvet**.
>
>

Fråga om återställda disk egenskaperna för Jobbinformationen.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Konfigurera Azure storage-kontexten och återställa JSON-konfigurationsfil som innehåller nyckel och Hemlig information för krypterade VM.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Återställa nyckeln
När JSON-filen har genererats i målsökväg som nämns ovan, generera nyckel blob-fil från JSON och feed det för att återställa nyckeln cmdlet för att placera nyckel (KEK) tillbaka i nyckelvalvet.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Återställa hemlighet
Använd JSON-filen som skapas ovan för att hämta det hemliga namnet och värdet och feed det om du vill ange hemliga för att placera hemligheten (BEK) tillbaka i nyckelvalvet. **Använda dessa cmdletar om den virtuella datorn är krypterad med BEK och KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Om den virtuella datorn är **krypteras med BEK endast**, generera hemliga blob-fil från JSON och feed återställs hemliga cmdlet för att placera hemligheten (BEK) i nyckelvalvet.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. Värdet för $secretname kan hämtas genom att referera till utdataporten för $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl och text efter hemligheter / t.ex. utdata hemliga URL är https://keyvaultname.vault.azure.net/secrets/ B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 och hemliga namnet är B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. Värdet för taggen DiskEncryptionKeyFileName är samma som det hemliga namnet.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Skapa virtuell dator från återställda disk
Om du har säkerhetskopierat krypterade VM som använder Azure VM säkerhetskopiering av PowerShell-cmdlets som nämns ovan hjälp du återställa nyckel och hemliga tillbaka till nyckelvalvet. När du återställer dem finns i artikeln [hantera säkerhetskopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) skapa krypterade virtuella datorer från återställda disk, nyckel och hemlig.

## <a name="legacy-approach"></a>Äldre metod
Den metod som nämns ovan fungerar för alla återställningspunkter. Dock är äldre metod för att få nyckel och Hemlig information från återställningspunkten giltig för återställningspunkter som är äldre än 11 juli 2017 för virtuella datorer som har krypterats med BEK och KEK. När disken Återställningsjobbet har slutförts för krypterade VM med hjälp av [PowerShell steg](backup-azure-vms-automation.md#restore-an-azure-vm), se till att $rp fylls med ett giltigt värde.

### <a name="restore-key"></a>Återställa nyckeln
Använda följande cmdletar för att hämta information om nyckel (KEK) från återställningspunkten och feed det för att återställa nyckeln cmdlet för att placera den i nyckelvalvet.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Återställa hemlighet
Använda följande cmdletar för att hämta information om hemliga (BEK) från återställningspunkten och feed det om du vill ange hemliga för att placera den i nyckelvalvet.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. Värdet för $secretname kan erhållas genom att referera till utdata för $rp1. KeyAndSecretDetails.SecretUrl och via SMS efter hemligheter / t.ex. utdata hemliga URL är https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 och hemliga namnet är B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. Värdet för taggen DiskEncryptionKeyFileName är samma som det hemliga namnet.
> 3. Värdet för DiskEncryptionKeyEncryptionKeyURL kan hämtas från nyckelvalvet efter återställning av nycklar tillbaka och använder [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet
>
>

## <a name="next-steps"></a>Nästa steg
När du återställer nyckel och hemliga tillbaka till nyckelvalvet, finns i artikeln [hantera säkerhetskopiering och återställning av virtuella Azure-datorer med hjälp av PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) skapa krypterade virtuella datorer från återställda disk, nyckel och hemlig.
