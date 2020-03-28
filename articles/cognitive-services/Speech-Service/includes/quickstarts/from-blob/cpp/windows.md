---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 20dfc49acdaa30bea6f0652640e007c16f08c572
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925997"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Skapa en Azure Speech-resurs](../../../../get-started.md)
> * [Ladda upp en källfil till en Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

1. Starta Visual Studio 2019.
2. Ladda projektet och `helloworld.cpp`öppna .

## <a name="add-a-references"></a>Lägga till en referens

För att snabba upp vår kodutveckling kommer vi att använda ett par externa komponenter:
* [CPP Vila SDK](https://github.com/microsoft/cpprestsdk) Ett klientbibliotek för att ringa REST-anrop till en REST-tjänst.
* [nlohmann/json](https://github.com/nlohmann/json) Handy JSON Parsing / Serialisering / Avserialisering bibliotek.

Båda kan installeras med [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON Omslag

Eftersom REST API: s ta förfrågningar i JSON-format och även returnera resultat i JSON vi kunde interagera med dem med bara strängar, men det rekommenderas inte.
För att göra förfrågningar och svar lättare att hantera, kommer vi att förklara några klasser att använda för serialisering / deserializing JSON och vissa metoder för att hjälpa nlohmann / json.

Gå vidare och lägga `recognizeSpeech` sina förklaringar före .
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Skapa och konfigurera en Http-klient
Det första vi behöver är en Http-klient som har en korrekt bas-URL och autentiseringsuppsättning.
Infoga den här koden i`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Generera en begäran om transkription
Därefter genererar vi begäran om transkription. Lägg till den här koden i`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Skicka begäran och kontrollera dess status
Nu lägger vi upp begäran till taltjänsten och kontrollerar den ursprungliga svarskoden. Den här svarskoden anger helt enkelt om tjänsten har tagit emot begäran. Tjänsten returnerar en url i svarsrubrikerna som är den plats där den lagrar transkriptionsstatusen.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Vänta tills transkriptionen är klar
Eftersom tjänsten bearbetar transkriptionen asynkront, måste vi enkät för sin status varje så ofta. Vi kollar var femte sekund.

Vi kan kontrollera status genom att hämta innehållet på webbadressen vi fick när den postat begäran. När vi får tillbaka innehållet deserialiserar vi det till en av våra hjälpklasser för att göra det lättare att interagera med.

Här är avsökningskoden med statusvisning för allt utom ett framgångsrikt slutförande, vi gör det nästa.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Visa transkriptionsresultaten
När tjänsten har slutfört transkriptionen kommer resultaten att lagras i en annan webbadress som vi kan få från statussvaret.

Vi hämtar innehållet i webbadressen, deserialiserar JSON och går igenom resultaten som skriver ut visningstexten när vi går.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Kontrollera din kod
Nu bör din kod se ut så här: (Vi har lagt till några kommentarer till den här versionen)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår taligenkänning med taltjänsten.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
