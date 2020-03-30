---
title: Skapa ett konto som stöder kundhanterade nycklar för tabeller och köer
titleSuffix: Azure Storage
description: Lär dig hur du skapar ett lagringskonto som stöder konfiguration av kundhanterade nycklar för tabeller och köer. Använd Azure CLI eller en Azure Resource Manager-mall för att skapa ett lagringskonto som är beroende av kontokrypteringsnyckeln för Azure Storage-kryptering. Du kan sedan konfigurera kundhanterade nycklar för kontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083567"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Skapa ett konto som stöder kundhanterade nycklar för tabeller och köer

Azure Storage krypterar alla data i ett lagringskonto i vila. Som standard använder kölagring och tabelllagring en nyckel som är begränsad till tjänsten och hanteras av Microsoft. Du kan också välja att använda kundhanterade nycklar för att kryptera kö- eller tabelldata. Om du vill använda kundhanterade nycklar med köer och tabeller måste du först skapa ett lagringskonto som använder en krypteringsnyckel som är begränsad till kontot i stället för till tjänsten. När du har skapat ett konto som använder kontokrypteringsnyckeln för kö- och tabelldata kan du konfigurera kundhanterade nycklar med Azure Key Vault för det lagringskontot.

I den här artikeln beskrivs hur du skapar ett lagringskonto som är beroende av en nyckel som är begränsad till kontot. När kontot först skapas använder Microsoft kontonyckeln för att kryptera data i kontot och Microsoft hanterar nyckeln. Du kan sedan konfigurera kundhanterade nycklar för kontot för att dra nytta av dessa fördelar, inklusive möjligheten att tillhandahålla egna nycklar, uppdatera nyckelversionen, rotera nycklarna och återkalla åtkomstkontroller.

## <a name="about-the-feature"></a>Om funktionen

Om du vill skapa ett lagringskonto som är beroende av kontokrypteringsnyckeln för lagring av kö och tabell måste du först registrera dig för att använda den här funktionen med Azure. På grund av begränsad kapacitet bör du vara medveten om att det kan ta flera månader innan begäran om åtkomst godkänns.

Du kan skapa ett lagringskonto som är beroende av kontokrypteringsnyckeln för kö- och tabelllagring i följande regioner:

- USA, östra
- USA, södra centrala
- USA, västra 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registrera dig för att använda kontokrypteringsnyckeln

