---
title: Ta reda på vilken krypteringsnyckelmodell som används för lagringskontot
titleSuffix: Azure Storage
description: Använd Azure portal, PowerShell eller Azure CLI för att kontrollera hur krypteringsnycklar hanteras för lagringskontot. Nycklar kan hanteras av Microsoft (standard) eller av kunden. Kundhanterade nycklar måste lagras i Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409871"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Ta reda på vilken Azure Storage-krypteringsnyckelmodell som används för lagringskontot

Data i ditt lagringskonto krypteras automatiskt av Azure Storage. Azure Storage-kryptering erbjuder två alternativ för att hantera krypteringsnycklar på lagringskontots nivå:

- **Microsoft-hanterade nycklar.** Som standard hanterar Microsoft de nycklar som används för att kryptera ditt lagringskonto.
- **Kundhanterade nycklar.** Du kan också välja att hantera krypteringsnycklar för ditt lagringskonto. Kundhanterade nycklar måste lagras i Azure Key Vault.

Dessutom kan du ange en krypteringsnyckel på nivån för en enskild begäran för vissa Blob-lagringsåtgärder. När en krypteringsnyckel anges på begäran åsidosätter nyckeln krypteringsnyckeln som är aktiv på lagringskontot. Mer information finns i [Ange en kundklumpningsnyckel på en begäran till Blob storage](../blobs/storage-blob-customer-provided-key.md).

Mer information om krypteringsnycklar finns i [Azure Storage-kryptering för data i vila](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Kontrollera krypteringsnyckelmodellen för lagringskontot

Om du vill ta reda på om ett lagringskonto använder Microsoft-hanterade nycklar eller kundhanterade nycklar för kryptering använder du någon av följande metoder.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Så här kontrollerar du krypteringsmodellen för lagringskontot med hjälp av Azure-portalen:

1. Navigera till ditt lagringskonto i Azure Portal.
1. Välj **krypteringsinställningen** och notera inställningen.

Följande bild visar ett lagringskonto som är krypterat med Microsoft-hanterade nycklar:

![Visa konto krypterat med Microsoft-hanterade nycklar](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

Och följande bild visar ett lagringskonto som är krypterat med kundhanterade nycklar:

![Skärmbild som visar krypteringsnyckelinställningen i Azure-portalen](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill kontrollera krypteringsmodellen för lagringskontot med Hjälp av PowerShell anropar du kommandot [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) och kontrollerar sedan egenskapen **KeySource** för kontot.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Om värdet för egenskapen `Microsoft.Storage` **KeySource** är krypteras kontot med Microsoft-hanterade nycklar. Om värdet för egenskapen `Microsoft.Keyvault` **KeySource** är krypteras kontot med kundhanterade nycklar.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Om du vill kontrollera krypteringsmodellen för lagringskontot med hjälp av Azure CLI anropar du kommandot [az storage account show](/cli/azure/storage/account#az-storage-account-show) och kontrollerar sedan egenskapen **keySource** för kontot.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Om värdet för egenskapen `Microsoft.Storage` **keySource** är krypteras kontot med Microsoft-hanterade nycklar. Om värdet för egenskapen `Microsoft.Keyvault` **keySource** är krypteras kontot med kundhanterade nycklar.

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering för data i vila](storage-service-encryption.md)
- [Använda kundhanterade nycklar med Azure Key Vault för att hantera Azure Storage-kryptering](encryption-customer-managed-keys.md)