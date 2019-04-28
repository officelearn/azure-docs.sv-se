---
title: 'Snabbstart: Taligenkänning, Node.js – Speech Services'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden till att skapa ett tal till text-konsolprogram med hjälp av Speech SDK för Node.js. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: 5fa0b6d2a361fdbf372ba73fed9d8e2c48cb3630
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123543"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Snabbstart: Taligenkänning med Speech SDK för Node.js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln beskrivs hur du skapar ett Node.js-projekt med hjälp av JavaScript-bindningen i Speech SDK för Azure Cognitive Services för att transkribera tal till text.
Programmet är baserat på [Speech SDK för JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumerationsnyckel för tjänsten Speech. [Skaffa en kostnadsfritt](get-started.md).
* En aktuell version av [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

Skapa en ny mapp och initiera projektet:

```sh
npm init -f
```

Med det här kommandot initierar du filen **package.json** med standardvärden. Du vill förmodligen redigera den här filen senare.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Lägg till Speech SDK i Node.js-projektet:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Med det här kommandot laddar du ned och installerar den senaste versionen av Speech SDK och eventuella nödvändiga förutsättningar från **npmjs**. SDK:t installeras i katalogen `node_modules` i projektmappen.

## <a name="use-the-speech-sdk"></a>Använda Speech SDK

Skapa en ny fil i mappen med namnet `index.js` och öppna filen i en textredigerare.

> [!NOTE]
> I Node.js har inte Speech SDK stöd för mikrofonen eller **fildatatypen**. Båda stöds endast på webbläsare. Använd i stället **Stream**-gränssnittet till Speech SDK, antingen via `AudioInputStream.createPushStream()` eller `AudioInputStream.createPullStream()`.

I det här exemplet använder vi gränssnittet `PushAudioInputStream`.

Lägg till följande JavaScript-kod:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Kör exemplet

Om du vill öppna appen ändrar du `YourSubscriptionKey`, `YourServiceRegion` och `YourAudioFile.wav` till din konfiguration. Kör den sedan med följande kommando:

```sh
node index.js
```

Då utlöses ett erkännande med hjälp av det angivna filnamnet. Utdata visas på konsolen.

Här visas utdata när du kör `index.js` efter att du har uppdaterat prenumerationsnyckeln och använder filen `whatstheweatherlike.wav`:

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

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installera och använda Speech SDK med Visual Studio Code

Du kan också köra exemplet från Visual Studio Code. Följ stegen nedan för att installera, öppna och köra snabbstarten:

1. Starta Visual Studio Code. Välj **Öppna mapp**. Bläddra sedan till snabbstartsmappen.

   ![Öppna mappen](media/sdk/qs-js-node-01-open_project.png)

1. Öppna en terminal i Visual Studio Code.

   ![Terminalfönstret](media/sdk/qs-js-node-02_open_terminal.png)

1. Kör `npm` för att installera beroenden.

   ![Installera npm](media/sdk/qs-js-node-03-npm_install.png)

1. Nu är du redo att öppna `index.js` och konfigurera en brytpunkt.

   ![index.js med en brytpunkt på rad 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Om du vill börja felsöka trycker du antingen på F5 eller så väljer du **Felsöka/Starta felsökning** från menyn.

   ![Felsökningsmenyn](media/sdk/qs-js-node-05-start_debugging.png)

1. När du kommer till en brytpunkt kan du inspektera anropsstacken och variablerna.

   ![Felsökare](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Alla utdata visas i konsolfönstret för felsökningen.

   ![Felsökningskonsolen](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Node.js-exempel på GitHub](https://aka.ms/csspeech/samples)
