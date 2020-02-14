---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: cce1a633efb21dc7616e4df941b87bd7aba1db98
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192508"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Diagnostikloggar i batch-konton måste vara aktiverade](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |Granska aktivering av diagnostikloggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |2.0.0 |
|[Mått varnings regler ska konfigureras för batch-konton](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |Granska konfigurationen av mått varnings regler för batch-kontot för att aktivera nödvändigt mått |AuditIfNotExists, inaktiverat |1.0.0 |
