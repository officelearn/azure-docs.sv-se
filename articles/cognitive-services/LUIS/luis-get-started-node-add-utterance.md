---
title: Ändra, träna appen, Node.js
titleSuffix: Azure Cognitive Services
description: I den här Node.js-snabbstarten lägger du till exempelyttranden till en app för hemautomatisering och tränar appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/17/2019
ms.author: diberry
ms.openlocfilehash: e2a8bed2b07934bf178ea338e074ec8e239ee646
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196394"
---
# <a name="quickstart-change-model-using-nodejs"></a>Snabbstart: Ändra modell med hjälp av Node.js

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Senaste [**Node.js**](https://nodejs.org/en/download/) med NPM.
* NPM-beroenden för den här artikeln: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Kod för att skapa snabbstart 

Lägg till NPM-beroendena i filen med namnet `add-utterances.js`.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

Lägg till LUIS-konstanterna i filen. Kopiera följande kod och ändra till din redigeringsnyckel, ditt program-ID och ditt versions-ID.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

Lägg till namnet och platsen för den uppladdningsfil som innehåller dina yttranden. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

Lägg till metod och objekt för funktionen `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

Lägg till metod och objekt för funktionen `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

Lägg till funktionen `sendUtteranceToApi` för att skicka och ta emot HTTP-anrop. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

Lägg till den **huvudkod** som väljer åtgärd.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>Installera beroenden

Skapa `package.json`-fil med följande text:

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

På kommandoraden går du till den katalog där package.json finns och installerar beroenden med NPM: `npm install`.

## <a name="run-code"></a>Köra koden

Kör programmet från en kommandorad med Node.js.

När du anropar `npm start` läggs yttrandena till, träning utförs och träningsstatus hämtas.

```console
> npm start 
```

Den här kommandoraden visar resultatet av anrop av API för att lägga till yttranden. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med snabbstarten tar du bort alla filer som skapas i den här snabbstarten. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en LUIS-app programmässigt](luis-tutorial-node-import-utterances-csv.md)
