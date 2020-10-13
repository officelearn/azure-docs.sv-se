---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940759"
---
I den här snabb starten får du lära dig hur du konverterar text till tal med hjälp av tal tjänsten och svängen.

En övergripande titt på text till tal-begrepp finns i [översikts](../../../text-to-speech.md) artikeln.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Omvandla text till tal

Kör följande kommando i en kommando tolk. Du måste infoga följande värden i kommandot.
- Din prenumerations nyckel för röst tjänst.
- Din Speech service-region.

Du kanske också vill ändra följande värden.
- `X-Microsoft-OutputFormat`Huvudvärdet, som styr formatet för ljud uppspelning. Du hittar en lista över vilka ljud format som stöds i [text till tal-REST API referens](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs).
- Rösten utdata. Information om hur du hämtar en lista över röster som är tillgängliga för din tal slut punkt finns i nästa avsnitt.
- Utdatafilen. I det här exemplet dirigerar vi svaret från servern till en fil med namnet `output.wav` .

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Lista tillgängliga röster för din tal slut punkt

Kör följande kommando för att visa en lista över tillgängliga röster för din tal slut punkt.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Du bör få ett svar som följande.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
