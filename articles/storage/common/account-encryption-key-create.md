---
title: Skapa ett konto som stöder Kundhanterade nycklar för tabeller och köer
titleSuffix: Azure Storage
description: Lär dig hur du skapar ett lagrings konto som stöder konfigurering av Kundhanterade nycklar för tabeller och köer. Använd Azure CLI eller en Azure Resource Manager mall för att skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln för Azure Storage kryptering. Du kan sedan konfigurera Kundhanterade nycklar för kontot.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8cf1f8ecb68e31f93c19d93d6ebc4f8ef37724e7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028447"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Skapa ett konto som stöder Kundhanterade nycklar för tabeller och köer

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard använder Queue Storage och table Storage en nyckel som är begränsad till tjänsten och hanteras av Microsoft. Du kan också välja att använda Kundhanterade nycklar för att kryptera kö-eller tabell data. Om du vill använda Kundhanterade nycklar med köer och tabeller måste du först skapa ett lagrings konto som använder en krypterings nyckel som är begränsad till kontot, i stället för till tjänsten. När du har skapat ett konto som använder konto krypterings nyckeln för kö-och tabell data kan du konfigurera Kundhanterade nycklar med Azure Key Vault för det lagrings kontot.

Den här artikeln beskriver hur du skapar ett lagrings konto som förlitar sig på en nyckel som är begränsad till kontot. När kontot först skapas använder Microsoft konto nyckeln för att kryptera data i kontot och Microsoft hanterar nyckeln. Du kan sedan konfigurera Kundhanterade nycklar för kontot för att dra nytta av dessa förmåner, inklusive möjligheten att tillhandahålla egna nycklar, uppdatera nyckel versionen, rotera nycklarna och återkalla åtkomst kontroller.

## <a name="about-the-feature"></a>Om funktionen

Om du vill skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln för kö-och tabell lagring måste du först registrera dig för att använda den här funktionen med Azure. På grund av begränsad kapacitet bör du vara medveten om att det kan ta flera månader innan förfrågningar om åtkomst godkänns.

Du kan skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln för kö-och tabell lagring i följande regioner:

- USA, östra
- USA, södra centrala
- USA, västra 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registrera dig för att använda konto krypterings nyckeln

Om du vill registrera dig med Azure CLI anropar du kommandot [AZ funktions register](/cli/azure/feature#az-feature-register) .

Registrera dig för att använda konto krypterings nyckeln med Queue Storage:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Registrera dig för att använda konto krypterings nyckeln med Table Storage:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="check-the-status-of-your-registration"></a>Kontrol lera status för registreringen

Kontrol lera status för registreringen för Queue Storage:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Så här kontrollerar du status för registreringen av Table Storage:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrera Azure Storage Resource providern igen

När registreringen har godkänts måste du registrera Azure Storage resurs leverantören igen. Anropa [AZ-providerns register](/cli/azure/provider#az-provider-register) kommando:

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Skapa ett konto som använder krypterings nyckeln för kontot

Du måste konfigurera ett nytt lagrings konto för att kunna använda kontots krypterings nyckel för köer och tabeller vid den tidpunkt då du skapar lagrings kontot. Det går inte att ändra omfånget för krypterings nyckeln när kontot har skapats.

Lagrings kontot måste vara av typen General-Purpose v2. Du kan skapa lagrings kontot och konfigurera det så att det förlitar sig på konto krypterings nyckeln med hjälp av antingen Azure CLI eller en Azure Resource Manager mall.

> [!NOTE]
> Det går bara att konfigurera kö-och tabell lagring för att kryptera data med konto krypterings nyckeln när lagrings kontot skapas. Blob Storage och Azure Files alltid använda kontots krypterings nyckel för att kryptera data.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill använda Azure CLI för att skapa ett lagrings konto som förlitar sig på konto krypterings nyckeln kontrollerar du att du har installerat Azure CLI-version 2.0.80 eller senare. Mer information finns i [installera Azure CLI](/cli/azure/install-azure-cli).

Skapa sedan ett allmänt-syfte v2-lagrings konto genom att anropa kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) med lämpliga parametrar:

- Ta med alternativet `--encryption-key-type-for-queue` och ange värdet till `Account` att använda kontots krypterings nyckel för att kryptera data i Queue Storage.
- Ta med alternativet `--encryption-key-type-for-table` och ange värdet till `Account` att använda kontots krypterings nyckel för att kryptera data i Table Storage.

I följande exempel visas hur du skapar ett allmänt-syfte v2-lagrings konto som har kon figurer ATS för LRS och som använder konto krypterings nyckeln för att kryptera data för både Queue-och table-lagring. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

### <a name="templatetabtemplate"></a>[Mall](#tab/template)

Följande JSON-exempel skapar ett allmänt-syfte v2-lagrings konto som har kon figurer ATS för LRS och som använder konto krypterings nyckeln för att kryptera data för både Queue-och table-lagring. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

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
            "name": "[parameters('Standard_LRS')]"
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

När du har skapat ett konto som förlitar sig på konto krypterings nyckeln kan du läsa följande artiklar för att konfigurera Kundhanterade nycklar med Azure Key Vault:

- [Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurera Kundhanterade nycklar med Azure Key Vault med hjälp av Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Verifiera konto krypterings nyckeln

För att verifiera att en tjänst i ett lagrings konto använder konto krypterings nyckeln anropar du kommandot för Azure CLI- [AZ lagrings konto](/cli/azure/storage/account#az-storage-account-show) . Det här kommandot returnerar en uppsättning egenskaper för lagrings konto och deras värden. Leta efter fältet `keyType` för varje tjänst i egenskapen kryptering och kontrol lera att den är inställd på `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

## <a name="next-steps"></a>Nästa steg

- [Azure Storage kryptering för vilande data](storage-service-encryption.md) 
- [Vad är Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
