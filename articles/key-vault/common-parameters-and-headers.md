---
title: Vanliga parametrar och sidhuvuden
description: Parametrar och rubriker som är gemensamma för alla åtgärder som du kan göra rör Key Vault-resurser.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: dae5e1ab6244d2898bc218ed5db3b6b2b90150cf
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294061"
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

   {  
     ”error”: {  
     ”code”: ”BadRequest”  
     ”message”: ”key vault-SKU: n är ogiltig”.  
     }  
   }  

|Elementnamn | Typ | Beskrivning |
|---|---|---|
| Kod | sträng | Typ av fel som inträffat.|
| meddelande | sträng | En beskrivning av vad som orsakade felet. |



## <a name="see-also"></a>Se även
 [Azure Key Vault REST API-referens](/rest/api/keyvault/)
