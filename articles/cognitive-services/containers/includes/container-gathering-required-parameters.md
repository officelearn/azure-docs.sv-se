---
title: Samla in nödvändiga parametrar
services: cognitive-services
author: aahill
manager: nitinme
description: Parametrarna för alla Cognitive Services behållare
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875065"
---
## <a name="gathering-required-parameters"></a>Samla in nödvändiga parametrar

Det finns tre primära parametrar för alla Cognitive Services behållare som krävs. Licensavtalet för slutanvändare (EULA) måste finnas med `accept`värdet . Dessutom behövs både en slutpunkts-URL och API-nyckel.

### <a name="endpoint-uri-endpoint_uri"></a>Slutpunkt URI`{ENDPOINT_URI}`

**URI-värdet endpoint** är tillgängligt på sidan Översikt *över* Azure-portalen för motsvarande Cognitive Service-resurs. Navigera till sidan *Översikt,* hovra över `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> slutpunkten och en ikon visas. Kopiera och använd där det behövs.

![Samla slutpunktsuri för senare användning](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Nycklar`{API_KEY}`

Den här nyckeln används för att starta behållaren och är tillgänglig på Azure-portalens keys-sida för motsvarande Cognitive Service-resurs. Navigera till sidan *Nycklar* och `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> klicka på ikonen.

![Skaffa en av de två nycklarna för senare användning](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Dessa prenumerationsnycklar används för att komma åt ditt Cognitive Service API. Dela inte dina nycklar. Lagra dem säkert, till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar dessa nycklar regelbundet. Endast en nyckel är nödvändig för att ringa ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.