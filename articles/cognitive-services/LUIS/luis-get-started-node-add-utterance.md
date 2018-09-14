---
title: 'Snabbstart: Ändra modell och träna LUIS-app med Node.js – Azure Cognitive Services | Microsoft Docs'
description: I den här Node.js-snabbstarten lägger du till exempelyttranden till en app för hemautomatisering och tränar appen. Exempelyttranden är konversationstext från användare som mappas till en avsikt. Genom att tillhandahålla exempelyttranden för avsikter lär du LUIS vilka typer av text från användare som tillhör vilken avsikt.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: fbd8b467fa3894d9cf58e1c8cb78ee00ebd0965e
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44156861"
---
# <a name="quickstart-change-model-using-nodejs"></a>Snabbstart: Ändra modell med hjälp av Node.js

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* Senaste [**Node.js**](https://nodejs.org/en/download/) med NPM.
* NPM-beroenden för den här artikeln: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

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

```CMD
> npm start 
```

Den här kommandoraden visar resultatet av anrop av API för att lägga till yttranden. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med snabbstarten tar du bort alla filer som skapas i den här snabbstarten. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en LUIS-app programmässigt](luis-tutorial-node-import-utterances-csv.md)