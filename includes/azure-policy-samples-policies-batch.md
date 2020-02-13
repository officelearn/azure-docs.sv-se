---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 59a47ca7d3b3b42a5822e61c37ee90cb238c0778
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172718"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Diagnostikloggar i batch-konton måste vara aktiverade](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |Granska aktivering av diagnostikloggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |2.0.0 |
|[Mått varnings regler ska konfigureras för batch-konton](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |Granska konfigurationen av mått varnings regler för batch-kontot för att aktivera nödvändigt mått |AuditIfNotExists, inaktiverat |1.0.0 |
