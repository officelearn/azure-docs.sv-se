---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 37df0dee28687f5d1e8a43087cc0dd10efe73ae0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79381690"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Diagnostikloggar i batchkonton ska aktiveras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Granskning aktivera diagnostiska loggar. På så sätt kan du återskapa aktivitetsspår som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket äventyras |AuditIfNotExists, Inaktiverad |2.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json)
|[Måttvarningsregler bör konfigureras för batchkonton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Granskningskonfiguration av måttvarningsregler för batchkonto för att aktivera det nödvändiga måttet |AuditIfNotExists, Inaktiverad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json)
