---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2019
ms.openlocfilehash: b20ee2df1f468323c3298854371c97950c017e49
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57788938"
---
Frågor till behållaren debiteras enligt prisnivån för Azure-resursen används för den `<ApiKey>`.

Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft. Behållaren rapporterar användning ungefär var 10 – 15 minuter.

Den `docker run` använder följande argument för fakturering:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckel för den Cognitive Service-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade resursen som anges i `Billing`. |
| `Billing` | Slutpunkten för den Cognitive Service-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till slutpunkten för en etablerad LUIS Azure-resurs-URI.|
| `Eula` | Anger att du har godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |

> [!IMPORTANT]
> Alla tre alternativ måste anges med giltiga värden eller behållaren startar inte.
