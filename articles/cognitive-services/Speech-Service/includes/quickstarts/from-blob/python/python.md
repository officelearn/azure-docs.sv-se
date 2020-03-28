---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 646cce25efcbebab6229389f63912346e3712cdd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78926062"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)
> * [Skapa en Azure Speech-resurs](../../../../get-started.md)
> * [Ladda upp en källfil till en Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Hämta och installera API-klientbiblioteket

För att köra exemplet måste du generera Python-biblioteket för REST API som genereras via [Swagger](https://swagger.io).

Följ dessa steg för installationen:

1. Gå till https://editor.swagger.io.
1. Klicka på **Arkiv**och sedan på **Importera URL.**
1. Ange Swagger-URL:en, inklusive regionen `https://<your-region>.cris.ai/docs/v2.0/swagger`för din taltjänstprenumeration: .
1. Klicka på **Generera klient** och välj **Python**.
1. Spara klientbiblioteket.
1. Extrahera den nedladdade python-klient-generated.zip någonstans i filsystemet.
1. Installera den extraherade python-klientmodulen i `pip install path/to/package/python-client`Python-miljön med pip: .
1. Det installerade paketet `swagger_client`har namnet . Du kan kontrollera att installationen `python -c "import swagger_client"`fungerade med kommandot .

> [!NOTE]
> På grund av ett [känt fel i automatisk swagger-generering](https://github.com/swagger-api/swagger-codegen/issues/7541) `swagger_client` kan det uppstå fel när du importerar paketet.
> Dessa kan åtgärdas genom att ta bort raden med innehållet
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> från filen `swagger_client/models/model.py` och linjen med innehållet
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> från filen `swagger_client/models/inner_error.py` i det installerade paketet. Felmeddelandet talar om var dessa filer finns för installationen.

## <a name="install-other-dependencies"></a>Installera andra beroenden

I exemplet `requests` används biblioteket. Du kan installera den med kommandot

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Skapa och konfigurera en Http-klient
Det första vi behöver är en Http-klient som har en korrekt bas-URL och autentiseringsuppsättning.
Infoga den `transcribe` här koden i[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Generera en begäran om transkription
Därefter genererar vi begäran om transkription. Lägg till `transcribe` den här koden i[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Skicka begäran och kontrollera dess status
Nu lägger vi upp begäran till taltjänsten och kontrollerar den ursprungliga svarskoden. Den här svarskoden anger helt enkelt om tjänsten har tagit emot begäran. Tjänsten returnerar en url i svarsrubrikerna som är den plats där den lagrar transkriptionsstatusen.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Vänta tills transkriptionen är klar
Eftersom tjänsten bearbetar transkriptionen asynkront, måste vi enkät för sin status varje så ofta. Vi kollar var femte sekund.

Vi räknar upp alla transkriptioner som den här taltjänstresursen bearbetar och letar efter den vi skapade.

Här är avsökningskoden med statusvisning för allt utom ett framgångsrikt slutförande, vi gör det nästa.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Visa transkriptionsresultaten
När tjänsten har slutfört transkriptionen kommer resultaten att lagras i en annan webbadress som vi kan få från statussvaret.

Här får vi det resultatet JSON och visa det.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Kontrollera din kod
Nu bör din kod se ut så här: (Vi har lagt till några kommentarer till den här versionen)[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår taligenkänning med taltjänsten.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
