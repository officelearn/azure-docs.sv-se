---
title: Konfigurera Kundhanterade nycklar för kryptering av Azure Storage från Azure CLI
description: Lär dig hur du använder Azure CLI för att konfigurera Kundhanterade nycklar för kryptering av Azure Storage. Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomstkontroller.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ca2cfc9369fd6fb001b2a2dc401c33e5ddfd38c8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142912"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Konfigurera Kundhanterade nycklar för kryptering av Azure Storage från Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Den här artikeln visar hur du konfigurerar ett nyckelvalv med Kundhanterade nycklar med hjälp av Azure CLI.

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela en identitet till storage-kontot

Om du vill använda Kundhanterade nycklar för ditt lagringskonto måste du först tilldela en automatiskt genererad hanterad identitet till storage-kontot. Du använder den här hanterade identiteten för att bevilja behörigheter för storage-konto för åtkomst till key vault.

Om du vill tilldela en hanterad identitet med hjälp av Azure CLI, anropa [az storage-konto uppdatering](/cli/azure/storage/account#az-storage-account-update). Kom ihåg att ersätter du platshållarens värden inom hakparentes med dina egna värden.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Mer information om hur du konfigurerar systemtilldelade hanterade identiteter med Azure CLI finns i [konfigurera hanterade identiteter för Azure-resurser på en Azure virtuell dator med Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckelvalv

Nyckelvalvet som du använder för att lagra Kundhanterade nycklar för Azure Storage kryptering måste ha två nyckelskydd inställningar aktiverade, **mjuk borttagning** och **gör inte rensa**. Kör följande kommandon för att skapa ett nytt nyckelvalv med PowerShell eller Azure CLI med dessa inställningar aktiverade. Kom ihåg att ersätter du platshållarens värden inom hakparentes med dina egna värden. 

Om du vill skapa ett nytt nyckelvalv med Azure CLI, anropa [az keyvault skapa](/cli/azure/keyvault#az-keyvault-create). Kom ihåg att ersätter du platshållarens värden inom hakparentes med dina egna värden.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurera nyckelvalvets åtkomstprincip

Konfigurera åtkomstprincip för nyckelvalvet så att lagringskontot har åtkomstbehörighet till den. I det här steget ska du använda den hanterade identitet som du tidigare tilldelat till lagringskontot.

För att ställa in åtkomstprincip för nyckelvalvet, anropa [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Kom ihåg att ersätter du platshållarens värden inom hakparentes med dina egna värden.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Skapa en ny nyckel

Skapa sedan en nyckel i nyckelvalvet. Om du vill skapa en nyckel, anropa [az keyvault key skapa](/cli/azure/keyvault/key#az-keyvault-key-create). Kom ihåg att ersätter du platshållarens värden inom hakparentes med dina egna värden.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurera kryptering med Kundhanterade nycklar

Som standard använder Azure Storage kryptering Microsoft-hanterade nycklar. Konfigurera Azure Storage-kontot för Kundhanterade nycklar och ange nyckeln som ska associeras med lagringskontot.

För att uppdatera krypteringsinställningar för storage-konto, anropa [az storage-konto uppdatering](/cli/azure/storage/account#az-storage-account-update). Det här exemplet frågar också efter nyckelvalvet URI och nyckelversionen, både vilka värden som krävs för att koppla nyckeln till lagringskontot. Kom ihåg att ersätter du platshållarens värden inom hakparentes med dina egna värden.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Uppdatera nyckelns version

När du skapar en ny version av en nyckel, måste du uppdatera lagringskontot för att använda den nya versionen. Först fråga för key vault URI genom att anropa [az keyvault show](/cli/azure/keyvault#az-keyvault-show), och för nyckelns version genom att anropa [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Anropa sedan [az storage-konto update](/cli/azure/storage/account#az-storage-account-update) att uppdatera krypteringsinställningar för storage-konto om du vill använda den nya versionen av nyckeln, som visas i föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md) 
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
