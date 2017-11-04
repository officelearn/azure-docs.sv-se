---
title: RequestDisallowedByPolicy fel med Azure resursprincip | Microsoft Docs
description: Beskriver orsaken till felet RequestDisallowedByPolicy.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: c950fe5444d4eacf3568d355a8dc9c5699645229
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>RequestDisallowedByPolicy fel med Azure resursprincip

Den här artikeln beskriver orsaken till felet RequestDisallowedByPolicy, ger den också lösning för det här felet.

## <a name="symptom"></a>Symtom

När du försöker utföra åtgärden under distributionen kan du få en **RequestDisallowedByPolicy** fel som förhindrar åtgärd från att slutföras. I följande exempel visar felet:

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

### <a name="method-1"></a>Metod 1

I PowerShell anger du den princip-ID som den `Id` parametern för att hämta information om den princip som blockerats av din distribution.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Metod 2 

Ange namnet på definitionen av principen i Azure CLI 2.0: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Lösning

IT-avdelningen kan tillämpa en resursprincip som förhindrar att skapa offentlig IP-adresser, Nätverkssäkerhetsgrupper, användardefinierade vägar eller routningstabeller för säkerhet och efterlevnad. Felmeddelandet i den **symptom** avsnitt visar en princip med namnet **regionPolicyDefinition**. Din princip kan ha ett annat namn.
Arbeta med din IT-avdelningen att granska principer för företagsresurser för att lösa problemet, och så att utföra den begärda åtgärden i enlighet med dessa principer.

Mer information finns i följande artiklar:

- [Översikt över princip för resurs](resource-manager-policy.md)
- [Visa principtilldelningar via portalen](resource-manager-policy-portal.md#view-policy-assignments)
