---
title: Konfigurera Kundhanterade nycklar med hjälp av Azure CLI
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure CLI för att konfigurera Kundhanterade nycklar med Azure Key Vault för Azure Storage kryptering.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 893c953562e0d150bd5e8110e5473fd24a2aff83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176353"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar en Azure Key Vault med Kundhanterade nycklar med hjälp av Azure CLI. Information om hur du skapar ett nyckel valv med Azure CLI finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med Azure CLI](../../key-vault/secrets/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela lagrings kontot en identitet

Om du vill aktivera Kundhanterade nycklar för ditt lagrings konto tilldelar du först en systemtilldelad hanterad identitet till lagrings kontot. Du använder den här hanterade identiteten för att ge lagrings kontots behörigheter åtkomst till nyckel valvet.

Om du vill tilldela en hanterad identitet med Azure CLI anropar du [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

```azurecli-interactive
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

Endast 2048-bitars RSA-och RSA-HSM-nycklar stöds med Azure Storage kryptering. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med Kundhanterade nycklar

Som standard använder Azure Storage kryptering Microsoft-hanterade nycklar. Konfigurera ditt Azure Storage konto för Kundhanterade nycklar och ange nyckeln som ska associeras med lagrings kontot.

Om du vill uppdatera lagrings kontots krypterings inställningar kan du anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update), som visas i följande exempel. Inkludera `--encryption-key-source` parametern och Ställ in den på `Microsoft.Keyvault` för att aktivera Kundhanterade nycklar för lagrings kontot. Exemplet frågar även efter Key Vault-URI: n och den senaste nyckel versionen, båda värdena som krävs för att associera nyckeln med lagrings kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

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

## <a name="update-the-key-version"></a>Uppdatera nyckel versionen

När du skapar en ny version av en nyckel måste du uppdatera lagrings kontot för att använda den nya versionen. Börja med att fråga efter Key Vault-URI: n genom att anropa AZ-nyckelpar [show](/cli/azure/keyvault#az-keyvault-show)och för nyckel versionen genom att anropa [AZ Key Vault Key List-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Anropa sedan [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen av nyckeln, som du ser i föregående avsnitt.

## <a name="use-a-different-key"></a>Använd en annan nyckel

Om du vill ändra den nyckel som används för Azure Storage-kryptering kan du anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) som visas i [Konfigurera kryptering med Kundhanterade nycklar](#configure-encryption-with-customer-managed-keys) och ange det nya nyckel namnet och versionen. Om den nya nyckeln finns i ett annat nyckel valv uppdaterar du även Key Vault-URI: n.

## <a name="revoke-customer-managed-keys"></a>Återkalla Kundhanterade nycklar

Om du tror att en nyckel kan ha komprometterats kan du återkalla Kundhanterade nycklar genom att ta bort åtkomst principen för nyckel valvet. Om du vill återkalla en kundhanterad nyckel anropar du kommandot [AZ Key Vault Delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) , som du ser i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Inaktivera Kundhanterade nycklar

När du inaktiverar Kundhanterade nycklar är ditt lagrings konto återigen krypterat med Microsoft-hanterade nycklar. Om du vill inaktivera Kundhanterade nycklar, anropa [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) och `--encryption-key-source parameter` Ställ `Microsoft.Storage`in på, som visas i följande exempel. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden och använda variablerna som definierats i föregående exempel.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Nästa steg

- [Azure Storage kryptering för vilande data](storage-service-encryption.md) 
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
