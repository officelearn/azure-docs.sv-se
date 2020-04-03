---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 888f8954ede7b733b89a4c3215a889eae6825e4d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624065"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Behållarregister ska krypteras med en CUSTOMER-Managed Key (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Granskningsbehållareregister som inte har kryptering aktiverat med CUSTOMER-Managed Keys (CMK). För mer information om CMK-kryptering, besök: https://aka.ms/acr/CMK. |Granskning, inaktiverad |1.0.0-förhandsvisning |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[Containerregister bör inte tillåta obegränsad nätverksåtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Granskningsbehållareregister som inte har några IP- eller VNET-regler konfigurerade och tillåter all nätverksåtkomst som standard. Container register med minst en IP / Brandvägg regel eller konfigurerat virtuellt nätverk kommer att anses vara kompatibel. Mer information om reglerna för containerregisternätverk https://aka.ms/acr/vnetfinns på: . |Granskning, inaktiverad |1.0.0-förhandsvisning |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
