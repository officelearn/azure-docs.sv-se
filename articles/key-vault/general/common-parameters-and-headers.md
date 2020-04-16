---
title: Vanliga parametrar och sidhuvuden
description: De parametrar och rubriker som är gemensamma för alla åtgärder som du kan göra relaterade till Key Vault-resurser.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430882"
---
# <a name="common-parameters-and-headers"></a>Vanliga parametrar och sidhuvuden

Följande information är gemensam för alla åtgärder som du kan göra relaterade till Key Vault-resurser:

- Ersätt `{api-version}` med api-versionen i URI.
- Ersätt `{subscription-id}` med din prenumerationsidentifierare i URI
- Ersätt `{resource-group-name}` med resursgruppen. Mer information finns i Använda resursgrupper för att hantera dina Azure-resurser.
- Ersätt `{vault-name}` med nyckelvalvets namn i URI.Replace with your key vault name in the URI.
- Ange innehållstypsrubriken till program/json.
- Ange auktoriseringshuvudet till en JSON-webbtoken som du hämtar från Azure Active Directory (AAD). Mer information finns i [Autentisera Azure Resource](authentication-requests-and-responses.md) Manager-begäranden.

## <a name="common-error-response"></a>Vanligt felsvar
Tjänsten använder HTTP-statuskoder för att indikera lyckades eller fel. Dessutom innehåller fel ett svar i följande format:

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
| kod | sträng | Den typ av fel som uppstod.|
| meddelande | sträng | En beskrivning av vad som orsakade felet. |



## <a name="see-also"></a>Se även
 [REST-referens för Azure Key Vault](/rest/api/keyvault/)
