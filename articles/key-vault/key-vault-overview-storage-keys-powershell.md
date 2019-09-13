---
title: Azure Key Vault hanterat lagrings konto – PowerShell-version
description: Den hanterade lagrings konto funktionen ger en miljö som inte är integrerad mellan Azure Key Vault och ett Azure Storage-konto.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/01/2019
ms.openlocfilehash: 7968c045a59e86ecb89c0cc797936210b9cfb07d
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883282"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault hanterat lagrings konto – PowerShell

> [!NOTE]
> [Azure Storage-integrering med Azure Active Directory (Azure AD) är nu i för hands version](../storage/common/storage-auth-aad.md). Vi rekommenderar att du använder Azure AD för autentisering och auktorisering, vilket ger OAuth2-tokenbaserad åtkomst till Azure Storage, precis som Azure Key Vault. På så sätt kan du:
> - Autentisera klient programmet med hjälp av ett program eller en användar identitet, i stället för autentiseringsuppgifterna för lagrings kontot. 
> - Använd en [hanterad Azure AD-identitet](/azure/active-directory/managed-identities-azure-resources/) när du kör på Azure. Hanterade identiteter tar bort behovet av klientautentisering tillsammans och lagrar autentiseringsuppgifter i eller med ditt program.
> - Använd rollbaserad Access Control (RBAC) för att hantera auktorisering, som också stöds av Key Vault.
> - AAD-åtkomst till lagrings kontot fungerar inte för att komma åt tabeller ännu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ett [Azure Storage-konto](/azure/storage/storage-create-storage-account) använder en autentiseringsuppgift som består av ett konto namn och en nyckel. Nyckeln genereras automatiskt och fungerar som "lösen ord" i stället för en kryptografisk nyckel. Key Vault kan hantera dessa lagrings konto nycklar genom att lagra dem som [Key Vault hemligheter](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Översikt

Den Key Vault hanterade lagrings konto funktionen utför flera hanterings funktioner för din räkning:

- Visar en lista över nycklar med ett Azure Storage-konto.
- Återskapar (roterar) nycklarna med jämna mellanrum.
- Hanterar nycklar för både lagrings konton och klassiska lagrings konton.
- Nyckel värden returneras aldrig som svar på anroparen.

När du använder funktionen för hanterad lagrings konto nyckel:

- **Tillåt endast Key Vault att hantera dina lagrings konto nycklar.** Försök inte att hantera dem själv, eftersom du stör Key Vaults processer.
- **Tillåt inte att lagrings konto nycklar hanteras av fler än ett Key Vault-objekt**.
- **Återskapa inte dina lagrings konto nycklar manuellt**. Vi rekommenderar att du återskapar dem via Key Vault.

I följande exempel visas hur du tillåter Key Vault att hantera dina lagrings konto nycklar.

## <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Autentisera din PowerShell-session med cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . 
```azurepowershell-interactive
Connect-AzAccount
```
Om du har flera Azure-prenumerationer kan du lista dem med hjälp av cmdleten [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) och ange den prenumeration som du vill använda med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Auktorisera Key Vault åtkomst till ditt lagrings konto

> [!IMPORTANT]
> En Azure AD-klient tillhandahåller varje registrerat program med ett **[huvud namn för tjänsten](/azure/active-directory/develop/developer-glossary#service-principal-object)** som fungerar som programmets identitet. Tjänstens huvud program-ID används när den ger tillstånd att få åtkomst till andra Azure-resurser med hjälp av rollbaserad åtkomst kontroll (RBAC). Eftersom Key Vault är ett Microsoft-program är det förregistrerat i alla Azure AD-klienter under samma program-ID, inom varje Azure-moln:
> - Azure AD-klienter i Azures offentliga moln använder program `7e7c393b-45d0-48b1-a35e-2905ddf8183c`-ID.
> - Azure AD-klienter i offentliga Azure-moln och alla andra använder program `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`-ID.

Innan Key Vault kan komma åt och hantera dina lagrings konto nycklar måste du ge åtkomst till ditt lagrings konto. Key Vault programmet måste ha behörighet att *lista* och *Återskapa* nycklar för ditt lagrings konto. Dessa behörigheter aktive ras via den inbyggda RBAC-rollen [lagrings konto nyckel operatörs tjänst roll](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Tilldela den här rollen till Key Vault tjänstens huvud namn, begränsa omfattningen till ditt lagrings konto med hjälp av följande steg. Se till att uppdatera `$resourceGroupName`variablerna, `$storageAccountKey` `$storageAccountName`, och `$keyVaultName` innan du kör skriptet:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Get your User Id for later commands
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Vid lyckad roll tilldelning bör du se utdata som liknar följande exempel:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Om Key Vault redan har lagts till i rollen på ditt lagrings konto får du en *"roll tilldelningen finns redan".* Fels. Du kan också kontrol lera roll tilldelningen med hjälp av sidan för lagrings kontots åtkomst kontroll (IAM) i Azure Portal.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Ge ditt användar konto behörighet till hanterade lagrings konton

>[!TIP] 
> Precis som Azure AD tillhandahåller ett **tjänst huvud namn** för ett programs identitet, anges ett **användar namn** för en användares identitet. Användarens huvud namn kan sedan tilldelas åtkomst behörighet till Key Vault, genom Key Vault åtkomst princip behörigheter.

Med samma PowerShell-session uppdaterar du Key Vault åtkomst princip för hanterade lagrings konton. Det här steget tillämpar lagrings kontots behörigheter för ditt användar konto, vilket garanterar att du kan komma åt de hanterade lagrings konto funktionerna: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Observera att behörigheter för lagrings konton inte är tillgängliga på sidan åtkomst principer för lagrings kontot i Azure Portal.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Lägg till ett hanterat lagrings konto till din Key Vault-instans

Skapa ett hanterat lagrings konto i Key Vault-instansen med samma PowerShell-session. `-DisableAutoRegenerateKey` Växeln anger att lagrings konto nycklarna inte ska återskapas.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

När du lägger till lagrings kontot utan att skapa nya nycklar bör du se utdata som liknar följande exempel:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Aktivera nyckel återskapande

Om du vill Key Vault återskapa dina lagrings konto nycklar med jämna mellanrum kan du ange en återställnings period. I följande exempel ställer vi in en tids period på tre dagar. Efter tre dagar kommer Key Vault att återskapa ' key2 ' och byta den aktiva nyckeln från ' key2 ' till ' KEY1 '.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

När du har tillrättat tillägget av lagrings kontot när nyckeln har återskapats bör du se utdata som liknar följande exempel:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Nästa steg

- [Nyckel exempel för hanterade lagrings konton](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell-referens](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
