---
title: Nödvändiga parametrar samlas in
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametrar för alla Cognitive Services behållare
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "73465941"
---
## <a name="gathering-required-parameters"></a>Nödvändiga parametrar samlas in

Det finns tre primära parametrar för alla Cognitive Services behållare som krävs. Licens avtalet för slutanvändare (EULA) måste förekomma med värdet `accept`. Dessutom behövs både en slut punkts-URL och API-nyckel.

### <a name="endpoint-uri-endpoint_uri"></a>Slut punkts-URI `{ENDPOINT_URI}`

URI-värdet för **slut punkten** är tillgängligt på sidan Azure Portal *Översikt* för motsvarande kognitiva tjänst resurs. Gå till sidan *Översikt* , Hovra över slut punkten och en `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikon visas. Kopiera och använd vid behov.

![Samla in slut punkts-URI för senare användning](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Nycklar `{API_KEY}`

Den här nyckeln används för att starta behållaren och är tillgänglig på sidan Azure Portals nycklar för motsvarande kognitiva tjänst resurser. Gå till sidan *nycklar* och klicka på ikonen `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Hämta en av de två nycklarna för senare användning](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Dessa prenumerations nycklar används för att få åtkomst till ditt kognitiva tjänst-API. Dela inte dina nycklar. Lagra dem på ett säkert sätt, till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar nycklarna regelbundet. Endast en nyckel krävs för att göra ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.