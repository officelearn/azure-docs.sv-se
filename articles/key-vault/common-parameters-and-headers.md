---
title: Vanliga parametrar och sidhuvuden
description: De parametrar och sidhuvud som är gemensamma för alla åtgärder som du kan göra relaterade till Key Vault resurser.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ec8e1df71e6513b13e9c37174a3363471be01d9
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879280"
---
# <a name="common-parameters-and-headers"></a>Vanliga parametrar och sidhuvuden

Följande information är gemensam för alla åtgärder som du kan göra relaterade till Key Vault resurser:

- Ersätt `{api-version}` med API-versionen i URI: n.
- Ersätt `{subscription-id}` med prenumerations-ID: t i URI: n
- Ersätt `{resource-group-name}` med resurs gruppen. Mer information finns i använda resurs grupper för att hantera dina Azure-resurser.
- Ersätt `{vault-name}` med nyckel valvets namn i URI: n.
- Ange rubrik för innehålls typ till Application/JSON.
- Ange Authorization-huvudet till en JSON Web Token som du får från Azure Active Directory (AAD). Mer information finns i [autentisera Azure Resource Manager](authentication-requests-and-responses.md) begär Anden.

## <a name="common-error-response"></a>Vanliga fel svar
Tjänsten kommer att använda HTTP-statuskod för att indikera att det lyckades eller misslyckades. Dessutom innehåller felen ett svar i följande format:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elementnamn | type | Beskrivning |
|---|---|---|
| code | sträng | Den typ av fel som inträffat.|
| message | sträng | En beskrivning av vad som orsakat felet. |



## <a name="see-also"></a>Se även
 [Azure Key Vault REST API referens](/rest/api/keyvault/)
