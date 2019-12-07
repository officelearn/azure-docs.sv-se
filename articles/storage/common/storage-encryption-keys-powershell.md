---
title: Använd PowerShell för att konfigurera Kundhanterade nycklar
titleSuffix: Azure Storage
description: Lär dig hur du använder PowerShell för att konfigurera Kundhanterade nycklar för Azure Storage kryptering. Med Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomst kontroller.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 87ee96b0f6ad27fc34709f3fc20a2dd69be49089
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895280"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar en Azure Key Vault med Kundhanterade nycklar med hjälp av PowerShell. Information om hur du skapar ett nyckel valv med Azure CLI finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av PowerShell](../../key-vault/quick-create-powershell.md).

> [!IMPORTANT]
> Om du använder Kundhanterade nycklar med Azure Storage kryptering måste du ange två egenskaper för nyckel valvet, **mjuk borttagning** och **Rensa inte**. De här egenskaperna är inte aktiverade som standard. Om du vill aktivera dessa egenskaper använder du antingen PowerShell eller Azure CLI.
> Endast RSA-nycklar och nyckel storlek 2048 stöds.

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela lagrings kontot en identitet

Om du vill aktivera Kundhanterade nycklar för ditt lagrings konto tilldelar du först en systemtilldelad hanterad identitet till lagrings kontot. Du använder den här hanterade identiteten för att ge lagrings kontots behörigheter åtkomst till nyckel valvet.

Om du vill tilldela en hanterad identitet med PowerShell, anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Mer information om hur du konfigurerar systemtilldelade hanterade identiteter med PowerShell finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckel valv

Anropa [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)om du vill skapa ett nytt nyckel valv med PowerShell. Nyckel valvet som du använder för att lagra Kundhanterade nycklar för Azure Storage kryptering måste ha två nyckel skydds inställningar aktiverade, **mjuk borttagning** och **Rensa inte**. 

Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera åtkomst principen för nyckel valvet

Konfigurera sedan åtkomst principen för nyckel valvet så att lagrings kontot har behörighet att komma åt det. I det här steget ska du använda den hanterade identitet som du tidigare tilldelade till lagrings kontot.

Ange åtkomst principen för nyckel valvet genom att anropa [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Skapa en ny nyckel

Skapa sedan en ny nyckel i nyckel valvet. Om du vill skapa en ny nyckel anropar du [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med Kundhanterade nycklar

Som standard använder Azure Storage kryptering Microsoft-hanterade nycklar. I det här steget konfigurerar du ditt Azure Storage-konto så att det använder Kundhanterade nycklar och anger nyckeln som ska associeras med lagrings kontot.

Anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagrings kontots krypterings inställningar. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

När du skapar en ny version av en nyckel måste du uppdatera lagrings kontot för att använda den nya versionen. Börja med att anropa [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) för att hämta den senaste versionen av nyckeln. Anropa sedan [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen av nyckeln, som du ser i föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage kryptering för vilande data](storage-service-encryption.md)
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
