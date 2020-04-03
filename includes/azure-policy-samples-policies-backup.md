---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 0447d2e3a5a8f8d39ece7b44522d4a14227360f1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624585"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Azure Backup bör aktiveras för virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Den här principen hjälper till att granska om Azure Backup-tjänsten är aktiverad för alla virtuella datorer. Azure Backup är en kostnadseffektiv säkerhetskopieringslösning med ett klick förenklar dataåterställning och är enklare att aktivera än andra molnsäkerhetstjänster för säkerhetskopiering. |AuditIfNotExists, Inaktiverad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Konfigurera säkerhetskopiering på virtuella datorer på en plats till ett befintligt centralt arkiv på samma plats](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Den här principen konfigurerar Azure Backup-skydd på virtuella datorer på en viss plats till ett befintligt centralt valv på samma plats. Det gäller endast de virtuella datorer som inte redan är konfigurerade för säkerhetskopiering. Vi rekommenderar att den här principen tilldelas högst 200 virtuella datorer. Om principen har tilldelats för mer än 200 virtuella datorer kan det leda till att säkerhetskopieringen utlöses några timmar utöver det definierade schemat. Den här principen kommer att förbättras för att stödja fler VM-avbildningar. |deployIfNotExists, auditIfNotExists, disabled deployIfNotExists, auditIfNotExists, disabled DeployIfNotExists, auditIfNotExists, disabled deployIf |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
