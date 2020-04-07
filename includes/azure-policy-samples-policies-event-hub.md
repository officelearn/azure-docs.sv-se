---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 50b7b71883d924682a8af60ea365745cf6df863d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758498"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från namnområdet Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Event Hub-klienter bör inte använda en åtkomstprincip för namnområdesnivå som ger åtkomst till alla köer och ämnen i ett namnområde. Om du vill anpassa dig till den lägsta behörighetssäkerhetsmodellen bör du skapa åtkomstprinciper på entitetsnivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten |Granskning, neka, inaktiverad |1.0.1 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json)
|[Auktoriseringsregler för eventhub-instansen bör definieras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Granskningsförekomst av auktoriseringsregler för Event Hub-enheter för att bevilja minst privilegierad åtkomst |AuditIfNotExists, Inaktiverad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json)
|[Diagnostikloggar i Event Hub ska aktiveras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Granskning aktivera diagnostiska loggar. På så sätt kan du återskapa aktivitetsspår som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket äventyras |AuditIfNotExists, Inaktiverad |2.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json)
