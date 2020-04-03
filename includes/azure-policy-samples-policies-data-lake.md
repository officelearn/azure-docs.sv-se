---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 16576e4076ebb6d361a4b0db699997edf83393ea
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80623984"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Diagnostikloggar i Azure Data Lake Store ska aktiveras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Granskning aktivera diagnostiska loggar. På så sätt kan du återskapa aktivitetsspår som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket äventyras |AuditIfNotExists, Inaktiverad |2.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json)
|[Diagnostikloggar i Data Lake Analytics bör aktiveras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Granskning aktivera diagnostiska loggar. På så sätt kan du återskapa aktivitetsspår som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket äventyras |AuditIfNotExists, Inaktiverad |2.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json)
|[Kräv kryptering på DataSjölagringskonton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Den här principen säkerställer att kryptering är aktiverat på alla Data Lake Store-konton |Förneka |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json)
