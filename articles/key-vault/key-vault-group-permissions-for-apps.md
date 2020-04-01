---
title: Bevilja behörighet till program för åtkomst till ett Azure-nyckelvalv - Azure Key Vault | Microsoft-dokument
description: Lär dig hur du ger behörighet till många program att komma åt ett nyckelvalv
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d5086377b0bb7f3ca2ece643f82a4e45156f1955
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78184884"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Ange autentisering av Key Vault med en åtkomstkontrollprincip

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det enklaste sättet att autentisera ett molnbaserat program till Key Vault är med en hanterad identitet. Se [Använda en hanterad apptjänstidentitet för att komma åt Azure Key Vault](managed-identity.md) för mer information.  Om du skapar ett on-prem-program, gör lokal utveckling eller på annat sätt inte kan använda en hanterad identitet, kan du i stället registrera ett tjänsthuvudnamn manuellt och ge åtkomst till nyckelvalvet med hjälp av en åtkomstkontrollprincip.  

Nyckelvalvet stöder upp till 1024 åtkomstprincipposter, där varje post ger en distinkt uppsättning behörigheter till ett "huvudnamn": Det här är till exempel så konsolappen i [Azure Key Vault-klientbiblioteket för .NET-snabbstarten](quick-create-net.md) kommer åt nyckelvalvet.

