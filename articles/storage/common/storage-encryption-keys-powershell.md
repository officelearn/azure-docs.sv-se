---
title: Använd PowerShell för att konfigurera Kundhanterade nycklar
titleSuffix: Azure Storage
description: Lär dig hur du använder PowerShell för att konfigurera Kundhanterade nycklar för Azure Storage kryptering.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a3fdde755a5e024efead5c8861a1d5cd769b6d23
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036836"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar en Azure Key Vault med Kundhanterade nycklar med hjälp av PowerShell. Information om hur du skapar ett nyckel valv med Azure CLI finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av PowerShell](../../key-vault/secrets/quick-create-powershell.md).

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

Om du vill skapa ett nytt nyckel valv med hjälp av PowerShell installerar du version 2.0.0 eller senare av PowerShell-modulen [AZ. Key Vault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) . Anropa sedan [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) för att skapa ett nytt nyckel valv.

Nyckel valvet som du använder för att lagra Kundhanterade nycklar för Azure Storage kryptering måste ha två nyckel skydds inställningar aktiverade, **mjuk borttagning** och **Rensa inte**. I version 2.0.0 och senare av modulen AZ. Key Vault är mjuk borttagning aktiverat som standard när du skapar ett nytt nyckel valv.

I följande exempel skapas ett nytt nyckel valv med den **mjuka borttagningen** och **rensnings** egenskaperna är inte aktiverade. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Information om hur du aktiverar **mjuk borttagning** och **inte rensar** på ett befintligt nyckel valv med PowerShell finns i avsnitten med rubriken **Aktivera mjuk borttagning** och **Aktivera rensnings skydd** i [använda mjuk borttagning med PowerShell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera åtkomst principen för nyckel valvet

Konfigurera sedan åtkomst principen för nyckel valvet så att lagrings kontot har behörighet att komma åt det. I det här steget ska du använda den hanterade identitet som du tidigare tilldelade till lagrings kontot.

Ange åtkomst principen för nyckel valvet genom att anropa [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Skapa en ny nyckel

Skapa sedan en ny nyckel i nyckel valvet. Om du vill skapa en ny nyckel anropar du [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Azure Storage-kryptering stöder RSA-och RSA-HSM-nycklar i storlekarna 2048, 3072 och 4096. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med Kundhanterade nycklar

Som standard använder Azure Storage kryptering Microsoft-hanterade nycklar. I det här steget konfigurerar du ditt Azure Storage-konto så att det använder Kundhanterade nycklar med Azure Key Vault och anger sedan nyckeln som ska associeras med lagrings kontot.

När du konfigurerar kryptering med Kundhanterade nycklar kan du välja att automatiskt rotera nyckeln som används för kryptering när versionen ändras i det associerade nyckel valvet. Alternativt kan du uttryckligen ange en nyckel version som ska användas för kryptering tills nyckel versionen uppdateras manuellt.

### <a name="configure-encryption-for-automatic-rotation-of-customer-managed-keys"></a>Konfigurera kryptering för automatisk rotation av Kundhanterade nycklar

Konfigurera kryptering för automatisk rotation av Kundhanterade nycklar genom att installera modulen [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) , version 2.0.0 eller senare.

Om du vill rotera Kundhanterade nycklar automatiskt, utelämnar du nyckel versionen när du konfigurerar Kundhanterade nycklar för lagrings kontot. Anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagrings kontots krypterings inställningar, som visas i följande exempel, och inkludera alternativet **-KeyvaultEncryption** för att aktivera Kundhanterade nycklar för lagrings kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-rotation-of-key-versions"></a>Konfigurera kryptering för manuell rotation av nyckel versioner

Ange nyckel versionen när du konfigurerar kryptering med Kundhanterade nycklar för lagrings kontot om du uttryckligen vill ange en nyckel version som ska användas för kryptering. Anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagrings kontots krypterings inställningar, som visas i följande exempel, och inkludera alternativet **-KeyvaultEncryption** för att aktivera Kundhanterade nycklar för lagrings kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

När du roterar nyckel versionen manuellt måste du uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen. Börja med att anropa [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) för att hämta den senaste versionen av nyckeln. Anropa sedan [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen av nyckeln, som du ser i föregående exempel.

## <a name="use-a-different-key"></a>Använd en annan nyckel

Om du vill ändra den nyckel som används för Azure Storage kryptering, anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) som visas i [Konfigurera kryptering med Kundhanterade nycklar](#configure-encryption-with-customer-managed-keys) och ange det nya nyckel namnet och den nya versionen. Om den nya nyckeln finns i ett annat nyckel valv uppdaterar du även Key Vault-URI: n.

## <a name="revoke-customer-managed-keys"></a>Återkalla Kundhanterade nycklar

Du kan återkalla Kundhanterade nycklar genom att ta bort åtkomst principen för nyckel valvet. Om du vill återkalla en kundhanterad nyckel anropar du kommandot [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , som du ser i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Inaktivera Kundhanterade nycklar

När du inaktiverar Kundhanterade nycklar är ditt lagrings konto återigen krypterat med Microsoft-hanterade nycklar. Du inaktiverar Kundhanterade nycklar genom att anropa [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) med `-StorageEncryption` alternativet, som du ser i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
