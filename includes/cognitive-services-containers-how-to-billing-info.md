---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: 05961f8dd2788179a15e6bfe094484cf4770067b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124364"
---
Frågor till behållaren debiteras enligt prisnivån för Azure-resursen används för den `<ApiKey>`.

Azure Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till fakturering slutpunkten för Avläsning av programvara. Du måste aktivera behållarna för att kommunicera faktureringsinformation med fakturering slutpunkten hela tiden. Cognitive Services-behållare Skicka inte kundinformation, till exempel bild eller text som analyseras, till Microsoft. 

### <a name="connect-to-azure"></a>Anslut till Azure

Behållaren måste fakturering argumentvärden ska köras. Dessa värden kan behållaren att ansluta till slutpunkten för fakturering. Behållaren rapporterar användning ungefär var 10 – 15 minuter. Om behållaren inte går att ansluta till Azure inom den tillåtna tidsperioden behållaren fortsätter att köras men leverera inte frågor tills fakturering slutpunkten har återställts. Anslutningen görs 10 gånger på samma tidsintervallet 10 – 15 minuter. Om den inte kan ansluta till den fakturering slutpunkten inom 10 försök kan behållaren stoppas. 

### <a name="billing-arguments"></a>Fakturering argument

Alla tre av följande alternativ måste anges med giltiga värden för den `docker run` kommando för att starta behållaren.

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckel för Cognitive Services-resurs som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade resursen som anges i `Billing`. |
| `Billing` | Slutpunkten för Cognitive Services-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en etablerade Azure-resurs-URI-slutpunkten.|
| `Eula` | Anger att du godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |


