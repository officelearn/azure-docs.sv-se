---
title: Azure Key Vault att flytta ett valv till en annan prenumeration | Microsoft Docs
description: Vägledning om hur du flyttar ett nyckel valv till en annan prenumeration.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: e0cd4cad74257dbf83ec8d30405eacca341a8d31
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289523"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Flytta en Azure Key Vault till en annan prenumeration

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> **Om du flyttar ett nyckel valv till en annan prenumeration ändras din miljö.**
> Se till att du förstår effekten av den här ändringen och följ anvisningarna i den här artikeln noggrant innan du bestämmer dig för att flytta nyckel valvet till en ny prenumeration.
> Om du använder hanterade tjänst identiteter (MSI) läser du anvisningarna efter flytten i slutet av dokumentet. 

När du skapar ett nyckel valv knyts det automatiskt till standard Azure Active Directory klient-ID: t för den prenumeration som den skapas i. Alla åtkomstprincipposter knyts också till detta klient-ID. Om du flyttar din Azure-prenumeration från klient A till klient B, blir dina befintliga nyckel valv otillgängliga för tjänstens huvud namn (användare och program) i klient B. För att åtgärda det här problemet måste du:

* Ändra det klient-ID som är associerat med alla befintliga nyckel valv i prenumerationen till klient B.
* Ta bort alla åtkomstprincipposter.
* Lägg till nya åtkomst princip poster som är associerade med klient B.

## <a name="limitations"></a>Begränsningar

> [!IMPORTANT]
> **Det går inte att flytta nyckel valv som används för disk kryptering** Om du använder Key Vault med disk kryptering för en virtuell dator kan nyckel valvet inte flyttas till en annan resurs grupp eller en prenumeration när disk kryptering är aktiverat. Du måste inaktivera disk kryptering innan du flyttar nyckel valvet till en ny resurs grupp eller prenumeration. 

Vissa tjänst huvud namn (användare och program) är kopplade till en viss klient. Om du flyttar nyckel valvet till en prenumeration i en annan klient, finns det en risk att du inte kan återställa åtkomsten till ett särskilt tjänst huvud namn. Kontrol lera att alla viktiga tjänst huvud namn finns i klient organisationen där du flyttar nyckel valvet.

## <a name="design-considerations"></a>Designöverväganden

Din organisation kan ha implementerat Azure Policy med tvång eller undantag på prenumerations nivån. Det kan finnas en annan uppsättning princip tilldelningar i prenumerationen där ditt nyckel valv finns och prenumerationen där du flyttar nyckel valvet. En konflikt i princip krav har möjlighet att bryta dina program.

### <a name="example"></a>Exempel

Du har ett program anslutet till nyckel valv som skapar certifikat som är giltiga i två år. Prenumerationen där du försöker flytta nyckel valvet har en princip tilldelning som blockerar skapandet av certifikat som är giltiga i mer än ett år. När du har flyttat nyckel valvet till den nya prenumerationen, blockeras Åtgärden för att skapa ett certifikat som är giltigt under två år av en Azure policy-tilldelning.

### <a name="solution"></a>Lösning

Se till att gå till Azure Policy sidan på Azure Portal och titta på princip tilldelningarna för din aktuella prenumeration samt den prenumeration som du flyttar till och se till att det inte finns några fel matchningar.

## <a name="prerequisites"></a>Krav

* Deltagar nivå åtkomst eller högre till den aktuella prenumerationen där nyckel valvet finns.
* Deltagar nivå åtkomst eller högre till den prenumeration där du vill flytta nyckel valvet.
* En resurs grupp i den nya prenumerationen.

## <a name="procedure"></a>Procedur

### <a name="moving-key-vault-to-a-new-subscription-within-the-same-tenant"></a>Flytta Key Vault till en ny prenumeration inom samma klient organisation

1. Logga in på Azure Portal
2. Navigera till ditt nyckel valv
3. Klicka på fliken "Översikt"
4. Välj knappen "flytta"
5. Välj "flytta till en annan prenumeration" från List Rute alternativen
6. Välj den resurs grupp där du vill flytta nyckel valvet
7. Bekräfta varningen om att flytta resurser
8. Välj OK

### <a name="additional-steps-if-you-moved-key-vault-to-a-subscription-in-a-new-tenant"></a>Ytterligare steg om du har flyttat nyckel valvet till en prenumeration i en ny klient

Om du har flyttat ditt nyckel valv till en prenumeration i en ny klient måste du uppdatera klient-ID: t manuellt och ta bort gamla åtkomst principer. Här är självstudier för de här stegen i PowerShell och Azure CLI. Om du använder PowerShell kan du behöva köra kommandot Clear-AzContext som dokumenteras nedan så att du kan se resurser utanför det valda omfånget. 

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Nu när ditt valv är associerat med rätt klient-ID och gamla åtkomst princip poster tas bort, anger du nya åtkomst princip poster med Azure PowerShell cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) eller Azure CLI [-AZ](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) för nyckel valv.

Om du använder en hanterad identitet för Azure-resurser måste du också uppdatera den till den nya Azure Active Directory-klienten. För ytterligare information om hanterade identiteter, [Översikt över hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md).

Om du använder hanterad identitet måste du också uppdatera identiteten eftersom den gamla identiteten inte längre kommer att ha rätt Azure Active Directory klient. Se följande dokument för att hjälpa till att lösa det här problemet. 

* [Uppdaterar MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Överför prenumeration till ny katalog](../../role-based-access-control/transfer-subscription.md)