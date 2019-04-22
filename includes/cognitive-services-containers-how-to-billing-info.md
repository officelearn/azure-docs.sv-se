---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684658"
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
| `Billing` | Slutpunkten för den Cognitive Service-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en etablerade Azure-resurs-URI-slutpunkten.|
| `Eula` | Anger att du har godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |


