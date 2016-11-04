---
title: Ändra nyckelvalvsklient-ID efter prenumerationsflytt | Microsoft Docs
description: Lär dig hur du växlar klient-ID för ett nyckelvalv när en prenumeration flyttas till en annan klient
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# Ändra nyckelvalvsklient-ID efter prenumerationsflytt
### F: Min prenumeration har flyttats från klient A till klient B. Hur ändrar jag klient-ID för mitt befintliga nyckelvalv och anger rätt åtkomstkontrollistor för säkerhetsobjekt i klient B?
När du skapar ett nytt nyckelvalv i en prenumeration knyts det automatiskt till det standardmässiga Azure Active Directory-klient-ID:t för den prenumerationen. Alla åtkomstprincipposter knyts också till detta klient-ID. När du flyttar din Azure-prenumeration från klient A till klient B är ditt befintliga nyckelvalv otillgängligt för säkerhetsobjekten (användare och program) i klient B. Om du vill åtgärda problemet måste du

* ändra det klient-ID som är associerat med alla befintliga nyckelvalv i den här prenumerationen till klient B
* ta bort alla åtkomstprincipposter
* lägga till nya åtkomstprincipposter som är associerade med klient B.

Om du till exempel har nyckelvalvet ”myvault” i en prenumeration som har flyttats från klient A till klientens B gör du så här för att ändra klient-ID:t för detta nyckelvalv och ta bort gamla åtkomstprinciper.

<pre>
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId $vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties $vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId $vault.Properties.AccessPolicies = @() Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Eftersom den här valvet fanns på klient A före flytten är ursprungsvärdet **$vault. Properties.TenantId** klient A, medan **(Get-AzureRmContext). Tenant.TenantId** är klient B.

Nu när ditt valv är associerat med rätt klient-ID och gamla åtkomstprincipposter har tagits bort ska du ange nya åtkomstprincipposter med [Set AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## Nästa steg
* Om du har några frågor om Nyckelvalv ska du gå till [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!--HONumber=Sep16_HO3-->


