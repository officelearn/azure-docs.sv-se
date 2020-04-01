---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299421"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Azure Backup bör aktiveras för virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Den här principen hjälper till att granska om Azure Backup-tjänsten är aktiverad för alla virtuella datorer. Azure Backup är en kostnadseffektiv säkerhetskopieringslösning med ett klick förenklar dataåterställning och är enklare att aktivera än andra molnsäkerhetstjänster för säkerhetskopiering. |AuditIfNotExists, Inaktiverad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Konfigurera säkerhetskopiering på virtuella datorer på en plats till ett befintligt centralt arkiv på samma plats](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Den här principen konfigurerar Azure Backup-skydd på virtuella datorer på en viss plats till ett befintligt centralt valv på samma plats. Det gäller endast de virtuella datorer som inte redan är konfigurerade för säkerhetskopiering. Vi rekommenderar att den här principen tilldelas högst 200 virtuella datorer. Om principen har tilldelats för mer än 200 virtuella datorer kan det leda till att säkerhetskopieringen utlöses några timmar utöver det definierade schemat. Den här principen kommer att förbättras för att stödja fler VM-avbildningar. |deployIfNotExists, auditIfNotExists, disabled deployIfNotExists, auditIfNotExists, disabled DeployIfNotExists, auditIfNotExists, disabled deployIf |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
