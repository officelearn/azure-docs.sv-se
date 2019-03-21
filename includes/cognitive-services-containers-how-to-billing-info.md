---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2019
ms.openlocfilehash: 200e2dfd2dd4f9aedd9256b307491a0b207ea124
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964293"
---
Frågor till behållaren debiteras enligt prisnivån för Azure-resursen används för den `<ApiKey>`.

Cognitive Services-behållare är inte rätt för att använda inte är ansluten till fakturering slutpunkten för Avläsning av programvara. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med fakturering slutpunkt hela tiden. Cognitive Services-behållare Skicka inte kunddata (t.ex, bild eller text som analyseras) till Microsoft. 

### <a name="connecting-to-azure"></a>Ansluta till Azure

Behållaren måste fakturering argumentvärden ska köras. Dessa värden kan behållaren att ansluta till slutpunkten för fakturering. Behållaren rapporterar användning ungefär var 10 – 15 minuter. Om behållaren inte går att ansluta inom den tillåtna tidsperioden till Azure behållaren fortsätter att köras, men kommer inte några frågor tills fakturering slutpunkten har återställts. Anslutningen görs 10 gånger på samma tidsintervallet 10 – 15 minuter. Om den inte kan ansluta till fakturering slutpunkten inom 10 försök stoppas behållaren. 

### <a name="billing-arguments"></a>Fakturering argument

Alla tre av följande alternativ måste anges med giltiga värden för den `docker run` kommando för att starta behållaren:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckel för den Cognitive Service-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade resursen som anges i `Billing`. |
| `Billing` | Slutpunkten för den Cognitive Service-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till slutpunkten för en etablerad LUIS Azure-resurs-URI.|
| `Eula` | Anger att du har godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |


