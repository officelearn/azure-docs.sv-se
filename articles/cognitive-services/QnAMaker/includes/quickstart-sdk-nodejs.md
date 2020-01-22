---
title: 'Snabb start: QnA Maker klient bibliotek för Node. js'
description: Den här snabb starten visar hur du kommer igång med QnA Maker-klient biblioteket för Node. js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021009"
---
Använd QnA Maker klient bibliotek för Node. js för att:

* Skapa en kunskapsbas
* Uppdatera en kunskapsbas
* Publicera en kunskapsbas
* Hämta publicerad slut punkts nyckel
* Vänta på aktivitet som körs
* Ta bort kunskaps bas

[Referens dokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Library Source Code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node. js-exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node. js](https://nodejs.org).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn.

När du har hämtat nyckeln och slut punkten från resursen hämtar du värdena från Azure Portal för din nya resurs på snabb starts sidan.

[Skapa miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)med namnet `QNAMAKER_AUTHORING_KEY` och `QNAMAKER_ENDPOINT`. Du kan kopiera [. miljö. exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) filen för att `.env` och använda miljövariablerna i den filen.

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Kör kommandot `npm init -y` för att skapa ett Node-program med en `package.json`-fil.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera de obligatoriska och valfria NPM-paketen:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

Appens `package.json` fil uppdateras med beroenden. `dotenv` är valfritt och används för att ställa in miljövariabler i en textfil. Kontrol lera inte `.env` i käll kontrollen.


## <a name="object-model"></a>Objekt modell

QnA Maker-klienten är ett [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) -objekt som autentiserar till Azure med hjälp av ServiceClientCredentials, som innehåller din nyckel.

När klienten har skapats använder du [kunskaps bas](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) egenskapen skapa, hantera och publicera din kunskaps bas.

Hantera din kunskaps bas genom att skicka ett JSON-objekt. För omedelbara åtgärder returnerar en metod vanligt vis ett JSON-objekt som indikerar status. För långvariga åtgärder är svaret åtgärds-ID. Anropa [klienten. Operations. getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) -metoden med åtgärds-ID för att fastställa [status för begäran](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med QnA Maker klient biblioteket för Node. js:

* [Skapa en kunskapsbas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskaps bas](#delete-a-knowledge-base)
* [Hämta publicerad slut punkt](#get-published-endpoint)
* [Hämta status för en åtgärd](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Skapa en fil som heter `index.js`. Lägg till QnA Maker bibliotek och beroenden till filen.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Skapa variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln.

|Miljövariabel|Node. js-variabel|Exempel|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|Nyckeln är en 32 tecken sträng och är tillgänglig i Azure Portal på den QnA Maker resursen på snabb starts sidan. Detta är inte samma som för förutsägelse slut punkts nyckel.|
|`QNAMAKER_ENDPOINT`|`endpoint`| Din redigerings slut punkt, i formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, innehåller **resurs namnet**. Detta är inte samma URL som används för att fråga efter förutsägelse slut punkten.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa sedan ett ApiKeyCredentials-objekt med din nyckel och Använd den med slut punkten för att skapa ett [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) -objekt. Använd klient objekt för att hämta ett [kunskaps bas klient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) objekt.


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskaps bas lagrar fråge-och svars par för [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) -objektet från tre källor:

* För **redaktionellt innehåll**använder du [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) -objektet.
* För **filer**använder du [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) -objektet.
* För **URL: er**använder du en lista med strängar.

Anropa [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) -metoden med kunskaps bas informationen. Kunskaps bas informationen är i princip ett JSON-objekt.

När metoden Create returnerar skickar du det returnerade åtgärds-ID: t till [wait_for_operation](#get-status-of-an-operation) -metoden för att avsöka efter status. Metoden wait_for_operation returnerar när åtgärden har slutförts. Parsa värdet för den returnerade åtgärdens `resourceLocation` huvud för att hämta det nya kunskaps bas-ID: t.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Se till att inkludera funktionen [`wait_for_operation`](#get-status-of-an-operation) , som refereras till i ovanstående kod, för att kunna skapa en kunskaps bas.

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskaps bas genom att skicka i kunskaps bas-ID: t och en [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) som innehåller objekt för att [lägga till](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [Uppdatera](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)och [ta bort](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO till [uppdaterings](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) metoden. DTO är också i princip JSON-objekt. Använd metoden [wait_for_operation](#get-status-of-an-operation) för att avgöra om uppdateringen har slutförts.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Se till att inkludera funktionen [`wait_for_operation`](#get-status-of-an-operation) , som refereras till i ovanstående kod, för att kunna uppdatera en kunskaps bas.

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskaps basen med [publicerings](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) metoden. Detta tar den aktuella sparade och utbildade modellen som refereras till av kunskaps bas-ID: t och publicerar den på en slut punkt. Kontrol lera HTTP-svarskod för att verifiera att publiceringen har slutförts.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Hämta publicerad slut punkt

När kunskaps basen har publicerats kan du få åtkomst till den publicerade kunskaps basen via generateAnswer-API: et för Query förutsägelse körning. För att kunna göra detta behöver du körningens slut punkts nyckel. Detta skiljer sig från redigerings nyckeln.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Två slut punkts nycklar returneras från anropet. Endast en är nödvändig för att få åtkomst till runtime-slutpunkten.

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort kunskaps basen med metoden [Delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) med en parameter i kunskaps bas-ID: t.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel skapa och uppdatera, kan ta tillräckligt lång tid i stället för att vänta på att processen ska slutföras, returneras en [åtgärd](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) . Använd [Åtgärds-ID: t](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) från åtgärden att söka (med logiken för omprövning) för att fastställa statusen för den ursprungliga metoden.

_DelayTimer_ -anropet i följande kodblock används för att simulera omprövnings logiken. Ersätt detta med din egen omprövnings logik.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Köra programmet

Kör programmet med kommandot `node index.js` från program katalogen.

Alla kodfragment i den här artikeln är [tillgängliga](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) och kan köras som en enda fil.

```console
node index.js
```

Programmet skriver ut status till-konsolen:

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)