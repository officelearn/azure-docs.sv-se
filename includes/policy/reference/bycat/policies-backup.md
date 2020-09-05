---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4c895a796a23fb62bb5f65e7a82a96916be9c438
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487906"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup ska vara aktiverat för Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Den här principen hjälper dig att granska om Azure Backup-tjänsten är aktive rad för alla virtuella datorer. Azure Backup är en kostnads effektiv lösning för säkerhets kopiering med enkel klickning som fören klar data återställningen och är enklare att aktivera än andra moln tjänster för säkerhets kopiering. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Konfigurera säkerhets kopiering på virtuella datorer för en plats till ett befintligt centralt valv på samma plats](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Den här principen konfigurerar Azure Backup skydd på virtuella datorer på en specifik plats till ett befintligt centralt valv på samma plats. Den gäller endast för de virtuella datorer som inte redan har kon figurer ATS för säkerhets kopiering. Vi rekommenderar att den här principen är tilldelad till högst 200 virtuella datorer. Om principen är tilldelad för fler än 200 virtuella datorer kan det leda till att säkerhets kopieringen utlöses några timmar utöver det definierade schemat. Den här principen kommer att förbättras för att stödja fler VM-avbildningar. |deployIfNotExists, auditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
