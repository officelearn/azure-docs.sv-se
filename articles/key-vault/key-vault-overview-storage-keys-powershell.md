---
title: Hanterat lagringskonto för Azure Key Vault - PowerShell-version
description: Funktionen för hanterade lagringskonto ger en sömlös integrering mellan Azure Key Vault och ett Azure-lagringskonto.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 833f78d89a1a9033e62c10c3b16c5adfc65e1da4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195132"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Hantera lagringskontonycklar med Key Vault och Azure PowerShell

Ett Azure-lagringskonto använder autentiseringsuppgifter som består av ett kontonamn och en nyckel. Nyckeln är autogenererad och fungerar som ett lösenord, snarare än en kryptografisk nyckel. Key Vault hanterar lagringskontonycklar genom att lagra dem som [Key Vault-hemligheter](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Du kan använda nyckelnyckelfunktionen för hanterade lagringskontonycklar för att lista (synkronisera) nycklar med ett Azure-lagringskonto och återskapa (rotera) nycklarna med jämna mellanrum. Du kan hantera nycklar för både lagringskonton och klassiska lagringskonton.

När du använder nyckelfunktionen för hanterade lagringskonton bör du tänka på följande:

- Nyckelvärden returneras aldrig som svar på en uppringare.
- Endast Key Vault ska hantera dina lagringskontonycklar. Hantera inte nycklarna själv och undvik att störa Key Vault-processer.
- Endast ett key vault-objekt ska hantera lagringskontonycklar. Tillåt inte nyckelhantering från flera objekt.
- Du kan begära att Key Vault hanterar ditt lagringskonto med ett användarobjekt, men inte med ett huvudnamn för tjänsten.
- Återskapa nycklar med endast Key Vault. Återskapa inte dina lagringskontonycklar manuellt.

Vi rekommenderar att du använder Azure Storage-integrering med Azure Active Directory (Azure AD), Microsofts molnbaserade identitets- och åtkomsthanteringstjänst. Azure AD-integrering är tillgänglig för [Azure-blobbar och köer](../storage/common/storage-auth-aad.md)och ger OAuth2 tokenbaserad åtkomst till Azure Storage (precis som Azure Key Vault).

Med Azure AD kan du autentisera klientprogrammet med hjälp av ett program eller en användaridentitet i stället för autentiseringsuppgifter för lagringskonto. Du kan använda en [Azure AD-hanterad identitet](/azure/active-directory/managed-identities-azure-resources/) när du kör på Azure. Hanterade identiteter tar bort behovet av klientautentisering och lagring av autentiseringsuppgifter i eller med ditt program.

Azure AD använder rollbaserad åtkomstkontroll (RBAC) för att hantera auktorisering, som också stöds av Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Tjänsthuvudnamns-ID

En Azure AD-klient ger varje registrerat program ett [huvudnamn](/azure/active-directory/develop/developer-glossary#service-principal-object)för tjänsten . Tjänstens huvudnamn fungerar som program-ID, som används under auktoriseringsinställningar för åtkomst till andra Azure-resurser via RBAC.

Key Vault är ett Microsoft-program som är förregistrerat i alla Azure AD-klienter. Key Vault registreras under samma program-ID i varje Azure-moln.

| Klientorganisationer | Molnet | Program-ID:t |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, offentlig | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Annat  | Alla | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här guiden måste du först göra följande:

- [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Skapa ett nyckelvalv](quick-create-powershell.md)
- [Skapa ett Azure-lagringskonto](../storage/common/storage-account-create.md?tabs=azure-powershell). Lagringskontonamnet får endast använda gemener och siffror. Namnets längd måste vara mellan 3 och 24 tecken.
      

## <a name="manage-storage-account-keys"></a>Hantera lagringskontonycklar

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Autentisera powershell-sessionen med cmdlet [connect-azaccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 

```azurepowershell-interactive
Connect-AzAccount
```
Om du har flera Azure-prenumerationer kan du lista dem med cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) och ange vilken prenumeration du vill använda med cmdleten [Set-AzContext.](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Ange variabler

Ange först de variabler som ska användas av PowerShell-cmdlets i följande steg. Var noga med <YourResourceGroupName> <YourStorageAccountName>att <YourKeyVaultName> uppdatera , och platshållare och `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` ange $keyVaultSpAppId till (som anges i [Tjänstens huvudprogram-ID](#service-principal-application-id)ovan).

Vi kommer också att använda cmdleterna Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) och [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) för att få ditt användar-ID och kontexten för ditt Azure-lagringskonto.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Ge Key Vault åtkomst till ditt lagringskonto

Innan Key Vault kan komma åt och hantera dina lagringskontonycklar måste du auktorisera dess åtkomst till ditt lagringskonto. Key Vault-programmet kräver behörigheter för att *lista* och *återskapa* nycklar för ditt lagringskonto. Dessa behörigheter aktiveras via den inbyggda RBAC-rollen [Storage Account Key Operator Service Role](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Tilldela den här rollen till huvudmannen för Key Vault-tjänsten, vilket begränsar omfattningen till ditt lagringskonto med hjälp av cmdleten Azure PowerShell [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0)

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

När du har slutfört rolltilldelningen bör du se utdata som liknar följande exempel:

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

Om Key Vault redan har lagts till i rollen på ditt lagringskonto får du en *"Rolltilldelningen finns redan".* error. Du kan också verifiera rolltilldelningen med hjälp av sidan "Åtkomstkontroll (IAM)" i Azure-portalen.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Ge ditt användarkonto behörighet till hanterade lagringskonton

Använd cmdleten Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) för att uppdatera åtkomstprincipen för Key Vault och bevilja lagringskontobehörigheter till ditt användarkonto.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Observera att behörigheter för lagringskonton inte är tillgängliga på sidan "Åtkomstprinciper" för lagringskontot i Azure-portalen.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Lägga till ett hanterat lagringskonto i Key Vault-instansen

Använd cmdleten Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) för att skapa ett hanterat lagringskonto i key vault-instansen. Växeln `-DisableAutoRegenerateKey` anger ATT inte återskapa lagringskontonycklarna.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

När lagringskontot har lagts till utan nyckelförnyelse bör du se utdata som liknar följande exempel:

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

### <a name="enable-key-regeneration"></a>Aktivera nyckelförnyelse

Om du vill att Key Vault ska återskapa dina lagringskontonycklar med jämna mellanrum kan du använda cmdleten Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) för att ange en förnyelseperiod. I det här exemplet anger vi en regenereringsperiod på tre dagar. Efter tre dagar kommer Key Vault att återskapa "key2" och byta ut den aktiva nyckeln från "key2" till "key1".

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

När lagringskontot har lagts till med nyckelförnyelsen bör du se utdata som liknar följande exempel:

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

## <a name="shared-access-signature-tokens"></a>Signaturtoken för delad åtkomst

Du kan också be Key Vault att generera signaturtoken för delad åtkomst. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Du kan ge klienter åtkomst till resurser i ditt lagringskonto utan att dela dina kontonycklar. En signatur för delad åtkomst ger dig ett säkert sätt att dela dina lagringsresurser utan att kompromissa med dina kontonycklar.

Kommandona i det här avsnittet utför följande åtgärder:

- Ange en signaturdefinition för delad åtkomst för konto. 
- Skapa en signaturtoken för delad åtkomst för ett konto för Blob-, File-, Table- och Queue-tjänster. Token skapas för resurstyper Tjänst, Behållare och Objekt. Token skapas med alla behörigheter, över https och med de angivna start- och slutdatumen.
- Ange en signaturdefinition för delad åtkomst för Key Vault-hanterad åtkomst i valvet. Definitionen har mallen URI för signaturtoken för delad åtkomst som skapades. Definitionen har signaturtypen `account` delad åtkomst och är giltig i N dagar.
- Kontrollera att signaturen för delad åtkomst har sparats i nyckelvalvet som en hemlighet.
- 
### <a name="set-variables"></a>Ange variabler

Ange först de variabler som ska användas av PowerShell-cmdlets i följande steg. Var noga med <YourStorageAccountName> <YourKeyVaultName> att uppdatera och platshållare.

Vi kommer också att använda Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) cmdlets för att få kontexten för ditt Azure-lagringskonto.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Skapa en signaturtoken för delad åtkomst

Skapa en signaturdefinition för delad åtkomst med azure powershell [new-azstorageaccountSASToken-cmdlets.](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0)
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Värdet på $sasToken kommer att se ut ungefär så här.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generera en signaturdefinition för delad åtkomst

Använd cmdleten Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) för att skapa en signaturdefinition för delad åtkomst.  Du kan ange namnet på `-Name` valfrit till parametern.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifiera signaturdefinitionen för delad åtkomst

Du kan kontrollera att signaturdefinitionen för delad åtkomst har lagrats i nyckelvalvet med azure powershell [get-azkeyvaultsecret-cmdlet.](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0)

Leta först reda på signaturdefinitionen för delad åtkomst i nyckelvalvet.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Hemligheten som motsvarar SAS-definitionen har följande egenskaper:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Du kan nu använda cmdleten [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) och egenskapen secret `Name` för att visa innehållet i hemligheten.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

Utdata för det här kommandot visar din SAS-definitionssträng.


## <a name="next-steps"></a>Nästa steg

- [Viktiga exempel på hanterade lagringskonto](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- [PowerShell-referens för Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