Fullständig information om åtkomstkontroll för Key Vault finns i [Azure Key Vault-säkerhet: Identitets- och åtkomsthantering](overview-security.md#identity-and-access-management). Fullständig information om åtkomstkontroll [för nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md) finns i: 

- [Åtkomstkontroll för nycklar](about-keys-secrets-and-certificates.md#key-access-control)
- [Åtkomstkontroll för hemligheter](about-keys-secrets-and-certificates.md#secret-access-control)
- [Åtkomstkontroll för certifikat](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

- Ett nyckelvalv. Du kan använda ett befintligt nyckelvalv eller skapa ett nytt genom att följa stegen i någon av dessa snabbstarter:
   - [Skapa ett nyckelvalv med Azure CLI](quick-create-cli.md)
   - [Skapa ett nyckelvalv med Azure PowerShell](quick-create-powershell.md)
   - [Skapa ett nyckelvalv med Azure-portalen](quick-create-portal.md).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/overview). Du kan också använda [Azure-portalen](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv

Varje nyckelvalvsåtkomstprincippost ger en separat uppsättning behörigheter till ett huvudnamn:

- **Ett program** Om programmet är molnbaserat bör du i stället [använda en hanterad identitet för att komma åt Azure Key Vault](managed-identity.md), om möjligt
- **En Azure AD-grupp** Även om nyckelvalv endast stöder 1024 åtkomstprincipposter kan du lägga till flera program och användare i en enda Azure AD-grupp och sedan lägga till den gruppen som en enda post i åtkomstkontrollprincipen.
- **En användare** Att ge användarna direkt åtkomst till ett nyckelvalv **avskräcks**. Helst bör användare läggas till i en Azure AD-grupp, som i sin tur får åtkomst till nyckelvalvet. Se [Azure Key Vault-säkerhet: Identitets- och åtkomsthantering](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Hämta objectID

Om du vill ge ett program, Azure AD-grupp eller användaråtkomst till nyckelvalvet måste du först hämta dess objectId.

#### <a name="applications"></a>Program

ObjectId för ett program motsvarar dess associerade tjänsthuvudnamn. För fullständig information om tjänstens huvudmän. se [Principobjekt](../active-directory/develop/app-objects-and-service-principals.md)för program och tjänst i Azure Active Directory . 

Det finns två sätt att hämta en objectId för ett program.  Den första är att registrera ditt program med Azure Active Directory. Det gör du genom att följa stegen i snabbstarten [Registrera ett program med Microsofts identitetsplattform](../active-directory/develop/quickstart-register-app.md). När registreringen är klar visas objectID som "Application (client) ID".

Den andra är att skapa ett huvudnamn för tjänsten i ett terminalfönster. Med Azure CLI använder du kommandot [az ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

ObjectId visas i utdata som `clientID`.

Med Azure PowerShell använder du cmdleten [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0)


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

ObjectId visas i utdata som `Id` (inte `ApplicationId`).

#### <a name="azure-ad-groups"></a>Azure AD-grupper

Du kan lägga till flera program och användare i en Azure AD-grupp och sedan ge gruppen åtkomst till nyckelvalvet.  Mer information finns i avsnittet [Skapa och lägga till medlemmar i en Azure AD-grupp](#creating-and-adding-members-to-an-azure-ad-group) nedan.

Om du vill hitta objectId för en Azure AD-grupp med Azure CLI använder du kommandot [az ad group list.](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) På grund av det stora antalet grupper som kan finnas i organisationen `--display-name` bör du också ange en söksträng till parametern.

```azurecli-interactive
az ad group list --display-name <search-string>
```
ObjectId returneras i JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Om du vill hitta objektetId för en Azure AD-grupp med Azure PowerShell använder du cmdleten [Get-AzADGroup.](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) På grund av det stora antalet grupper som kan finnas i din organisation, `-SearchString` kommer du förmodligen vill också ge en söksträng till parametern.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

I utdata visas objectId som `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Användare

Du kan också lägga till en enskild användare i en nyckelvalvs åtkomstkontrollprincip. **Vi rekommenderar inte detta.** Vi rekommenderar istället att du lägger till användare i en Azure AD-grupp och lägger till gruppen i principerna.

Om du ändå vill hitta en användare med Azure CLI använder du kommandot az ad `--id` user [show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) och skickar användarnas e-postadress till parametern.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

Användarens objectId returneras i utdata:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Om du vill hitta en användare med Azure PowerShell använder du cmdleten `-UserPrincipalName` [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) och skickar användarnas e-postadress till parametern.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

Användarens objectId returneras i utdata `Id`som .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Ge huvudmannen tillgång till ditt nyckelvalv

Nu när du har ett objectID för ditt huvudnamn kan du skapa en åtkomstprincip för nyckelvalvet som ger det behörigheter att hämta, lista, ange och ta bort för både nycklar och hemligheter, plus eventuella ytterligare behörigheter som du vill ha.

Med Azure CLI görs detta genom att skicka objectId till kommandot [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Med Azure PowerShell görs detta genom att skicka objectId till [cmdleten Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Skapa och lägga till medlemmar i en Azure AD-grupp

Du kan skapa en Azure AD-grupp, lägga till program och användare i gruppen och ge gruppen åtkomst till nyckelvalvet.  På så sätt kan du lägga till ett antal program i ett nyckelvalv som en enda åtkomstprincippost och eliminerar behovet av att ge användarna direkt åtkomst till ditt nyckelvalv (vilket vi avråder från). Mer information finns i [Hantera app- och resursåtkomst med Hjälp av Azure Active Directory-grupper](../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Ytterligare förutsättningar

Förutom [förutsättningarna ovan](#prerequisites)behöver du behörigheter för att skapa/redigera grupper i din Azure Active Directory-klientorganisation. Om du inte har behörighet kan du behöva kontakta din Azure Active Directory-administratör.

Om du tänker använda PowerShell behöver du även [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Skapa en Azure Active Directory-grupp

Skapa en ny Azure Active Directory-grupp med kommandot Skapa Azure CLI [az-annonsgrupp](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) eller Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) cmdlet.


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

I båda fallen, anteckna de nyskapade grupperna GroupId, eftersom du behöver det för stegen nedan.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Hitta objektIds för dina program och användare

Du hittar objektiderna för dina program med hjälp av Azure CLI `--show-mine` med kommandot az ad sp [list,](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) med parametern.

```azurecli-interactive
az ad sp list --show-mine
```

Hitta objectIds för dina program med Azure PowerShell med cmdleten [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) och skicka en söksträng till parametern. `-SearchString`

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Så här hittar du objektiderna för användarna genom att följa stegen i avsnittet [Användare](#users) ovan.

### <a name="add-your-applications-and-users-to-the-group"></a>Lägga till dina program och användare i gruppen

Lägg nu till objectIds i din nyligen skapade Azure AD-grupp.

Med Azure CLI använder du [az ad group member](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)add `--member-id` , skicka objectId till parametern.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Med Azure PowerShell använder du cmdleten [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) `-MemberObjectId` och skickar objectId till parametern.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Ge AD-gruppen åtkomst till nyckelvalvet

Slutligen ge AD-gruppen behörighet till ditt nyckelvalv med kommandot Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) eller Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) cmdlet. Exempel på exempel finns i avsnittet [Ge programmet, Azure AD eller användaråtkomst till avsnittet nyckelvalv](#give-the-principal-access-to-your-key-vault) ovan.

Programmet behöver också minst en IAM-roll (Identity and Access Management) som tilldelats nyckelvalvet. Annars kommer det inte att kunna logga in och kommer att misslyckas med otillräckliga rättigheter att komma åt prenumerationen.

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault-säkerhet: Hantering av identitet och åtkomst](overview-security.md#identity-and-access-management)
- [Tillhandahålla Key Vault-autentisering med en hanterad apptjänstidentitet](managed-identity.md)
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- [Säkra ditt nyckelvalv.](key-vault-secure-your-key-vault.md)
- [Utvecklarguide för Azure Key Vault](key-vault-developers-guide.md)
- Granska [metodtips för Azure Key Vault](key-vault-best-practices.md)
