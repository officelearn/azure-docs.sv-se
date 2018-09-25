---
title: Azure ytterdörren Service – stöd för HTTP-huvuden protokoll | Microsoft Docs
description: Den här artikeln hjälper dig att förstå de HTTP-huvud protokoll som stöds av ytterdörren
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038858"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure ytterdörren Service – stöd för HTTP-huvuden protokoll
Det här dokumentet beskriver det protokoll som Azure ytterdörren tjänsten stöder med de olika delarna av anropet sökvägen från bilden nedan. I avsnitten nedan ger mer information om HTTP-huvuden som har stöd för åtkomsten.

![Azure ytterdörren Service HTTP-huvuden-protokoll][1]

>[!WARNING]
>Azure ytterdörren-tjänst ger inga garantier på alla HTTP-huvuden som inte finns dokumenterade här.

## <a name="1-client-to-front-door"></a>1. Klient till ytterdörren
Ytterdörren accepterar de flesta meddelandehuvuden från den inkommande begäranden (utan att ändra dem), men det finns vissa reserverade huvuden som tas bort från den inkommande begäranden om de skickas. Detta inkluderar rubriker med följande prefix:
 - X-FD *
 - X-MS *

## <a name="2-front-door-to-backend"></a>2. Åtkomsten till serverdelen

Ytterdörren innehåller rubrikerna från den inkommande begäran, såvida inte de har tagits bort på grund av begränsningar som nämns ovan. Ytterdörren kommer också att lägga till följande rubriker:

| Sidhuvud  | Exempel och beskrivning |
| ------------- | ------------- |
| X-MS-referens |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ ==* </br> Det här är en unik referens-sträng som identifierar en begäran som hanteras av ytterdörren. Det är viktigt för att felsöka som används för att söka i Åtkomstloggarna.|
| X-MS-RequestChain |  *X-MS-RequestChain: hopp = 1* </br> Detta är en rubrik som ytterdörren använder för att identifiera begäran loopar och användare som inte ska ett beroende på den. |
| X-MS-Via |  *X-MS-Via: Azure* </br> Detta läggs till av dörren att indikera att Azure/ytterdörren har en mellanliggande mottagare för förfrågan mellan klienten och serverdelen. |

## <a name="3-front-door-to-client"></a>3. Åtkomsten till klienten

Följande är de rubriker som skickas från åtkomsten till klienter. Rubriker som skickas till ytterdörren från serverdelen skickas till klienten även.

| Sidhuvud  | Exempel |
| ------------- | ------------- |
| X-MS-referens |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ ==* </br> Det här är en unik referens-sträng som identifierar en begäran som hanteras av ytterdörren. Det är viktigt för att felsöka som används för att söka i Åtkomstloggarna.|

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en ytterdörren](quickstart-create-front-door.md).
- Lär dig [hur ytterdörren fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png