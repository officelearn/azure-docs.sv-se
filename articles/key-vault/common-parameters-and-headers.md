---
title: Vanliga parametrar och sidhuvuden
description: Parametrar och rubriker som är gemensamma för alla åtgärder som du kan göra rör Key Vault-resurser.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 01ac02ca0e449dcac8fcd05450566fd8138acba3
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "64696694"
---
# <a name="common-parameters-and-headers"></a>Vanliga parametrar och sidhuvuden

Följande information är gemensamma för alla åtgärder som du kan göra rör Key Vault-resurser:

- Ersätt `{api-version}` med api-versionen i URI: N.
- Ersätt `{subscription-id}` med ditt prenumerations-ID i URI: N
- Ersätt `{resource-group-name}` med resursgruppen. Mer information finns i använda resursgrupper för att hantera dina Azure-resurser.
- Ersätt `{vault-name}` med namnet på din nyckelvalv i URI: N.
- Ange Content-Type-huvud till application/json.
- Ange auktoriseringsrubriken till en JSON Web Token som du har fått från Azure Active Directory (AAD). Mer information finns i [autentisering av Azure Resource Manager](authentication-requests-and-responses.md) begäranden.

## <a name="common-error-response"></a>Vanliga felsvar
Tjänsten använder HTTP-statuskoder för att ange lyckad eller misslyckad. Dessutom kan innehålla fel svar i följande format:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elementnamn | Typ | Beskrivning |
|---|---|---|
| code | string | Typ av fel som inträffat.|
| message | string | En beskrivning av vad som orsakade felet. |



## <a name="see-also"></a>Se även
 [Azure Key Vault REST API-referens](/rest/api/keyvault/)
