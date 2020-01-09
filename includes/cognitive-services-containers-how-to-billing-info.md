---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704250"
---
Frågor till behållaren debiteras enligt pris nivån för den Azure-resurs som används för `<ApiKey>`.

Azure Cognitive Services-behållare är inte licensierade för att köras utan att vara ansluten till fakturerings slut punkten för mätning. Du måste göra det möjligt för behållarna att kommunicera fakturerings information med fakturerings slut punkten hela tiden. Cognitive Services behållare skickar inte kund information, till exempel den bild eller text som analyseras, till Microsoft. 

### <a name="connect-to-azure"></a>Anslut till Azure

Containern behöver de fakturerings argument värden som ska köras. Dessa värden tillåter att behållaren ansluter till fakturerings slut punkten. Behållar rapporteringen visar var 10 till 15: e minut. Om behållaren inte ansluter till Azure inom den tillåtna tids perioden fortsätter behållaren att köras men hanterar inte frågor förrän fakturerings slut punkten återställs. Anslutnings försöket görs 10 gånger med samma tidsintervall 10 till 15 minuter. Om det inte går att ansluta till fakturerings slut punkten inom 10 försök, slutar behållaren att köras. 

### <a name="billing-arguments"></a>Fakturerings argument

För att `docker run` kommandot ska starta behållaren måste alla tre av följande alternativ anges med giltiga värden:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckeln för den Cognitive Services resurs som används för att spåra fakturerings information.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade resurs som anges i `Billing`. |
| `Billing` | Slut punkten för den Cognitive Services resursen som används för att spåra fakturerings information.<br/>Värdet för det här alternativet måste anges till slut punkts-URI för en etablerad Azure-resurs.|
| `Eula` | Anger att du har accepterat licensen för behållaren.<br/>Värdet för det här alternativet måste vara inställt på **acceptera**. |


