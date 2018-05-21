---
title: RequestDisallowedByPolicy fel med Azure resursprincip | Microsoft Docs
description: Beskriver orsaken till felet RequestDisallowedByPolicy.
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
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: 474400d92660b68fd7fef906216b8e37c6e8c94d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>RequestDisallowedByPolicy fel med Azure resursprincip

Den här artikeln beskriver orsaken till felet RequestDisallowedByPolicy, ger den också lösning för det här felet.

## <a name="symptom"></a>Symtom

Under distributionen kan du få en **RequestDisallowedByPolicy** fel som förhindrar att skapa resurser. I följande exempel visar felet:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Felsökning

Använd följande någon av metoderna för att hämta information om den princip som blockeras distributionen:

### <a name="powershell"></a>PowerShell

I PowerShell anger du den princip-ID som den `Id` parametern för att hämta information om den princip som blockerats av din distribution.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Ange namnet på definitionen av principen i Azure CLI 2.0:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Lösning

För säkerhets- eller efterlevnad kan prenumerationsadministratörer tilldela principer som begränsar hur resurser har distribuerats. Din prenumeration kan till exempel har en princip som förhindrar att skapa offentlig IP-adresser, Nätverkssäkerhetsgrupper, användardefinierade vägar eller routningstabeller. Felmeddelandet i den **symptom** avsnitt visar namnet på principen.
Lös problemet, granska principer för företagsresurser och bestämma hur du distribuerar resurser som är kompatibla med dessa principer.

Mer information finns i följande artiklar:

- [Vad är Azure principen?](../azure-policy/azure-policy-introduction.md)
- [Skapa och hantera principer för att tvinga kompatibilitet](../azure-policy/create-manage-policy.md)
