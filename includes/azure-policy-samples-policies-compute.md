---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172753"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Tillåtna SKU: er för virtuella datorer](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Med den här principen kan du ange en uppsättning-SKU:er för virtuella datorer som din organisation kan distribuera. |Neka |1.0.0 |
|[Granska virtuella datorer utan haveri beredskap har kon figurer ATS](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Granska virtuella datorer som inte har någon katastrof återställning konfigurerad. Om du vill veta mer om haveri beredskap går du till https://aka.ms/asr-doc. |auditIfNotExists |1.0.0 |
|[Granska virtuella datorer som inte använder hanterade diskar](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Den här principen granskar virtuella datorer som inte använder hanterade diskar |händelse |1.0.0 |
|[Distribuera standard Microsoft IaaSAntimalware-tillägget för Windows Server](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Den här principen distribuerar ett Microsoft IaaSAntimalware-tillägg med en standard konfiguration när en virtuell dator inte har kon figurer ATS med tillägget för program mot skadlig kod. |deployIfNotExists |1.0.0 |
|[Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Vi rekommenderar att du aktiverar loggar så att aktivitets spårningen kan återskapas när undersökningar krävs vid en incident eller en kompromiss. |AuditIfNotExists, inaktiverat |1.0.0 |
|[Microsoft Antimalware för Azure bör konfigureras att automatiskt uppdatera skydds-signaturer](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Den här principen granskar en virtuell Windows-dator som inte har kon figurer ATS med automatisk uppdatering av Microsoft Antimalware-signaturer för virus skydd. |AuditIfNotExists, inaktiverat |1.0.0 |
|[Microsoft IaaSAntimalware-tillägget bör distribueras på Windows-servrar](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Den här principen granskar valfri virtuell Windows Server-dator utan Microsoft IaaSAntimalware-tillägg distribuerat. |AuditIfNotExists, inaktiverat |1.0.0 |
|[Endast godkända VM-tillägg ska installeras](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Den här principen styr de tillägg för virtuella datorer som inte är godkända. |Granska, neka, inaktive rad |1.0.0 |
|[Kräv automatisk uppdatering av operativ system avbildningar på Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Med den här principen aktive ras automatisk uppdatering av operativ system avbildningar på Virtual Machine Scale Sets att alltid ha Virtual Machines säker av att du använder de senaste säkerhets korrigeringarna varje månad. |autentiseringsregel |1.0.0 |
|[Ej anslutna diskar ska vara krypterade](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Den här principen granskar alla frånkopplade diskar utan kryptering aktiverat. |Granskning, inaktive rad |1.0.0 |
|[Virtuella datorer ska migreras till nya Azure Resource Manager-resurser](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |Använd nya Azure Resource Manager för dina virtuella datorer för att ge säkerhets förbättringar som till exempel: starkare åtkomst kontroll (RBAC), bättre granskning, ARM-baserad distribution och styrning, åtkomst till hanterade identiteter, åtkomst till nyckel valv för hemligheter, Azure AD-baserad autentisering och stöd för taggar och resurs grupper för enklare säkerhets hantering |Granska, neka, inaktive rad |1.0.0 |
