---
title: Använda Azure CLI för att konfigurera kundhanterade nycklar
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure CLI för att konfigurera kundhanterade nycklar med Azure Key Vault för Azure Storage-kryptering.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea944d4cfa3006c33f1dee3dd8e6ee6088681aa7
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618649"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Konfigurera kundhanterade nycklar med Azure Key Vault med hjälp av Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar ett Azure Key Vault med kundhanterade nycklar med Azure CLI. Mer information om hur du skapar ett nyckelvalv med Azure CLI finns i [Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med Azure CLI](../../key-vault/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela en identitet till lagringskontot

Om du vill aktivera kundhanterade nycklar för ditt lagringskonto tilldelar du först en systemtilldelad hanterad identitet till lagringskontot. Du ska använda den här hanterade identiteten för att bevilja lagringskontobehörigheter för åtkomst till nyckelvalvet.

Om du vill tilldela en hanterad identitet med Azure CLI anropar du [uppdatering av az-lagringskonto](/cli/azure/storage/account#az-storage-account-update). Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Mer information om hur du konfigurerar systemtilldelade hanterade identiteter med Azure CLI finns i [Konfigurera hanterade identiteter för Azure-resurser på en Azure VM med Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckelvalv

Nyckelvalvet som du använder för att lagra kundhanterade nycklar för Azure Storage-kryptering måste ha två nyckelskyddsinställningar aktiverade, **Mjuk borttagning** och **Rensa inte**. Om du vill skapa ett nytt nyckelvalv med PowerShell eller Azure CLI med dessa inställningar aktiverade kör du följande kommandon. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden.

Om du vill skapa ett nytt nyckelvalv med Azure CLI anropar du [az keyvault create](/cli/azure/keyvault#az-keyvault-create). Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Mer information om hur du aktiverar **Mjuk borttagning** och **rensar inte** på ett befintligt nyckelvalv med Azure CLI finns i avsnitten **Aktivera mjuk borttagning** och **Aktivera rensningsskydd** i [Så här använder du mjuk borttagning med CLI](../../key-vault/key-vault-soft-delete-cli.md).

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera principen för nyckelvalvsåtkomst

Konfigurera sedan åtkomstprincipen för nyckelvalvet så att lagringskontot har behörighet att komma åt det. I det här steget ska du använda den hanterade identitet som du tidigare tilldelat lagringskontot.

Om du vill ange åtkomstprincipen för nyckelvalvet anropar du [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden.

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

Skapa sedan en nyckel i nyckelvalvet. Om du vill skapa en nyckel anropar du [az keyvault-tangenten create](/cli/azure/keyvault/key#az-keyvault-key-create). Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Endast 2048-bitars RSA- och RSA-HSM-nycklar stöds med Azure Storage-kryptering. Mer information om nycklar finns i **Key Vault-nycklar** i [Om Azure Key Vault-nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med kundhanterade nycklar

Som standard använder Azure Storage-kryptering Microsoft-hanterade nycklar. Konfigurera ditt Azure Storage-konto för kundhanterade nycklar och ange nyckeln som ska associeras med lagringskontot.

Om du vill uppdatera krypteringsinställningarna för lagringskontot anropar du [uppdatering av az-lagringskontot,](/cli/azure/storage/account#az-storage-account-update)vilket visas i följande exempel. Inkludera `--encryption-key-source` parametern och `Microsoft.Keyvault` ställ in den på att aktivera kundhanterade nycklar för lagringskontot. Exemplet frågar också efter uri-kort för nyckelvalvet och den senaste nyckelversionen, som båda värdena behövs för att associera nyckeln med lagringskontot. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden.

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

## <a name="update-the-key-version"></a>Uppdatera nyckelversionen

När du skapar en ny version av en nyckel måste du uppdatera lagringskontot för att kunna använda den nya versionen. Först fråga efter nyckeln valv URI genom att ringa [az keyvault visa](/cli/azure/keyvault#az-keyvault-show), och för nyckelversionen genom att ringa [az keyvault nyckel lista-versioner](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Anropa sedan uppdatering av [az-lagringskontot](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagringskontots krypteringsinställningar för att använda den nya versionen av nyckeln, som visas i föregående avsnitt.

## <a name="use-a-different-key"></a>Använda en annan nyckel

Om du vill ändra nyckeln som används för Azure Storage-kryptering anropar du [uppdatering av az-lagringskonto](/cli/azure/storage/account#az-storage-account-update) som visas i [Konfigurera kryptering med kundhanterade nycklar](#configure-encryption-with-customer-managed-keys) och tillhandahåller det nya nyckelnamnet och versionen. Om den nya nyckeln finns i ett annat nyckelvalv uppdaterar du även uri-kort för nyckelvalvet.

## <a name="revoke-customer-managed-keys"></a>Återkalla kundhanterade nycklar

Om du tror att en nyckel kan ha komprometterats kan du återkalla kundhanterade nycklar genom att ta bort åtkomstprincipen för nyckelvalvet. Om du vill återkalla en kundhanterad nyckel anropar du kommandot [az keyvault delete-policy,](/cli/azure/keyvault#az-keyvault-delete-policy) som visas i följande exempel. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden och använda de variabler som definierats i föregående exempel.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Inaktivera kundhanterade nycklar

När du inaktiverar kundhanterade nycklar krypteras ditt lagringskonto återigen med Microsoft-hanterade nycklar. Om du vill inaktivera kundhanterade nycklar `--encryption-key-source parameter` anropar du [uppdatering av az-lagringskontot](/cli/azure/storage/account#az-storage-account-update) och ställer in till `Microsoft.Storage`, som visas i följande exempel. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden och använda de variabler som definierats i föregående exempel.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering för data i vila](storage-service-encryption.md) 
- [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
