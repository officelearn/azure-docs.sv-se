---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 6497a1444b3f502d3e5169ff8ace2884e4e045c9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758825"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Behållarregister ska krypteras med en CUSTOMER-Managed Key (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Granskningsbehållareregister som inte har kryptering aktiverat med CUSTOMER-Managed Keys (CMK). För mer information om CMK-kryptering, besök: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Granskning, inaktiverad |1.0.0-förhandsvisning |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Containerregister bör inte tillåta obegränsad nätverksåtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Granskningsbehållareregister som inte har några IP- eller VNET-regler konfigurerade och tillåter all nätverksåtkomst som standard. Container register med minst en IP / Brandvägg regel eller konfigurerat virtuellt nätverk kommer att anses vara kompatibel. Mer information om reglerna för containerregisternätverk [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet)finns på: . |Granskning, inaktiverad |1.0.0-förhandsvisning |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