Om du vill registrera dig för att använda kontokrypteringsnyckeln med Kö- eller Tabelllagring använder du PowerShell eller Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill registrera dig med PowerShell anropar du kommandot [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera dig med Azure CLI anropar du kommandot [az-funktionsregister.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

### <a name="check-the-status-of-your-registration"></a>Kontrollera status för din registrering

Om du vill kontrollera status för din registrering för lagring av kö eller tabell använder du PowerShell eller Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill kontrollera status för din registrering med PowerShell anropar du kommandot [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrollera status för din registrering med Azure CLI anropar du kommandot [az-funktionen.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrera azure storage-resursprovidern igen

När registreringen har godkänts måste du registrera azure storage-resursprovidern igen. Använd PowerShell eller Azure CLI för att registrera resursprovidern igen.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill registrera om resursleverantören med PowerShell anropar du kommandot [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera resursprovidern igen med Azure CLI anropar du kommandot [az provider register.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Skapa ett konto som använder nyckeln för kontokryptering

Du måste konfigurera ett nytt lagringskonto för att använda kontokrypteringsnyckeln för köer och tabeller när du skapar lagringskontot. Krypteringsnyckelns omfattning kan inte ändras när kontot har skapats.

Lagringskontot måste vara av typen allmänt ändamål v2. Du kan skapa lagringskontot och konfigurera det så att det är beroende av kontokrypteringsnyckeln med hjälp av Azure CLI eller en Azure Resource Manager-mall.

> [!NOTE]
> Endast kö- och tabelllagring kan konfigureras för att kryptera data med kontokrypteringsnyckeln när lagringskontot skapas. Blob-lagring och Azure-filer använder alltid kontokrypteringsnyckeln för att kryptera data.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill använda PowerShell för att skapa ett lagringskonto som är beroende av kontokrypteringsnyckeln kontrollerar du att du har installerat Azure PowerShell-modulen, version 3.4.0 eller senare. Mer information finns [i Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Skapa sedan ett allmänt v2-lagringskonto genom att anropa kommandot [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) med lämpliga parametrar:

- Inkludera `-EncryptionKeyTypeForQueue` alternativet och ange `Account` dess värde för att använda kontokrypteringsnyckeln för att kryptera data i kölagring.
- Inkludera `-EncryptionKeyTypeForTable` alternativet och ange `Account` dess värde för att använda kontokrypteringsnyckeln för att kryptera data i Tabelllagring.

I följande exempel visas hur du skapar ett allmänt v2-lagringskonto som är konfigurerat för geoundant lagring (läsåtkomst) och som använder kontokrypteringsnyckeln för att kryptera data för både kö- och tabelllagring. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill använda Azure CLI för att skapa ett lagringskonto som är beroende av kontokrypteringsnyckeln kontrollerar du att du har installerat Azure CLI version 2.0.80 eller senare. Mer information finns [i Installera Azure CLI](/cli/azure/install-azure-cli).

Skapa sedan ett allmänt v2-lagringskonto genom att anropa kommandot [az storage account create,](/cli/azure/storage/account#az-storage-account-create) med lämpliga parametrar:

- Inkludera `--encryption-key-type-for-queue` alternativet och ange `Account` dess värde för att använda kontokrypteringsnyckeln för att kryptera data i kölagring.
- Inkludera `--encryption-key-type-for-table` alternativet och ange `Account` dess värde för att använda kontokrypteringsnyckeln för att kryptera data i Tabelllagring.

I följande exempel visas hur du skapar ett allmänt v2-lagringskonto som är konfigurerat för geoundant lagring (läsåtkomst) och som använder kontokrypteringsnyckeln för att kryptera data för både kö- och tabelllagring. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Mall](#tab/template)

I följande JSON-exempel skapas ett allmänt v2-lagringskonto som är konfigurerat för geoundant lagring (läsåtkomst) och som använder kontokrypteringsnyckeln för att kryptera data för både kö- och tabelllagring. Kom ihåg att ersätta platshållarvärdena inom vinkelparenteser med dina egna värden:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

När du har skapat ett konto som är beroende av kontokrypteringsnyckeln läser du någon av följande artiklar för att konfigurera kundhanterade nycklar med Azure Key Vault:

- [Konfigurera kundhanterade nycklar med Azure Key Vault med hjälp av Azure-portalen](storage-encryption-keys-portal.md)
- [Konfigurera kundhanterade nycklar med Azure Key Vault med PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera kundhanterade nycklar med Azure Key Vault med hjälp av Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Verifiera nyckeln för kontokryptering

Om du vill verifiera att en tjänst i ett lagringskonto använder krypteringsnyckeln för kontot anropar du kommandot Azure CLI [az storage account.](/cli/azure/storage/account#az-storage-account-show) Det här kommandot returnerar en uppsättning lagringskontoegenskaper och deras värden. Leta efter `keyType` fältet för varje tjänst i krypteringsegenskapen och kontrollera att den är inställd på `Account`.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Om du vill kontrollera att en tjänst i ett lagringskonto använder krypteringsnyckeln för kontot anropar du kommandot [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Det här kommandot returnerar en uppsättning lagringskontoegenskaper och deras värden. Leta efter `KeyType` fältet för varje `Encryption` tjänst i egenskapen `Account`och kontrollera att den är inställd på .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrollera att en tjänst i ett lagringskonto använder krypteringsnyckeln för kontot anropar du kommandot [az storage account.](/cli/azure/storage/account#az-storage-account-show) Det här kommandot returnerar en uppsättning lagringskontoegenskaper och deras värden. Leta efter `keyType` fältet för varje tjänst i krypteringsegenskapen och kontrollera att den är inställd på `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering för data i vila](storage-service-encryption.md) 
- [Vad är Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
