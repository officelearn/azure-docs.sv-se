---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: edf08715290fc68b65f2d910440093be7df37af0
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425513"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Skapa en Azure tal-resurs](../../../../overview.md#try-the-speech-service-for-free)
> * [Ladda upp en källfil till en Azure-Blob](../../../../../../storage/blobs/storage-quickstart-blobs-portal.md)

## <a name="download-and-install-the-api-client-library"></a>Hämta och installera API-klient biblioteket

Om du vill köra det här exemplet måste du generera python-biblioteket för REST API som genereras via [Swagger](https://swagger.io).

Följ de här stegen för installationen:

1. Gå till https://editor.swagger.io.
1. Klicka på **Arkiv** och sedan på **Importera URL**.
1. Ange Swagger-URL: en, inklusive regionen för din röst tjänst prenumeration: `https://<your-region>.cris.ai/docs/v2.0/swagger` .
1. Klicka på **generera klient** och välj **python**.
1. Spara klient biblioteket.
1. Extrahera den nedladdade python-client-generated.zip någonstans i fil systemet.
1. Installera den extraherade python-client-modulen i python-miljön med hjälp av pip: `pip install path/to/package/python-client` .
1. Det installerade paketet har namnet `swagger_client` . Du kan kontrol lera att installationen fungerade med kommandot `python -c "import swagger_client"` .

> [!NOTE]
> På grund av ett [känt fel i Swagger autogenerning](https://github.com/swagger-api/swagger-codegen/issues/7541)kan du stöta på fel vid import av `swagger_client` paketet.
> Dessa kan åtgärdas genom att raden tas bort med innehållet
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> från filen `swagger_client/models/model.py` och raden med innehållet
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> från filen `swagger_client/models/inner_error.py` i det installerade paketet. Fel meddelandet visar var de här filerna finns för din installation.

## <a name="install-other-dependencies"></a>Installera andra beroenden

Exemplet använder `requests` biblioteket. Du kan installera det med kommandot

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Skapa och konfigurera en http-klient
Det första vi behöver är en http-klient som har rätt bas-URL och autentisering.
Infoga den här koden i `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Generera en avskrifts förfrågan
Nu ska vi skapa en avskrifts förfrågan. Lägg till den här koden i `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Skicka begäran och kontrol lera dess status
Nu ska vi publicera begäran till tal tjänsten och kontrol lera den ursprungliga svars koden. Den här svars koden anger bara om tjänsten har tagit emot begäran. Tjänsten returnerar en URL i svarshuvuden som är den plats där den kommer att lagra avskrifts status.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Vänta tills avskriften har slutförts
Eftersom tjänsten bearbetar avskriften asynkront måste vi avsöka efter status varje så ofta. Vi ska kontrol lera var femte sekund.

Vi räknar upp alla avskrifter som den här tal tjänst resursen bearbetar och letar efter den som vi skapade.

Här är avsöknings koden med status visning för allt, förutom att slutföra slut för ande, vi ska göra det härnäst.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Visa avskrifts resultatet
När tjänsten har slutfört avskriften kommer resultatet att lagras i en annan URL som vi kan få från status svaret.

Här får vi det resulterande JSON och visar det.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Kontrol lera koden
I det här läget bör din kod se ut så här: (vi har lagt till några kommentarer till den här versionen) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår tal igenkänning med röst tjänsten.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]