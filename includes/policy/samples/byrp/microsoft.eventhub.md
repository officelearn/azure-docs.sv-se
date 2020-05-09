---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2e751d187b128e4f9410387d1d11e06bd8c0308d
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838558"
---
|Name |Beskrivning |Påverkan (ar) |Version |GitHub |
|---|---|---|---|---|
|[Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från Event Hub-namnområdet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Event Hub-klienter bör inte använda en åtkomst princip för namn områdes nivå som ger åtkomst till alla köer och ämnen i ett namn område. Om du vill justera med minsta behörighets säkerhets modell bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten |Granska, neka, inaktive rad |1.0.1 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json) |
|[Auktoriseringsregler för Event Hub-instansen måste definieras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Granska förekomst av auktoriseringsregler i Event Hub-entiteter för att bevilja åtkomst med minst privilegier |AuditIfNotExists, inaktiverat |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json) |
|[Distribuera diagnostikinställningar för händelsehubben till Event Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef7b61ef-b8e4-4c91-8e78-6946c6b0023f) |Distribuerar diagnostikinställningar för Event Hub till att strömma till en regional händelsehubben när en händelse hubb som saknar de här diagnostikinställningar skapas eller uppdateras. |DeployIfNotExists, inaktiverat |2.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EventHub_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Distribuera diagnostikinställningar för Event Hub till Log Analytics-arbetsyta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f6e93e8-6b31-41b1-83f6-36e449a42579) |Distribuerar diagnostikinställningar för Event Hub till att strömma till en regional Log Analytics arbets yta när en händelse hubb som saknar de här diagnostikinställningar skapas eller uppdateras. |DeployIfNotExists, inaktiverat |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EventHub_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Diagnostikloggar i Händelsehubben måste vara aktive rad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Granska aktivering av diagnostikloggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |2.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Event Hub bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Den här principen granskar eventuella Händelsehubben som inte är konfigurerade för att använda en tjänst slut punkt för virtuella nätverk. |AuditIfNotExists, inaktiverat |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
