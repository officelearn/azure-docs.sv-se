---
title: Ändra klient-ID för Key Vault efter att prenumerationen har flyttats Azure Key Vault | Microsoft Docs
description: Lär dig hur du växlar klient-ID för ett nyckelvalv när en prenumeration flyttas till en annan klient
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 87025767725142cc2f861ff8b390d6ea916f8e38
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947721"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Ändra nyckelvalvsklient-ID efter en prenumerationsflytt

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>F: Min prenumeration har flyttats från klient A till klient B. Hur gör jag för att ändra klient-ID för mitt befintliga nyckelvalv och ange rätt åtkomstkontrollistor för säkerhetsobjekt i klient B?

När du skapar ett nytt nyckelvalv i en prenumeration knyts det automatiskt till det standardmässiga Azure Active Directory-klient-ID:t för den prenumerationen. Alla åtkomstprincipposter knyts också till detta klient-ID. När du flyttar din Azure-prenumeration från klient A till klient B är ditt befintliga nyckelvalv otillgängligt för säkerhetsobjekten (användare och program) i klient B. Om du vill åtgärda problemet måste du:

* Ändra det klient-ID som är associerat med alla befintliga nyckelvalv i den här prenumerationen till klient B.
* Ta bort alla åtkomstprincipposter.
* Lägga till nya åtkomstprincipposter som är associerade med klient B.

Om du till exempel har nyckelvalvet ”myvault” i en prenumeration som har flyttats från klient A till klientens B gör du så här för att ändra klient-ID:t för detta nyckelvalv och ta bort gamla åtkomstprinciper.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID                   # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your Keyvault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your Keyvault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your Keyvault resides in
$vault.Properties.AccessPolicies = @()                                     # Accesspolicies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                                                                              # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the kevault's properties.
</pre>

Eftersom det här valvet var i klient organisationen A före flytten, det ursprungliga värdet för **$Vault. Egenskaper. TenantId** är klient A, medan **(Get-AzContext). Tenant. TenantId** är klient B.

Nu när ditt valv är associerat med rätt klient-ID och gamla åtkomst princip poster tas bort, anger du nya åtkomst princip poster med [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

Om du använder MSI måste du också uppdatera MSI-identiteten eftersom den gamla identiteten inte längre kommer att finnas i rätt AAD-klient.

## <a name="next-steps"></a>Nästa steg

Om du har några frågor om Azure Key Vault kan du besöka [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
