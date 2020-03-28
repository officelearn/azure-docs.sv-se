---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 070de0f65e890c38acd5075286b349e95cd19f3b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371681"
---
Använd klientbiblioteket för språk understanding (LUIS) för nod.js för att:

* Skapa en app.
* Lägg till avsikter, entiteter och exempelyttranden.
* Lägg till funktioner, till exempel en fraslista.
* Träna och publicera en app.
* Ta bort app

[Referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [Författande paket (NPM),](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring) [NPM-exempel (Runtime Package)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Krav

* Språk understanding authoring resource: [Skapa en i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Inrätta

### <a name="get-your-language-understanding-luis-starter-key"></a>Hämta startnyckeln för språkförståelse (LUIS)

Hämta [startnyckeln](../luis-how-to-azure-subscription.md#starter-key) genom att skapa en LUIS-redigeringsresurs. Behåll nyckeln och slutpunkten för nyckeln för nästa steg.

### <a name="create-an-environment-variable"></a>Skapa en miljövariabel

Med hjälp av nyckeln och regionen för nyckeln skapar du två miljövariabler för autentisering:

* `LUIS_AUTHORING_KEY`- Resursnyckeln för att autentisera dina begäranden.
* `LUIS_AUTHORING_ENDPOINT`- Slutpunkten som är associerad med din nyckel.

Följ instruktionerna för operativsystemet.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

När du har lagt till miljövariabeln startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[Macos](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Installera NPM-biblioteket för LUIS-redigering

Installera beroendena med följande kommando i programkatalogen:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Objektmodell

LUIS-redigeringsklienten (Language Understanding) är ett [LUISAuthoringClient-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) som autentiserar till Azure, som innehåller din redigeringsnyckel.

När klienten har skapats använder du den här klienten för att komma åt funktioner, inklusive:

* Appar - [lägga till](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [ta bort](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publicera](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Exempel yttranden - [lägga till efter batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), ta bort av [ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Funktioner - hantera [fraslistor](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modell - hantera [avsikter](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) och entiteter
* Mönster - hantera [mönster](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Tåg - [träna](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) appen och enkät för [träningsstatus](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versioner](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - hantera med klon, export och ta bort


## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket För att skapa språk understanding (LUIS) för nod.js:

* [Skapa en app](#create-a-luis-app)
* [Lägg till entiteter](#create-entities-for-the-app)
* [Lägg till avsikter](#create-intent-for-the-app)
* [Lägga till exempelyttranden](#add-example-utterance-to-intent)
* [Träna appen](#train-the-app)
* [Publicera appen](#publish-a-language-understanding-app)
* [Ta bort appen](#delete-a-language-understanding-app)
* [Lista appar](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa en ny textfil i önskad `luis_authoring_quickstart.js`redigerare eller IDE med namnet . Lägg sedan till följande beroenden.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Skapa variabler för resursens Azure-slutpunkt och -nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa ett [CognitiveServicesCredentials-objekt](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) med nyckeln och använd det med slutpunkten för att skapa ett [LUISAuthoringClient-objekt.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

1. Skapa en LUIS-app som innehåller NLP-modellen (Natural Language Processing) som innehåller avsikter, entiteter och exempelyttranden.

1. Skapa en Add-metod för Att [skapa](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) appen för ett [AppsOperation-objekt.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) Namn och språkkultur är obligatoriska egenskaper.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Skapa avsikt för appen
Det primära objektet i en LUIS-apps modell är avsikten. Avsikten justerar med en gruppering av avsikter för _användarens_yttrande . En användare kan ställa en fråga eller göra ett uttalande som letar efter ett särskilt _avsett_ svar från en bot (eller annat klientprogram). Exempel på avsikter är att boka ett flyg, fråga om vädret i en destinationsstad och fråga om kontaktuppgifter för kundservice.

Använd [metoden model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) med namnet på den unika avsikten och skicka sedan app-ID, versions-ID och nytt avsiktsnamn.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Skapa entiteter för appen

Entiteter krävs inte, men de finns i de flesta appar. Entiteten extraherar information från användarens yttrande, nödvändigt att fullfil användarens avsikt. Det finns flera typer av färdiga och anpassade [entiteter,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) var och en med sina egna DTO-modeller (Data Transformation Object).  Vanliga fördefinierade entiteter som ska läggas till i appen är [nummer,](../luis-reference-prebuilt-number.md) [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Den här **add_entities** metoden `Location` skapade en enkel entitet med två roller, en `Class` enkel entitet, en `Flight` sammansatt entitet och lägger till flera fördefinierade entiteter.

Det är viktigt att veta att entiteter inte är markerade med en avsikt. De kan och brukar gälla för många avsikter. Endast exempelanvändaryttranden är markerade för en specifik, enda avsikt.

Skapa metoder för entiteter är en del av [klassen Modell.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) Varje entitetstyp har sin egen DTO-modell (Data Transformation Object).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Lägga till exempelutseende till avsikt

För att fastställa en yttrande avsikt och extrahera entiteter, behöver appen exempel på yttranden. Exemplen måste inriktas på en specifik, enda avsikt och bör markera alla anpassade entiteter. Fördefinierade entiteter behöver inte markeras.

Lägg till exempelyttranden genom att skapa en lista med [ExempelLabelObject-objekt,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) ett objekt för varje exempelutseende. Varje exempel ska markera alla entiteter med en ordlista med namn-/värdepar med entitetsnamn och entitetsvärde. Entitetsvärdet ska vara exakt som det visas i texten i exempelutseendet.

Ring [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) med app-ID, versions-ID och listan med exempel. Samtalet svarar med en lista med resultat. Du måste kontrollera varje exempels resultat för att se till att det har lagts till i modellen.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Träna appen

När modellen har skapats måste LUIS-appen tränas för den här versionen av modellen. En tränad modell kan användas i en [behållare](../luis-container-howto.md)eller [publiceras](../luis-how-to-publish-app.md) på mellanlagrings- eller produktplatserna.

[Train.trainVersion-metoden](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) behöver app-ID och versions-ID.

En mycket liten modell, som denna snabbstart visar, kommer att träna mycket snabbt. För program på produktionsnivå bör appen innehålla ett avsökningsanrop till [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) metod för att avgöra när eller om utbildningen lyckades. Svaret är en lista över [ModelTrainingInfo-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) med en separat status för varje objekt. Alla objekt måste lyckas för att utbildningen ska anses vara fullständig.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Utbildning av alla modeller tar tid. Använd åtgärdenResultat för att kontrollera träningsstatusen.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publicera en språk understanding-app

Publicera LUIS-appen med metoden [app.publish.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) Detta publicerar den aktuella tränade versionen till den angivna platsen vid slutpunkten. Klientprogrammet använder den här slutpunkten för att skicka användaryttranden för förutsägelse av avsikts- och entitetsextrahering.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Ta bort en språk understanding-app

Ta bort LUIS-appen med metoden [app.deleteMethod.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) Då tas den aktuella appen bort.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Lista språkförståelseappar

Få en lista över appar som är associerade med nyckeln För språkförståelse

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `node luis_authoring_quickstart.js` kommandot på snabbstartsfilen.

```console
node luis_authoring_quickstart.js
```

Programmets kommandoradsutdata är:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
