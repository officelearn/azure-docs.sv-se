---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 1ec0281145609f9ae06ad07e1ad2cfd2b7f9aba8
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940820"
---
I den här snabb starten lär du dig att konvertera tal till text med hjälp av tal tjänsten och svängen.

En övergripande titt på tal till text-koncept finns i [översikts](../../../speech-to-text.md) artikeln.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Omvandla text till tal

Kör följande kommando i en kommando tolk. Du måste infoga följande värden i kommandot.
- Din prenumerations nyckel för röst tjänst.
- Din Speech service-region.
- Sökvägen till ljud filen för indata. Du kan generera ljudfiler med [text till tal](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Du bör få ett svar som följande.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Mer information finns [REST API referens för tal till text](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text).
