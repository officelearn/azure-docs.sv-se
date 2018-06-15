---
title: Gemensamma parametrar och rubriker
description: Parametrar och huvuden som är gemensamma för alla åtgärder som du kan göra rör Key Vault-resurser.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: ead1ac550c9b7c489edefd35d5672a9955e78255
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012194"
---
# <a name="common-parameters-and-headers"></a>Gemensamma parametrar och rubriker

Följande information är gemensamma för alla åtgärder som du kan göra rör Key Vault-resurser:

- Ersätt `{api-version}` med api-version i URI: N.
- Ersätt `{subscription-id}` med prenumerations-ID i URI: N
- Ersätt `{resource-group-name}` med resursgrupp. Mer information finns i använda resursgrupper för att hantera dina Azure-resurser.
- Ersätt `{vault-name}` med ditt nyckelvalv namn i URI: N.
- Ange Content-Type-huvudet till application/json.
- Ange en JSON Web Token som du har fått från Azure Active Directory (AAD) Authorization-huvud. Mer information finns i [autentisera Azure Resource Manager](authentication-requests-and-responses.md) begäranden.

## <a name="common-error-response"></a>Vanliga felsvar
Tjänsten använder HTTP-statuskoder för att ange lyckad eller misslyckad. Dessutom kan innehålla fel svar i följande format:

   {  
     ”error”: {  
     ”kod”: ”BadRequest”  
     ”meddelandet”: ”key vault-sku är ogiltig”.  
     }  
   }  

|Elementnamn | Typ | Beskrivning |
|---|---|---|
| Koden | sträng | Typ av fel som inträffade.|
| meddelande | sträng | En beskrivning av vad som orsakade felet. |



## <a name="see-also"></a>Se även
 [Azure Key Vault REST API-referens](/rest/api/keyvault/)
