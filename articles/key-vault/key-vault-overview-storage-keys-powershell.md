---
title: Azure Key Vault hanteras lagringskonto – PowerShell-version
description: Funktionen hanterad lagring ger en seemless integration mellan Azure Key Vault och ett Azure storage-konto.
ms.topic: conceptual
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 11/28/2018
ms.openlocfilehash: 280d3a7783d689c6174ecf6d2b29e52bdbc42417
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634667"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault hanteras lagringskonto – PowerShell

> [!NOTE]
> [Azure storage-integrering med Azure Active Directory (Azure AD) finns nu i förhandsversion](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Vi rekommenderar att du använder Azure AD för autentisering och auktorisering, som ger OAuth2-tokenbaserad åtkomst till Azure storage, precis som Azure Key Vault. På så sätt kan du:
> - Autentisera ditt klientprogram som använder en program- eller identitet, i stället för autentiseringsuppgifterna för lagringskontot. 
> - Använd en [Azure AD-hanterad identitet](/azure/active-directory/managed-identities-azure-resources/) när körs på Azure. Hanterade identiteter ta bort behovet av klientautentisering som helhet och lagra autentiseringsuppgifter i eller med ditt program.
> - Använd rollbaserad åtkomstkontroll (RBAC) för att hantera auktorisering, vilket även stöds av Key Vault.

En [Azure storage-konto](/azure/storage/storage-create-storage-account) använder en autentiseringsuppgift som består av ett kontonamn och en nyckel. Nyckeln är automatiskt genererade och fungerar mer som ”lösenord” till skillnad från en kryptografisk nyckel. Key Vault kan hantera dessa lagringskontonycklar genom att lagra dem som [Key Vault-hemligheter](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Översikt

Key Vault hanteras storage-konto som funktionen utför flera hanteringsfunktioner åt dig:

- Listor (synkronisering) nycklar med ett Azure storage-konto.
- Återskapar (roterar) nycklarna regelbundet.
- Hanterar nycklar för både storage-konton och klassiska lagringskonton.
- Nyckelvärden returneras aldrig i svaret till anroparen.

När du använder funktionen viktiga hanterad lagring:

- **Tillåt endast Key Vault för att hantera dina lagringskontonycklar.** Försök inte hantera dem själv, som du ska påverka Key Vault-processer.
- **Tillåt inte storage-kontonycklar som ska hanteras av fler än ett objekt i Key Vault**.
- **Inte manuellt återskapa dina lagringskontonycklar**. Vi rekommenderar att du återskapar dem via Key Vault.

I följande exempel visar hur du kan låta Key Vault för att hantera dina lagringskontonycklar.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Auktorisera Key Vault får tillgång till ditt storage-konto

> [!TIP]
> Azure AD innehåller varje registrerade program med en  **[tjänstens huvudnamn](/azure/active-directory/develop/developer-glossary#service-principal-object)**, som fungerar som programmets identitet. Tjänstens huvudnamn kan sedan få åtkomstbehörighet till andra Azure-resurser, till exempel Key Vault via rollbaserad åtkomstkontroll (RBAC). Det registreras förväg i alla Azure AD-klienter under program-ID ”cfa8b339-82a2-471a-a3c9-0fc0be7a4093” eftersom Key Vault är en Microsoft-program.

Innan Key Vault kan komma åt och hantera dina lagringskontonycklar, måste du godkänna åtkomsten ditt storage-konto. Key Vault-programmet kräver behörighet att *lista* och *återskapa* nycklar för ditt lagringskonto. Dessa behörigheter aktiveras via den inbyggda RBAC-rollen [Tjänstroll som operatör Storage-konto nyckeln](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Tilldela den här rollen till Key Vault-tjänstobjektet, begränsa omfånget till ditt lagringskonto med följande steg. Se till att uppdatera den `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey`, och `$keyVaultName` variabler innan du kör skriptet:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzureRmAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzureRmRoleAssignment -ApplicationId “cfa8b339-82a2-471a-a3c9-0fc0be7a4093” -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Vid lyckad rolltilldelningen, bör du se utdata som liknar följande exempel:

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

Om Key Vault har redan lagts till i rollen på ditt lagringskonto, får du en *”rolltilldelningen redan finns”.* Ett fel uppstod. Du kan också kontrollera rolltilldelningen, med hjälp av sidan ”åtkomstkontroll (IAM)” för lagringskontot i Azure-portalen.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Ge dina användare konto behörighet hanterade storage-konton

>[!TIP] 
> Precis som Azure AD tillhandahåller en **tjänstens huvudnamn** för identiteten för ett program, en **UPN** tillhandahålls för en användares identitet. Användares huvudnamn kan sedan få åtkomstbehörighet till Key Vault via Key Vault åtkomstbehörigheter för principen.

Med samma PowerShell-session kan uppdatera åtkomstprincip för Nyckelvalvet för hanterade storage-konton. Det här steget gäller lagring behörigheterna till ditt användarkonto, så att du har åtkomst till de hantera lagringsfunktionerna för konto: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get,list,delete,set,update,regeneratekey,recover,backup,restore,purge
```

Observera att behörigheter för storage-konton är inte tillgängliga på ”åtkomstprinciper” sidan för lagringskontot i Azure-portalen.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Lägg till ett hanterat lagringskonto till din Key Vault-instans

Med hjälp av samma PowerShell-session, skapa ett hanterat lagringskonto i din Key Vault-instans. Den `-DisableAutoRegenerateKey` växeln anger att inte återskapa lagringskontonycklarna.

```azurepowershell-interactive

# Add your storage account to your Key Vault's managed storage accounts
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Vid lyckad lägga till lagringskontot med inga åtkomstnyckeln återskapades bör du se utdata som liknar följande exempel:

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

### <a name="enable-key-regeneration"></a>Aktivera åtkomstnyckeln återskapades

Om du vill Key Vault för att återskapa dina lagringskontonycklar med jämna mellanrum, kan du ange en återskapandeperiod. I följande exempel anger vi en återskapandeperiod på tre dagar. Efter tre dagar, Key Vault återskapa ”key1” och växla den aktiva nyckeln från ”key2” till ”key1”.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Vid lyckad lägga till lagringskontot med nycklar bör du se utdata som liknar följande exempel:

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

- [Hanterade key storage-konto-exempel](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell-referens](/powershell/module/azurerm.keyvault/)
