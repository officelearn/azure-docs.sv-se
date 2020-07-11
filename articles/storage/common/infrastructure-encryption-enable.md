---
title: Skapa ett lagrings konto med infrastruktur kryptering aktiverat för dubbel kryptering av data
titleSuffix: Azure Storage
description: Kunder som behöver högre garantier att deras data är säkra kan också aktivera 256-bitars AES-kryptering på Azure Storage infrastruktur nivå. När infrastruktur kryptering har Aktiver ATS krypteras data i ett lagrings konto två gånger med två olika krypteringsalgoritmer och två olika nycklar.
services: storage
author: tamram
ms.service: storage
ms.date: 07/08/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: edeb184af1c1260a456ed3de7064805526629de8
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225369"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Skapa ett lagrings konto med infrastruktur kryptering aktiverat för dubbel kryptering av data

Azure Storage krypterar automatiskt alla data i ett lagrings konto på tjänst nivå med hjälp av 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Kunder som behöver högre garantier att deras data är säkra kan också aktivera 256-bitars AES-kryptering på Azure Storage infrastruktur nivå. När infrastruktur kryptering har Aktiver ATS krypteras data i ett lagrings konto två gånger &mdash; på tjänst nivå och en gång på infrastruktur nivå &mdash; med två olika krypteringsalgoritmer och två olika nycklar. Dubbel kryptering av Azure Storage data skyddar mot ett scenario där en av krypteringsalgoritmerna eller nycklarna kan komprometteras. I det här scenariot fortsätter det ytterligare lagret med kryptering att skydda dina data.

Kryptering på tjänst nivå stöder användning av antingen Microsoft-hanterade nycklar eller kund hanterade nycklar med Azure Key Vault. Kryptering på infrastruktur nivå är beroende av Microsoft-hanterade nycklar och använder alltid en separat nyckel. Mer information om nyckel hantering med Azure Storage kryptering finns i [om hantering av krypterings nycklar](storage-service-encryption.md#about-encryption-key-management).

Om du vill dubblera kryptering av data måste du först skapa ett lagrings konto som har kon figurer ATS för infrastruktur kryptering. Den här artikeln beskriver hur du skapar ett lagrings konto som möjliggör infrastruktur kryptering.

## <a name="about-the-feature"></a>Om funktionen

Om du vill skapa ett lagrings konto som har infrastruktur kryptering aktiverat måste du först registrera dig för att använda den här funktionen med Azure. På grund av begränsad kapacitet bör du vara medveten om att det kan ta flera månader innan förfrågningar om åtkomst godkänns.

Du kan skapa ett lagrings konto med infrastruktur kryptering aktiverat i följande regioner:

- East US
- USA, södra centrala
- USA, västra 2

### <a name="register-to-use-infrastructure-encryption"></a>Registrera dig för att använda infrastruktur kryptering

Om du vill registrera dig för att använda infrastruktur kryptering med Azure Storage använder du PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrera med PowerShell genom att anropa kommandot [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera dig med Azure CLI anropar du kommandot [AZ funktions register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

### <a name="check-the-status-of-your-registration"></a>Kontrol lera status för registreringen

Använd PowerShell eller Azure CLI för att kontrol lera statusen för registreringen av infrastruktur kryptering.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrol lera status för registreringen med PowerShell anropar du kommandot [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrol lera statusen för registreringen med Azure CLI anropar du kommandot [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrera Azure Storage Resource providern igen

När registreringen har godkänts måste du registrera Azure Storage resurs leverantören igen. Använd PowerShell eller Azure CLI för att omregistrera resurs leverantören.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Anropa kommandot [register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) för att omregistrera resurs leverantören med PowerShell.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill registrera om resurs leverantören med Azure CLI anropar du kommandot [AZ Provider register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Skapa ett konto med infrastruktur kryptering aktiverat

Du måste konfigurera ett lagrings konto för att använda infrastruktur kryptering vid den tidpunkt då du skapar kontot. Det går inte att aktivera infrastruktur kryptering eller inaktive ras när kontot har skapats.

Lagrings kontot måste vara av typen General-Purpose v2. Du kan skapa lagrings kontot och konfigurera det för att aktivera infrastruktur kryptering med hjälp av antingen PowerShell, Azure CLI eller en Azure Resource Manager mall.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill använda PowerShell för att skapa ett lagrings konto med infrastruktur kryptering aktiverat, kontrollerar du att du har installerat [PowerShell-modulen AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage), version 2.2.0 eller senare. Mer information finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

Skapa sedan ett allmänt-syfte v2-lagrings konto genom att anropa kommandot [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Inkludera `-RequireInfrastructureEncryption` alternativet för att aktivera infrastruktur kryptering.

I följande exempel visas hur du skapar ett allmänt-syfte v2-lagrings konto som är konfigurerat för Geo-redundant lagring med Läs behörighet (RA-GRS) och har infrastruktur kryptering aktiverat för dubbel kryptering av data. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill använda Azure CLI för att skapa ett lagrings konto som har infrastruktur kryptering aktiverat, kontrollerar du att du har installerat Azure CLI-version 2.8.0 eller senare. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

Skapa sedan ett allmänt-syfte v2-lagrings konto genom att anropa kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) och inkludera `--require-infrastructure-encryption option` för att aktivera infrastruktur kryptering.

I följande exempel visas hur du skapar ett allmänt-syfte v2-lagrings konto som är konfigurerat för Geo-redundant lagring med Läs behörighet (RA-GRS) och har infrastruktur kryptering aktiverat för dubbel kryptering av data. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Mall](#tab/template)

Följande JSON-exempel skapar ett allmänt-syfte v2-lagrings konto som är konfigurerat för Geo-redundant lagring med Läs behörighet (RA-GRS) och har infrastruktur kryptering aktiverat för dubbel kryptering av data. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Kontrol lera att infrastruktur kryptering har Aktiver ATS

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill kontrol lera att infrastruktur kryptering har Aktiver ATS för ett lagrings konto anropar du kommandot [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Det här kommandot returnerar en uppsättning egenskaper för lagrings konto och deras värden. Hämta `RequireInfrastructureEncryption` fältet i `Encryption` egenskapen och kontrol lera att det är inställt på `True` .

I följande exempel hämtas värdet för `RequireInfrastructureEncryption` egenskapen. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill kontrol lera att infrastruktur kryptering har Aktiver ATS för ett lagrings konto anropar du kommandot [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) . Det här kommandot returnerar en uppsättning egenskaper för lagrings konto och deras värden. Leta efter `requireInfrastructureEncryption` fältet i `encryption` egenskapen och kontrol lera att det är inställt på `true` .

I följande exempel hämtas värdet för `requireInfrastructureEncryption` egenskapen. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Använda Kundhanterade nycklar med Azure Key Vault för att hantera Azure Storage kryptering](encryption-customer-managed-keys.md)