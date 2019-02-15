---
title: Vanliga parametrar och sidhuvuden
description: Parametrar och rubriker som är gemensamma för alla åtgärder som du kan göra rör Key Vault-resurser.
services: key-vault
documentationcenter: ''
author: bryanla
manager: barbkess
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: 1ac0f54aa4dfdc9db4724629c4dbfe7a4982838f
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301238"
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

|Elementnamn | Type | Beskrivning |
|---|---|---|
| Kod | sträng | Typ av fel som inträffat.|
| meddelande | sträng | En beskrivning av vad som orsakade felet. |



## <a name="see-also"></a>Se även
 [Azure Key Vault REST API-referens](/rest/api/keyvault/)
