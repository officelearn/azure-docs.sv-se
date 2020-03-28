---
title: Ändra klient-ID:n för nyckelvalv efter ett prenumerationssteg – Azure Key Vault | Microsoft-dokument
description: Lär dig hur du växlar klient-ID för ett nyckelvalv när en prenumeration flyttas till en annan klient
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 2b262823323e9491965bc16818220b2f80cf236a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79457312"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Ändra nyckelvalvsklient-ID efter en prenumerationsflytt

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


När du skapar ett nytt nyckelvalv i en prenumeration knyts det automatiskt till det standardmässiga Azure Active Directory-klient-ID:t för den prenumerationen. Alla åtkomstprincipposter knyts också till detta klient-ID. 

Om du flyttar din Azure-prenumeration från klient A till klient B är dina befintliga nyckelvalv otillgängliga av huvudmännen (användare och program) i klient B. För att åtgärda problemet måste du:

* Ändra klient-ID som är associerat med alla befintliga nyckelvalv i prenumerationen till klient B.
* Ta bort alla åtkomstprincipposter.
* Lägg till nya åtkomstprincipposter som är associerade med klient B.

Om du till exempel har nyckelvalvet "myvault" i en prenumeration som har flyttats från klient A till klient B, kan du använda Azure PowerShell för att ändra klient-ID:t och ta bort gamla åtkomstprinciper.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Eller så kan du använda Azure CLI.

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Nu när ditt valv är associerat med rätt klient-ID och gamla åtkomstprincipposter tas bort, ange nya åtkomstprincipposter med Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) cmdlet eller Azure CLI [az keyvault set-policy-kommandot.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

Om du använder en hanterad identitet för Azure-resurser måste du också uppdatera den till den nya Azure AD-klienten. Mer information om hanterade identiteter finns i [Tillhandahålla nyckelvalvsautentisering med en hanterad identitet](managed-identity.md).


Om du använder MSI måste du också uppdatera MSI-identiteten eftersom den gamla identiteten inte längre finns i rätt AAD-klientorganisation.

## <a name="next-steps"></a>Nästa steg

Om du har några frågor om Azure Key Vault kan du besöka [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
