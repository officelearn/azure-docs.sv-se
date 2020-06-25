---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a8527e2916baa1619e4649a3273d42169c8fcbb
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322779"
---
|Namn<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Den här principen hjälper dig att granska om Azure Backup-tjänsten är aktive rad för alla virtuella datorer. Azure Backup är en kostnads effektiv lösning för säkerhets kopiering med enkel klickning som fören klar data återställningen och är enklare att aktivera än andra moln tjänster för säkerhets kopiering. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Konfigurera säkerhets kopiering på virtuella datorer för en plats till ett befintligt centralt valv på samma plats](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Den här principen konfigurerar Azure Backup skydd på virtuella datorer på en specifik plats till ett befintligt centralt valv på samma plats. Den gäller endast för de virtuella datorer som inte redan har kon figurer ATS för säkerhets kopiering. Vi rekommenderar att den här principen är tilldelad till högst 200 virtuella datorer. Om principen är tilldelad för fler än 200 virtuella datorer kan det leda till att säkerhets kopieringen utlöses några timmar utöver det definierade schemat. Den här principen kommer att förbättras för att stödja fler VM-avbildningar. |deployIfNotExists, auditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Distribuera diagnostikinställningar för Recovery Services valv till Log Analytics arbets yta för resursbaserade kategorier.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Distribuera diagnostikinställningar för Recovery Services valv för att strömma till Log Analytics arbets ytan för resursbaserade kategorier. Om någon av resurs kategorierna inte är aktiverade skapas en ny diagnostisk inställning. |deployIfNotExists |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
