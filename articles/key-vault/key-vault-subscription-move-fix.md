---
title: "Ändra nyckelvalvsklient-ID efter en prenumerationsflytt | Microsoft Docs"
description: "Lär dig hur du växlar klient-ID för ett nyckelvalv när en prenumeration flyttas till en annan klient"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: 2f007dd4f877b48003cddcefa5f4321049853361
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Ändra nyckelvalvsklient-ID efter en prenumerationsflytt
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>F: Min prenumeration har flyttats från klient A till klient B. Hur ändrar jag klient-ID för mitt befintliga nyckelvalv och anger rätt åtkomstkontrollistor för säkerhetsobjekt i klient B?
När du skapar ett nytt nyckelvalv i en prenumeration knyts det automatiskt till det standardmässiga Azure Active Directory-klient-ID:t för den prenumerationen. Alla åtkomstprincipposter knyts också till detta klient-ID. När du flyttar din Azure-prenumeration från klient A till klient B är ditt befintliga nyckelvalv otillgängligt för säkerhetsobjekten (användare och program) i klient B. Om du vill åtgärda problemet måste du:

* Ändra det klient-ID som är associerat med alla befintliga nyckelvalv i den här prenumerationen till klient B.
* Ta bort alla åtkomstprincipposter.
* Lägga till nya åtkomstprincipposter som är associerade med klient B.

Om du till exempel har nyckelvalvet ”myvault” i en prenumeration som har flyttats från klient A till klientens B gör du så här för att ändra klient-ID:t för detta nyckelvalv och ta bort gamla åtkomstprinciper.

<pre>
$Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Eftersom det här valvet fanns på klient A innan flytten är ursprungsvärdet **$vault. Properties.TenantId** klient A, medan **(Get-AzureRmContext). Tenant.TenantId** är klient B.

Nu när ditt valv är associerat med rätt klient-ID och gamla åtkomstprincipposter har tagits bort ska du ange nya åtkomstprincipposter med [Set AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Nästa steg
Om du har några frågor om Azure Key Vault kan du besöka [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

