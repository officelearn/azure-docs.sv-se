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
ms.openlocfilehash: 631185c20b816191530158fab2b7cd1ed68c3092
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371806"
---
Använd klientbiblioteket För att skapa språk understanding (LUIS) för Python för att:

* Skapa en app.
* Lägg till avsikter, entiteter och exempelyttranden.
* Lägg till funktioner, till exempel en fraslista.
* Träna och publicera en app.

[Referensdokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Redigeringspaket (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Krav

* Portalkonto (Language Understanding) (LUIS) : [Skapa ett gratis](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Inrätta

### <a name="get-your-language-understanding-luis-starter-key"></a>Hämta startnyckeln för språkförståelse (LUIS)

Hämta [startnyckeln](../luis-how-to-azure-subscription.md#starter-key) genom att skapa en LUIS-redigeringsresurs. Behåll nyckeln och regionen för nyckeln för nästa steg.

### <a name="create-an-environment-variable"></a>Skapa en miljövariabel

Med hjälp av nyckeln och regionen för nyckeln skapar du två miljövariabler för autentisering:

* `LUIS_AUTHORING_KEY`- Resursnyckeln för att autentisera dina begäranden.
* `LUIS_REGION`- Regionen som är kopplad till din nyckel. Till exempel `westus`.

Följ instruktionerna för operativsystemet.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

När du har lagt till miljövariabeln startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[Macos](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.
***

### <a name="install-the-python-library-for-luis"></a>Installera Python-biblioteket för LUIS

Installera klientbiblioteket (Language Understanding) för att skapa python i programkatalogen:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektmodell

LUIS-redigeringsklienten (Language Understanding) är ett [LUISAuthoringClient-objekt](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) som autentiserar till Azure, som innehåller din redigeringsnyckel.

När klienten har skapats använder du den här klienten för att komma åt funktioner, inklusive:

* Appar - [skapa,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) [ta bort](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publicera](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Exempel yttranden - [lägga till efter batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), ta bort av [ID](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Funktioner - hantera [fraslistor](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Modell - hantera [avsikter](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) och entiteter
* Mönster - hantera [mönster](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Tåg - [träna](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) appen och enkät för [träningsstatus](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versioner](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - hantera med klon, export och ta bort


## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med klientbiblioteket För redigering av språk understanding (LUIS) för python:

* [Skapa en app](#create-a-luis-app)
* [Lägg till entiteter](#create-entities-for-the-app)
* [Lägg till avsikter](#create-intent-for-the-app)
* [Lägga till exempelyttranden](#add-example-utterance-to-intent)
* [Träna appen](#train-the-app)
* [Publicera appen](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Skapa ett nytt pythonprogram

Skapa ett nytt Python-program i önskad redigerare eller IDE. Importera sedan följande bibliotek.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Skapa variabler för resursens Azure-slutpunkt och -nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa ett [CognitiveServicesCredentials-objekt](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) med nyckeln och använd det med slutpunkten för att skapa ett [LUISAuthoringClient-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Skapa en LUIS-app

1. Skapa en LUIS-app som innehåller NLP-modellen (Natural Language Processing) som innehåller avsikter, entiteter och exempelyttranden.

1. Skapa en Add-metod för Att [skapa](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) appen för ett [AppsOperation-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) Namn och språkkultur är obligatoriska egenskaper.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Skapa avsikt för appen
Det primära objektet i en LUIS-apps modell är avsikten. Avsikten justerar med en gruppering av avsikter för _användarens_yttrande . En användare kan ställa en fråga eller göra ett uttalande som letar efter ett särskilt _avsett_ svar från en bot (eller annat klientprogram). Exempel på avsikter är att boka ett flyg, fråga om vädret i en destinationsstad och fråga om kontaktuppgifter för kundservice.

Använd [metoden model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) med namnet på den unika avsikten och skicka sedan app-ID, versions-ID och nytt avsiktsnamn.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Skapa entiteter för appen

Entiteter krävs inte, men de finns i de flesta appar. Entiteten extraherar information från användarens yttrande, nödvändigt att fullfil användarens avsikt. Det finns flera typer av färdiga och anpassade [entiteter,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) var och en med sina egna DTO-modeller (Data Transformation Object).  Vanliga fördefinierade entiteter som ska läggas till i appen är [nummer,](../luis-reference-prebuilt-number.md) [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Den här **add_entities** metoden `Location` skapade en enkel entitet med två roller, en `Class` enkel entitet, en `Flight` sammansatt entitet och lägger till flera fördefinierade entiteter.

Det är viktigt att veta att entiteter inte är markerade med en avsikt. De kan och brukar gälla för många avsikter. Endast exempelanvändaryttranden är markerade för en specifik, enda avsikt.

Skapa metoder för entiteter är en del av klassen [ModelOperations.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) Varje entitetstyp har sin egen DTO-modell (Data Transformation Object).

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Lägga till exempelutseende till avsikt

För att fastställa en yttrande avsikt och extrahera entiteter, behöver appen exempel på yttranden. Exemplen måste inriktas på en specifik, enda avsikt och bör markera alla anpassade entiteter. Fördefinierade entiteter behöver inte markeras.

Lägg till exempelyttranden genom att skapa en lista med [ExempelLabelObject-objekt,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) ett objekt för varje exempelutseende. Varje exempel ska markera alla entiteter med en ordlista med namn-/värdepar med entitetsnamn och entitetsvärde. Entitetsvärdet ska vara exakt som det visas i texten i exempelutseendet.

Ring [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) med app-ID, versions-ID och listan med exempel. Samtalet svarar med en lista med resultat. Du måste kontrollera varje exempels resultat för att se till att det har lagts till i modellen.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>Träna appen

När modellen har skapats måste LUIS-appen tränas för den här versionen av modellen. En tränad modell kan användas i en [behållare](../luis-container-howto.md)eller [publiceras](../luis-how-to-publish-app.md) på mellanlagrings- eller produktplatserna.

Metoden [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) behöver app-ID och versions-ID.

En mycket liten modell, som denna snabbstart visar, kommer att träna mycket snabbt. För program på produktionsnivå bör appen innehålla ett avsökningsanrop till [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) metod för att avgöra när eller om utbildningen lyckades. Svaret är en lista över [ModelTrainingInfo-objekt](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) med en separat status för varje objekt. Alla objekt måste lyckas för att utbildningen ska anses vara fullständig.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Publicera en språk understanding-app

Publicera LUIS-appen med metoden [app.publish.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) Detta publicerar den aktuella tränade versionen till den angivna platsen vid slutpunkten. Klientprogrammet använder den här slutpunkten för att skicka användaryttranden för förutsägelse av avsikts- och entitetsextrahering.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `python` kommandot på snabbstartsfilen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med dina förutsägelser rensar du arbetet från den här snabbstarten genom att ta bort filen och dess underkataloger.