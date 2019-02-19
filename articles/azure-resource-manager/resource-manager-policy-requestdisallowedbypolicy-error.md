---
title: Fel med RequestDisallowedByPolicy med Azure-resurs-princip | Microsoft Docs
description: Beskriver orsaken till fel med RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d05ec1835bc04be0ca0fc12f41322c14b5420213
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337824"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Fel med RequestDisallowedByPolicy med Azure-resurs-princip

Den här artikeln beskriver orsaken till fel med RequestDisallowedByPolicy, ger även lösning för det här felet.

## <a name="symptom"></a>Symtom

Under distributionen kan du få en **RequestDisallowedByPolicy** fel som förhindrar dig att skapa resurser. I följande exempel visas felet:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Felsökning

Använd det följande av metoder för att hämta information om principen som blockerade din distribution:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I PowerShell, anger du den princip-ID som den `Id` parametern för att hämta information om principen som blockerade din distribution.

```PowerShell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Ange namnet på principdefinitionen i Azure CLI:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Lösning

Prenumerationens administratörer kan tilldela principer som begränsar hur resurser distribueras för säkerhet eller efterlevnad. Till exempel kan din prenumeration har en princip som förhindrar skapa offentlig IP-adresser, Nätverkssäkerhetsgrupperna, användardefinierade vägar eller routningstabeller. Felmeddelandet i den **symptom** visar namnet på principen.
Lös problemet genom att granska resursprinciperna och kontrollera hur du distribuerar resurser som är kompatibla med dessa principer.

Mer information finns i följande artiklar:

- [Vad är Azure Policy?](../governance/policy/overview.md)
- [Skapa och hantera principer för att tvinga kompatibilitet](../governance/policy/tutorials/create-and-manage.md)
