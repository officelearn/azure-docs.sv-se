---
title: Vanliga parametrar och sidhuvuden
description: De parametrar och sidhuvud som är gemensamma för alla åtgärder som du kan göra relaterade till Key Vault resurser.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430882"
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

|Elementnamn | Typ | Beskrivning |
|---|---|---|
| kod | sträng | Den typ av fel som inträffat.|
| meddelande | sträng | En beskrivning av vad som orsakat felet. |



## <a name="see-also"></a>Se även
 [Azure Key Vault REST API referens](/rest/api/keyvault/)
