---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 645494688ed1fb2a29efa083d50e9456f6d2b1b4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172732"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Diagnostikloggar i Azure Data Lake Store ska vara aktive rad](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |Granska aktivering av diagnostikloggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |2.0.0 |
|[Diagnostikloggar i Data Lake Analytics ska vara aktive rad](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |Granska aktivering av diagnostikloggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |2.0.0 |
|[Kräv kryptering för Data Lake Store-konton](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |Den här principen säkerställer att kryptering är aktiverat på alla Data Lake Store-konton |autentiseringsregel |1.0.0 |
