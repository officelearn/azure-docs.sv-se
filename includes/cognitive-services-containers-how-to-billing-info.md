---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: bfda8b83f1bedf11151ba89b58c95347aa35839a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052030"
---
Frågor till behållaren debiteras enligt prisnivån för Azure-resursen som används för den `<ApiKey>`.

Azure Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till fakturering slutpunkten för Avläsning av programvara. Du måste aktivera behållarna för att kommunicera faktureringsinformation med fakturering slutpunkten hela tiden. Cognitive Services-behållare Skicka inte kundinformation, till exempel bild eller text som analyseras, till Microsoft. 

### <a name="connect-to-azure"></a>Anslut till Azure

Behållaren måste fakturering argumentvärden ska köras. Dessa värden kan behållaren att ansluta till slutpunkten för fakturering. Behållaren rapporterar användning ungefär var 10 – 15 minuter. Om behållaren inte går att ansluta till Azure inom den tillåtna tidsperioden behållaren fortsätter att köras men leverera inte frågor tills fakturering slutpunkten har återställts. Anslutningen görs 10 gånger på samma tidsintervallet 10 – 15 minuter. Om den inte kan ansluta till den fakturering slutpunkten inom 10 försök kan behållaren stoppas. 

### <a name="billing-arguments"></a>Fakturering argument

För den `docker run` kommando för att starta behållaren måste alla tre av följande alternativ måste anges med giltiga värden:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckel för Cognitive Services-resurs som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade resursen som anges i `Billing`. |
| `Billing` | Slutpunkten för Cognitive Services-resurs som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en etablerade Azure-resurs-URI-slutpunkten.|
| `Eula` | Anger att du godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till **acceptera**. |


