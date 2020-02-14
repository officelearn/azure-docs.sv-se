---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 49934fbb9b317ac99a7c9473164f25436eefb0b0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192899"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Tillåtna SKU:er för lagringskonto](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Med den här principen kan du ange en uppsättning lagrings konto-SKU: er som din organisation kan distribuera. |Neka |1.0.0 |
|[Granska obegränsad nätverks åtkomst till lagrings konton](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Granska obegränsad nätverks åtkomst i brand Väggs inställningarna för ditt lagrings konto. Konfigurera i stället nätverks regler så att endast program från tillåtna nätverk kan komma åt lagrings kontot. För att tillåta anslutningar från vissa Internet-eller lokala klienter kan åtkomst beviljas till trafik från vissa virtuella Azure-nätverk eller offentliga IP-adressintervall för Internet. |Granskning, inaktive rad |1.0.0 |
|[Distribuera Avancerat skydd på lagrings konton](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Den här principen aktiverar Avancerat skydd på lagrings konton. |DeployIfNotExists, inaktiverat |1.0.0 |
|[Geo-redundant lagring ska vara aktiverat för lagrings konton](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Den här principen granskar alla lagrings konton med Geo-redundant lagring inte aktive rad. |Granskning, inaktive rad |1.0.0 |
|[Säker överföring till lagrings konton ska vara aktiverat](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Granska kravet på säker överföring i ditt lagrings konto. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från nätverks lager attacker, till exempel man-in-the-Middle, avlyssning och session-kapning |Granska, neka, inaktive rad |1.0.0 |
|[Lagrings konton ska tillåta åtkomst från betrodda Microsoft-tjänster](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Vissa Microsoft-tjänster som interagerar med storage-konton fungerar från nätverk som inte kan beviljas åtkomst via Nätverksregler. För att den här typen av tjänsten fungerar som avsett, Tillåt att uppsättningen med betrodda Microsoft-tjänster för att kringgå reglerna nätverk. De här tjänsterna använder sedan stark autentisering för att få åtkomst till lagringskontot. |Granska, neka, inaktive rad |1.0.0 |
|[Lagrings konton ska migreras till nya Azure Resource Manager resurser](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |Använd nya Azure Resource Manager för dina lagrings konton för att tillhandahålla säkerhets förbättringar, till exempel: starkare åtkomst kontroll (RBAC), bättre granskning, Azure Resource Manager baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till nyckel valv för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resurs grupper för enklare säkerhets hantering |Granska, neka, inaktive rad |1.0.0 |
