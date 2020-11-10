---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 09a4f36f2b4b0e90dc17489721cd52b0f70f17ba
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425385"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Skapa en Azure tal-resurs](../../../../overview.md#try-the-speech-service-for-free)
> * [Ladda upp en källfil till en Azure-Blob](../../../../../../storage/blobs/storage-quickstart-blobs-portal.md)

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

1. Starta Visual Studio 2019.
2. Läs in projektet och öppna `helloworld.cpp` .

## <a name="add-a-references"></a>Lägg till en referens

För att påskynda vår kod utveckling kommer vi att använda ett par externa komponenter:
* [Cpp rest SDK](https://github.com/microsoft/cpprestsdk) Ett klient bibliotek för att göra REST-anrop till en REST-tjänst.
* [nlohmann/JSON](https://github.com/nlohmann/json) Praktiska bibliotek för JSON-parsning/serialisering/serialisering/deserialisering.

Båda kan installeras med [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON-omslutningar

Som REST API utföra begär anden i JSON-format och även returnera resultat i JSON kan vi interagera med dem med enbart strängar, men det rekommenderas inte.
För att göra det lättare att hantera förfrågningar och svar kan vi deklarera några klasser som ska användas för serialisering/deserialisering av JSON och vissa metoder för att hjälpa nlohmann/JSON.

Gå vidare och placera deras deklarationer innan `recognizeSpeech` .
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Skapa och konfigurera en http-klient
Det första vi behöver är en http-klient som har rätt bas-URL och autentisering.
Infoga den här koden i `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Generera en avskrifts förfrågan
Nu ska vi skapa en avskrifts förfrågan. Lägg till den här koden i `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Skicka begäran och kontrol lera dess status
Nu ska vi publicera begäran till tal tjänsten och kontrol lera den ursprungliga svars koden. Den här svars koden anger bara om tjänsten har tagit emot begäran. Tjänsten returnerar en URL i svarshuvuden som är den plats där den kommer att lagra avskrifts status.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Vänta tills avskriften har slutförts
Eftersom tjänsten bearbetar avskriften asynkront måste vi avsöka efter status varje så ofta. Vi ska kontrol lera var femte sekund.

Vi kan kontrol lera statusen genom att hämta innehållet på URL: en som vi fick när du publicerade begäran. När vi får tillbaka innehållet deserialiserar vi det till någon av våra hjälp klasser för att göra det enklare att interagera med.

Här är avsöknings koden med status visning för allt, förutom att slutföra slut för ande, vi ska göra det härnäst.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Visa avskrifts resultatet
När tjänsten har slutfört avskriften kommer resultatet att lagras i en annan URL som vi kan få från status svaret.

Vi laddar ned innehållet i den URL: en, deserialiserar JSON och loopar igenom de resultat som skriver ut visnings texten som vi går.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Kontrol lera koden
I det här läget bör din kod se ut så här: (vi har lagt till några kommentarer till den här versionen)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår tal igenkänning med röst tjänsten.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]