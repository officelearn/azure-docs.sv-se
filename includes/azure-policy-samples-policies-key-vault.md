---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 7b2179c0a924f7fc29aa70ff748d435e664980ae
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192948"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Distribuera diagnostikinställningar för Key Vault till Händelsehubben](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Distribuerar diagnostikinställningar för Key Vault att strömma till en regional händelsehubben när en Key Vault som saknar dessa diagnostikinställningar skapas eller uppdateras. |deployIfNotExists |1.0.0 |
|[Diagnostikloggar i Key Vault ska vara aktive rad](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Granska aktivering av diagnostikloggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |1.0.0 |
|[Key Vault objekt ska vara återställnings bara](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Den här principen granskar om det inte går att återskapa nyckel valvs objekt. Med funktionen för mjuk borttagning kan du effektivt hålla resurserna under en viss kvarhållningsperiod (90 dagar) även efter en BORTTAGNINGs åtgärd, samtidigt som du ger det utseende som objektet tas bort. När ' Rensa skydd ' är på kan ett valv eller ett objekt i Borttaget läge inte rensas förrän kvarhållningsperioden på 90 dagar har passerat. Dessa valv och objekt kan fortfarande återställas, vilket gör det möjligt för kunderna att följa bevarande principen. |Granskning, inaktive rad |1.0.0 |
