---
title: Konfigurera Kundhanterade nycklar med hjälp av Azure CLI
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure CLI för att konfigurera Kundhanterade nycklar med Azure Key Vault för Azure Storage kryptering.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d6fa4bbaf9b37c93ef4efbe405087c39395df63d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086021"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar en Azure Key Vault med Kundhanterade nycklar med hjälp av Azure CLI. Information om hur du skapar ett nyckel valv med Azure CLI finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med Azure CLI](../../key-vault/secrets/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela lagrings kontot en identitet

Om du vill aktivera Kundhanterade nycklar för ditt lagrings konto tilldelar du först en systemtilldelad hanterad identitet till lagrings kontot. Du använder den här hanterade identiteten för att ge lagrings kontots behörigheter åtkomst till nyckel valvet.

Om du vill tilldela en hanterad identitet med Azure CLI anropar du [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Mer information om hur du konfigurerar systemtilldelade hanterade identiteter med Azure CLI finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckel valv

Nyckel valvet som du använder för att lagra Kundhanterade nycklar för Azure Storage kryptering måste ha två nyckel skydds inställningar aktiverade, **mjuk borttagning** och **Rensa inte**. Om du vill skapa ett nytt nyckel valv med hjälp av PowerShell eller Azure CLI med de här inställningarna aktiverade kör du följande kommandon. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

Skapa ett nytt nyckel valv med Azure CLI genom att anropa [AZ](/cli/azure/keyvault#az-keyvault-create)-nyckelpar Create. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Information om hur du aktiverar **mjuk borttagning** och **inte rensar** på ett befintligt nyckel valv med Azure CLI finns i avsnitten med rubriken **Aktivera mjuk borttagning** och **Aktivera rensnings skydd** i [använda mjuk borttagning med CLI](../../key-vault/general/soft-delete-cli.md).

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera åtkomst principen för nyckel valvet

Konfigurera sedan åtkomst principen för nyckel valvet så att lagrings kontot har behörighet att komma åt det. I det här steget ska du använda den hanterade identitet som du tidigare tilldelade till lagrings kontot.

Om du vill ange åtkomst principen för nyckel valvet anropar du [AZ Key Vault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Skapa en ny nyckel

Skapa sedan en nyckel i nyckel valvet. Anropa [AZ Key Vault Create](/cli/azure/keyvault/key#az-keyvault-key-create)för att skapa en nyckel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

Azure Storage-kryptering stöder RSA-och RSA-HSM-nycklar i storlekarna 2048, 3072 och 4096. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med Kundhanterade nycklar

Som standard använder Azure Storage kryptering Microsoft-hanterade nycklar. I det här steget konfigurerar du ditt Azure Storage-konto så att det använder Kundhanterade nycklar med Azure Key Vault och anger sedan nyckeln som ska associeras med lagrings kontot.

När du konfigurerar kryptering med Kundhanterade nycklar kan du välja att automatiskt rotera nyckeln som används för kryptering när versionen ändras i det associerade nyckel valvet. Alternativt kan du uttryckligen ange en nyckel version som ska användas för kryptering tills nyckel versionen uppdateras manuellt.

### <a name="configure-encryption-for-automatic-rotation-of-customer-managed-keys"></a>Konfigurera kryptering för automatisk rotation av Kundhanterade nycklar

Om du vill konfigurera kryptering för automatisk rotation av Kundhanterade nycklar, installerar du [Azure CLI version 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) eller senare. Mer information finns i [Installera Azure CLI](/azure/install-azure-cli).

Om du vill rotera Kundhanterade nycklar automatiskt, utelämnar du nyckel versionen när du konfigurerar Kundhanterade nycklar för lagrings kontot. Anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagrings kontots krypterings inställningar, som du ser i följande exempel. Inkludera `--encryption-key-source` parametern och Ställ in den på `Microsoft.Keyvault` för att aktivera Kundhanterade nycklar för kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

### <a name="configure-encryption-for-manual-rotation-of-key-versions"></a>Konfigurera kryptering för manuell rotation av nyckel versioner

Ange nyckel versionen när du konfigurerar kryptering med Kundhanterade nycklar för lagrings kontot om du uttryckligen vill ange en nyckel version som ska användas för kryptering. Anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagrings kontots krypterings inställningar, som du ser i följande exempel. Inkludera `--encryption-key-source` parametern och Ställ in den på `Microsoft.Keyvault` för att aktivera Kundhanterade nycklar för kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

När du roterar nyckel versionen manuellt måste du uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen. Börja med att fråga efter Key Vault-URI: n genom att anropa AZ-nyckelpar [show](/cli/azure/keyvault#az-keyvault-show)och för nyckel versionen genom att anropa [AZ Key Vault Key List-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Anropa sedan [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen av nyckeln, som du ser i föregående exempel.

## <a name="use-a-different-key"></a>Använd en annan nyckel

Om du vill ändra den nyckel som används för Azure Storage-kryptering kan du anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) som visas i [Konfigurera kryptering med Kundhanterade nycklar](#configure-encryption-with-customer-managed-keys) och ange det nya nyckel namnet och versionen. Om den nya nyckeln finns i ett annat nyckel valv uppdaterar du även Key Vault-URI: n.

## <a name="revoke-customer-managed-keys"></a>Återkalla Kundhanterade nycklar

Du kan återkalla Kundhanterade nycklar genom att ta bort åtkomst principen för nyckel valvet. Om du vill återkalla en kundhanterad nyckel anropar du kommandot [AZ Key Vault Delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) , som du ser i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Inaktivera Kundhanterade nycklar

När du inaktiverar Kundhanterade nycklar är ditt lagrings konto återigen krypterat med Microsoft-hanterade nycklar. Om du vill inaktivera Kundhanterade nycklar, anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) och Ställ in på `--encryption-key-source parameter` `Microsoft.Storage` , som visas i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md) 
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
