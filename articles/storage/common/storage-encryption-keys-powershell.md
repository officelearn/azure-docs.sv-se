---
title: Använda PowerShell för att konfigurera kundhanterade nycklar
titleSuffix: Azure Storage
description: Lär dig hur du använder PowerShell för att konfigurera kundhanterade nycklar för Azure Storage-kryptering.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 264dbbaedca5a28c8741d699a683b3e2b2385383
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061147"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Konfigurera kundhanterade nycklar med Azure Key Vault med PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar ett Azure Key Vault med kundhanterade nycklar med PowerShell. Mer information om hur du skapar ett nyckelvalv med Azure CLI finns i [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med PowerShell](../../key-vault/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela en identitet till lagringskontot

Om du vill aktivera kundhanterade nycklar för ditt lagringskonto tilldelar du först en systemtilldelad hanterad identitet till lagringskontot. Du ska använda den här hanterade identiteten för att bevilja lagringskontobehörigheter för åtkomst till nyckelvalvet.

Om du vill tilldela en hanterad identitet med PowerShell anropar du [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Mer information om hur du konfigurerar systemtilldelade hanterade identiteter med PowerShell finns i [Konfigurera hanterade identiteter för Azure-resurser på en Azure VM med PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckelvalv

Om du vill skapa ett nytt nyckelvalv med PowerShell anropar du [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Nyckelvalvet som du använder för att lagra kundhanterade nycklar för Azure Storage-kryptering måste ha två nyckelskyddsinställningar aktiverade, **Mjuk borttagning** och **Rensa inte**.

Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Mer information om hur du aktiverar **Mjuk borttagning** och **rensar inte** på ett befintligt nyckelvalv med PowerShell finns i avsnitten **Aktivera mjuk borttagning** och **Aktivera rensningsskydd** i [Så här använder du mjuk borttagning med PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera principen för nyckelvalvsåtkomst

Konfigurera sedan åtkomstprincipen för nyckelvalvet så att lagringskontot har behörighet att komma åt det. I det här steget ska du använda den hanterade identitet som du tidigare tilldelat lagringskontot.

Om du vill ange åtkomstprincipen för nyckelvalvet anropar du [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden och använda de variabler som definierats i föregående exempel.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Skapa en ny nyckel

Skapa sedan en ny nyckel i nyckelvalvet. Om du vill skapa en ny nyckel anropar du [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden och använda de variabler som definierats i föregående exempel.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med kundhanterade nycklar

Som standard använder Azure Storage-kryptering Microsoft-hanterade nycklar. I det här steget konfigurerar du ditt Azure Storage-konto så att det använder kundhanterade nycklar och anger nyckeln som ska associeras med lagringskontot.

Ring [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagringskontots krypteringsinställningar, vilket visas i följande exempel. Inkludera alternativet **-KeyvaultEncryption** för att aktivera kundhanterade nycklar för lagringskontot. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden och använda de variabler som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Uppdatera nyckelversionen

När du skapar en ny version av en nyckel måste du uppdatera lagringskontot för att kunna använda den nya versionen. Anropa först [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) för att få den senaste versionen av nyckeln. Anropa sedan [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagringskontots krypteringsinställningar för att använda den nya versionen av nyckeln, som visas i föregående avsnitt.

## <a name="use-a-different-key"></a>Använda en annan nyckel

Om du vill ändra nyckeln som används för Azure [Storage-kryptering anropar du Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) som visas i [Konfigurera kryptering med kundhanterade nycklar](#configure-encryption-with-customer-managed-keys) och anger det nya nyckelnamnet och versionen. Om den nya nyckeln finns i ett annat nyckelvalv uppdaterar du även uri-kort för nyckelvalvet.

## <a name="revoke-customer-managed-keys"></a>Återkalla kundhanterade nycklar

Om du tror att en nyckel kan ha komprometterats kan du återkalla kundhanterade nycklar genom att ta bort åtkomstprincipen för nyckelvalvet. Om du vill återkalla en kundhanterad nyckel anropar du kommandot [Ta bort AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) som visas i följande exempel. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden och använda de variabler som definierats i föregående exempel.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Inaktivera kundhanterade nycklar

När du inaktiverar kundhanterade nycklar krypteras ditt lagringskonto återigen med Microsoft-hanterade nycklar. Om du vill inaktivera kundhanterade nycklar anropar `-StorageEncryption` du [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) med alternativet, som visas i följande exempel. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden och använda de variabler som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering för data i vila](storage-service-encryption.md)
- [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
