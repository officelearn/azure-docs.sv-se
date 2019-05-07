---
title: Konfigurera Kundhanterade nycklar för kryptering av Azure Storage från PowerShell
description: Lär dig hur du använder PowerShell för att konfigurera Kundhanterade nycklar för kryptering av Azure Storage. Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomstkontroller.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0eeae1451e77d9000c87b1aff7ad73323e74f7ee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154128"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Konfigurera Kundhanterade nycklar för kryptering av Azure Storage från PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar ett nyckelvalv med Kundhanterade nycklar med hjälp av PowerShell.

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela en identitet till storage-kontot

Om du vill använda Kundhanterade nycklar för ditt lagringskonto måste du först tilldela en automatiskt genererad hanterad identitet till storage-kontot. Du använder den här hanterade identiteten för att bevilja behörigheter för storage-konto för åtkomst till key vault.

Om du vill tilldela en hanterad identitet med hjälp av PowerShell anropar [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Kom ihåg att ersätter du platshållarens värden inom hakparentes med dina egna värden.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Mer information om hur du konfigurerar systemtilldelade hanterade identiteter med PowerShell finns i [konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckelvalv

Om du vill skapa ett nytt nyckelvalv med hjälp av PowerShell anropar [New AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Nyckelvalvet som du använder för att lagra Kundhanterade nycklar för Azure Storage kryptering måste ha två nyckelskydd inställningar aktiverade, **mjuk borttagning** och **gör inte rensa**. 

Kom ihåg att ersätter du platshållarens värden inom hakparentes med dina egna värden. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera nyckelvalvets åtkomstprincip

Konfigurera åtkomstprincip för nyckelvalvet så att lagringskontot har åtkomstbehörighet till den. I det här steget ska du använda den hanterade identitet som du tidigare tilldelat till lagringskontot.

För att ställa in åtkomstprincip för nyckelvalvet, anropa [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och om du vill använda de variabler som definierats i föregående exempel.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Skapa en ny nyckel

Skapa sedan en ny nyckel i nyckelvalvet. Om du vill skapa en ny nyckel, anropa [Lägg till AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och om du vill använda de variabler som definierats i föregående exempel.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med Kundhanterade nycklar

Som standard använder Azure Storage kryptering Microsoft-hanterade nycklar. I det här steget konfigurerar du ditt Azure Storage-konto om du vill använda Kundhanterade nycklar och ange nyckeln som ska associeras med lagringskontot.

Anropa [Set-AzStorageAccount](/powershell/module/az.keyvault/set-azstorageaccount) att uppdatera krypteringsinställningar för storage-konto. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden och om du vill använda de variabler som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Uppdatera nyckelns version

När du skapar en ny version av en nyckel, måste du uppdatera lagringskontot för att använda den nya versionen. Först anropa [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) att hämta den senaste versionen av nyckeln. Anropa sedan [Set-AzStorageAccount](/powershell/module/az.keyvault/set-azstorageaccount) att uppdatera krypteringsinställningar för storage-konto om du vill använda den nya versionen av nyckeln, som visas i föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md) 
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
