---
title: 'Snabb start: identifiera tal som lagras i BLOB C++ Storage,-tal service'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 54c6f920b581a0bbd00910a3b3ddeebecdbb595f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954933"
---
## <a name="prerequisites"></a>Krav

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Ladda upp en källfil till en Azure-Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

1. Starta Visual Studio 2019.
2. Läs in projektet och öppna `helloworld.cpp`.

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
(Du måste ersätta värdena för `YourSubscriptionKey`, `YourServiceRegion`och `YourFileUrl` med dina egna värden.)

## <a name="json-wrappers"></a>JSON-omslutningar

Som REST API utföra begär anden i JSON-format och även returnera resultat i JSON kan vi interagera med dem med enbart strängar, men det rekommenderas inte.
För att göra det lättare att hantera förfrågningar och svar kan vi deklarera några klasser som ska användas för serialisering/deserialisering av JSON och vissa metoder för att hjälpa nlohmann/JSON.

Gå vidare och placera deras deklarationer innan du `recognizeSpeech`.
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
