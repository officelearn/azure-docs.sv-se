---
title: Fel med RequestDisallowedByPolicy
description: Beskriver orsaken till felet RequestDisallowedByPolicy när resurser distribueras med Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477673"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>RequestDisallowedByPolicy-fel med Azure-resursprincip

I den här artikeln beskrivs orsaken till felet RequestDisallowedByPolicy, och det här felet är också lösningen.

## <a name="symptom"></a>Symptom

Under distributionen kan du få ett **RequestDisallowedByPolicy-fel** som hindrar dig från att skapa resurserna. I följande exempel visas felet:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Felsökning

Om du vill hämta information om principen som blockerade distributionen använder du följande metoder:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I PowerShell anger du den `Id` principidentifieraren som parameter för att hämta information om principen som blockerade distributionen.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

I Azure CLI anger du namnet på principdefinitionen:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Lösning

För säkerhet eller efterlevnad kan dina prenumerationsadministratörer tilldela principer som begränsar hur resurser distribueras. Din prenumeration kan till exempel ha en princip som förhindrar att offentliga IP-adresser, nätverkssäkerhetsgrupper, användardefinierade vägar eller flödestabeller skapas. Felmeddelandet i avsnittet **Symptom** visar namnet på principen.
LÃ¶s problemet genom att granska resursprinciperna och bestÃ¶¥ hur resurser som följer dessa principer ska distribueras.

Mer information finns i följande artiklar:

- [Vad är Azure Policy?](../../governance/policy/overview.md)
- [Skapa och hantera principer för att använda kompatibilitet](../../governance/policy/tutorials/create-and-manage.md)
