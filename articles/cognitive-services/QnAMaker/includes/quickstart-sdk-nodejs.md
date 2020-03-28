---
title: 'Snabbstart: QnA Maker-klientbibliotek för Node.js'
description: Den här snabbstarten visar hur du kommer igång med QnA Maker-klientbiblioteket för Node.js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021009"
---
Använd QnA Maker-klientbiblioteket för Node.js för att:

* Skapa en kunskapsbas
* Uppdatera en kunskapsbas
* Publicera en kunskapsbas
* Få publicerad slutpunktsnyckel
* Vänta på tidskrävande uppgift
* Ta bort kunskapsbas

[Referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Ursprungskodpaket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Nod.js-exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node.js](https://nodejs.org).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator.

När du har hämtat nyckeln och slutpunkten från din resurs hämtar du värdena från Azure-portalen för din nya resurs på snabbstartssidan.

[Skapa miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication), namngivna `QNAMAKER_AUTHORING_KEY` och `QNAMAKER_ENDPOINT`. Du kan kopiera [.env.sample-filen](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) till `.env` och använda miljövariablerna i filen.

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Kör `npm init -y` kommandot för att skapa ett `package.json` nodprogram med en fil.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera de nödvändiga och valfria NPM-paketen:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

Appens fil `package.json` uppdateras med beroenden. Det `dotenv` är valfritt och används för att du ska kunna ange miljövariabler i en textfil. Kontrollera inte `.env` in i källkontrollen.


## <a name="object-model"></a>Objektmodell

QnA Maker-klienten är ett [QnAMakerClient-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) som autentiserar till Azure med ServiceClientCredentials, som innehåller din nyckel.

När klienten har skapats använder du egenskapen [Knowledge base](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) skapa, hantera och publicera din kunskapsbas.

Hantera din kunskapsbas genom att skicka ett JSON-objekt. För omedelbara åtgärder returnerar en metod vanligtvis ett JSON-objekt som anger status. För tidskrävande åtgärder är svaret operations-ID. Ring [klienten. Metoden Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) med åtgärds-ID för att fastställa [status för begäran](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med QnA Maker-klientbiblioteket för Node.js:

* [Skapa en kunskapsbas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Få publicerad slutpunkt](#get-published-endpoint)
* [Hämta status för en åtgärd](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Skapa en fil som heter `index.js`. Lägg till QnA Maker-biblioteket och beroendena i filen.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Skapa variabler för resursens Azure-slutpunkt och -nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

|Miljövariabel|Nod.js variabel|Exempel|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|Nyckeln är en 32 teckensträng och är tillgänglig i Azure-portalen, på QnA Maker-resursen, på snabbstartssidan. Detta är inte samma sak som förutsägelseslutpunktsnyckeln.|
|`QNAMAKER_ENDPOINT`|`endpoint`| Din redigeringsslutpunkt, i `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formatet , innehåller ditt **resursnamn**. Det här är inte samma URL som används för att fråga ut förutsägelseslutpunkten.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa sedan ett ApiKeyCredentials-objekt med nyckeln och använd det med slutpunkten för att skapa ett [QnAMakerClient-objekt.](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) Använd klientobjektet för att hämta ett [kunskapsbasklientobjekt.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest)


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

I en kunskapsbas lagras fråge- och svarspar för [CreateKbDTO-objektet](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) från tre källor:

* För **redaktionellt innehåll**använder du [QnADTO-objektet.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest)
* Använd [FileDTO-objektet](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) för **filer.**
* För **webbadresser**använder du en lista med strängar.

Anropa [metoden skapa](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) med kunskapsbasinformationen. Kunskapsbasinformationen är i grunden ett JSON-objekt.

När metoden skapa returnerar skickar du det returnerade åtgärds-ID:et till [wait_for_operation](#get-status-of-an-operation) metoden för att avsöka status. Metoden wait_for_operation returneras när åtgärden är klar. Tolka `resourceLocation` huvudvärdet för den returnerade åtgärden för att hämta det nya kunskapsbas-ID:et.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Se till att [`wait_for_operation`](#get-status-of-an-operation) funktionen inkluderas, som refereras i koden ovan, för att kunna skapa en kunskapsbas.

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskapsbas genom att skicka in knowledge base-ID och ett [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) som innehåller [lägg till,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add) [uppdatera](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)och [ta bort](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO-objekt till [uppdateringsmetoden.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) DSOs är också i grunden JSON objekt. Använd [metoden wait_for_operation](#get-status-of-an-operation) för att avgöra om uppdateringen lyckades.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Se till att [`wait_for_operation`](#get-status-of-an-operation) funktionen inkluderas, som refereras i ovanstående kod, för att uppdatera en kunskapsbas.

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskapsbasen [publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) med publiceringsmetoden. Detta tar den aktuella sparade och tränade modellen, refererad av kunskapsbas-ID, och publicerar den vid en slutpunkt. Kontrollera http-svarskoden för att validera publiceringen lyckades.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Få publicerad slutpunkt

När kunskapsbasen har publicerats får du tillgång till den publicerade kunskapsbasen via frågeprognoskörningens generateAnswer API. För att göra detta behöver du körningens slutpunktsnyckel. Detta är annorlunda än redigeringsnyckeln.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Två slutpunktsnycklar returneras från samtalet. Endast en är nödvändig för att komma åt slutpunkten för körning.

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort kunskapsbasen med [borttagningsmetoden](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) med en parameter i kunskapsbas-ID: et.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel skapa och uppdatera, kan ta tillräckligt med tid att en [åtgärd](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) returneras i stället för att vänta på att processen ska slutföras. Använd [åtgärds-ID:t](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) från åtgärden för att avsöka (med logik för återförsök) för att fastställa status för den ursprungliga metoden.

_DelayTimer-anropet_ i följande kodblock används för att simulera logiken för återförsök. Ersätt detta med din egen logik för återförsök.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Köra appen

Kör programmet `node index.js` med kommando från programkatalogen.

Alla kodavsnitt i den här artikeln är [tillgängliga](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) och kan köras som en enda fil.

```console
node index.js
```

Statusen skrivs ut till konsolen:

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

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)