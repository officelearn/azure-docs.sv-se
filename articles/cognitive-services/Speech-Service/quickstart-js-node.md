---
title: 'Snabbstart: Taligenkänning i JavaScript i Node.js med hjälp av Speech Service SDK'
titleSuffix: Azure Cognitive Services
description: Lär dig om taligenkänning i JavaScript i Node.js med hjälp av Speech Service SDK
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 35652b169067bc545fa0d1fcc977bbaee79ec3aa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724444"
---
# <a name="quickstart-recognize-speech-in-javascript-in-nodejs-using-the-speech-service-sdk"></a>Snabbstart: Taligenkänning i JavaScript i Node.js med hjälp av Speech Service SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig hur du skapar ett Node.js-projekt med hjälp av JavaScript-bindningen i Cognitive Services Speech SDK för att transkribera tal till text.
Appen baseras på Microsoft [Cognitive Services Speech SDK](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en utan kostnad](get-started.md).
* En aktuell version av [Node.js](https://nodejs.org).

## <a name="create-a-new-project-folder"></a>Skapa en ny projektmapp

Skapa en ny tom mapp och initiera den som ett nytt projekt för JavaScript och Node.js.

```sh
npm init -f
```

På så sätt kommer package.json-filer att initieras med standardvärden. Du vill förmodligen redigera den här filen senare.

## <a name="install-the-speech-sdk-for-javascript-into-that-folder"></a>Installera Speech SDK för JavaScript i mappen

Lägg till Speech SDK via `npm install microsoft-cognitiveservices-speech-sdk` till ditt Node.js-projekt.

Detta kommer att ladda ner och installera den senaste versionen av Speech SDK och eventuella nödvändiga förutsättningar från npmjs. SDK kommer att installeras i `node_modules`-katalogen i projektmappen.

## <a name="using-the-speech-sdk"></a>Använda Speech SDK

Skapa en ny fil i mappen, med namnet `index.js` och öppna filen med en textredigerare.

> [!NOTE]
> Observera att i Node.js har inte Speech SDK stöd för mikrofonen eller fildatatypen. Båda stöds endast på webbläsare. Använd i stället Stream-gränssnittet till Speech SDK, antingen via `AudioInputStream.createPushStream()` eller `AudioInputStream.createPullStream()`.

I det här exemplet ska vi använda `PushAudioInputStream`-gränssnittet.

Lägg till följande JavaScript-kod:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="running-the-sample-from-command-line"></a>Kör exemplet från kommandoraden

Om du vill starta appen, anpassa `YourSubscriptionKey`, `YourServiceRegion` och `YourAudioFile.wav` till din konfiguration. Sedan kan du köra den genom att anropa följande kommando:

```sh
node index.js
```

Det utlöser en igenkänning med hjälp av det angivna filnamnet och utdata på konsolen.

Här är ett exempel på utdata av att köra `index.js` när du uppdaterat prenumerationsnyckeln och använt filen `whatstheweatherlike.wav`.

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="running-the-sample-from-visual-studio-code"></a>Kör exemplet från Visual Studio Code

Du kan också köra exemplet från Visual Studio Code. Följ stegen nedan för att installera, öppna och köra snabbstarten:

1. Starta Visual Studio Code och klicka på ”Öppna mapp” och navigera sedan till snabbstartsmappen

   ![Skärmbild av Öppna mapp](media/sdk/qs-js-node-01-open_project.png)

1. Öppna en terminal i Visual Studio Code

   ![Skärmbild av terminalfönstret](media/sdk/qs-js-node-02_open_terminal.png)

1. Kör npm för att installera beroenden

   ![Skärmbild av npm-installering](media/sdk/qs-js-node-03-npm_install.png)

1. Nu är du redo att öppna `index.js`och konfigurera en brytpunkt

   ![Skärmbild av index.js med en brytpunkt på rad 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Om du vill starta felsökning, tryck antingen på F5 eller välj Felsöka/Starta felsökning från menyn

   ![Skärmbild av felsökningsmenyn](media/sdk/qs-js-node-05-start_debugging.png)

1. När en brytpunkt kommer, kan du inspektera anropsstacken och variablerna

   ![Skärmbild av felsökningsprogram](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Inga utdata visas i konsolfönstret för felsökning

   ![Skärmbild av felsökningskonsolen](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://aka.ms/csspeech/samples)
