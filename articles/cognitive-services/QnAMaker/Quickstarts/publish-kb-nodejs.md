---
title: 'Snabbstart: QnA Maker med REST API:er för Node.js'
description: Den här snabbstarten visar hur du kommer igång med QnA Maker REST API:er för Node.js. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: ecc3fb144fb4b4e27182567925199f841b1c4357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851678"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Snabbstart: QnA Maker REST API:er för nod.js

Kom igång med QnA Maker REST API:er för Node.js. Följ dessa steg för att prova exempelkoden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.

Använd QnA Maker REST-API:er för nod.js för att:

* Skapa en kunskapsbas
* Byt ut en kunskapsbas
* Publicera en kunskapsbas
* Ta bort en kunskapsbas
* Ladda ner en kunskapsbas
* Hämta status för en åtgärd

[Exempel på referensdokumentation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node.js](https://nodejs.org).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta nyckeln och slutpunkten (som innehåller resursnamnet) väljer du **Snabbstart** för din resurs i Azure-portalen.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator.

När du har hämtat en nyckel från resursen `QNAMAKER_RESOURCE_KEY` `QNAMAKER_AUTHORING_ENDPOINT`skapar [du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för resursen med namnet och . Använd nyckel- och slutpunktsvärdena som finns på resursens **snabbstartssida** i Azure-portalen.

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den.

```console
mkdir myapp && cd myapp
```

Kör `npm init -y` kommandot för att `package.json` skapa en nodfil.

```console
npm init -y
```

Lägg `reqeuestretry` till `request` NPM-paketen och NPM:arna:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med QnA Maker REST API:er för Node.js:

* [Skapa en kunskapsbas](#create-a-knowledge-base)
* [Byt ut en kunskapsbas](#replace-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Ladda ner en kunskapsbas](#download-the-knowledge-base)
* [Hämta status för en åtgärd](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Skapa en `rest-apis.js` fil med namnet och lägg till följande _kräver_ uttalande för att göra HTTP-begäranden.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Lägga till Azure-resursinformation

Skapa variabler för resursens Azure-slutpunkt och -nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

Ange följande miljövärden:

* `QNAMAKER_RESOURCE_KEY`- **Nyckeln** är en 32 teckensträng och är tillgänglig i Azure-portalen, på QnA Maker-resursen, på **snabbstartssidan.** Detta är inte samma sak som förutsägelseslutpunktsnyckeln.
* `QNAMAKER_AUTHORING_ENDPOINT`- Din redigering slutpunkt, i `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formatet , innehåller ditt **resursnamn**. Det här är inte samma URL som används för att fråga ut förutsägelseslutpunkten.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskapsbas lagrar fråge- och svarspar som skapats från ett JSON-objekt med:

* **Redaktionellt innehåll**.
* **Filer** - lokala filer som inte kräver några behörigheter.
* **Webbadresser** – offentligt tillgängliga webbadresser.

Använd [REST API för att skapa en kunskapsbas](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Byt ut en kunskapsbas

Använd [REST API för att ersätta en kunskapsbas](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskapsbasen. Den här processen gör kunskapsbasen tillgänglig från en HTTP-frågeprognosslutpunkt.

Använd [REST API för att publicera en kunskapsbas](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Ladda ner kunskapsbasen

Använd [REST API för att hämta en kunskapsbas](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

När du är klar med kunskapsbasen tar du bort den.

Använd [REST API för att ta bort en kunskapsbas](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Tidskrävande processer som skapandeprocessen returnerar ett åtgärds-ID, som måste kontrolleras med ett separat REST API-anrop. Den här funktionen tar brödtexten i skapa-svaret. Den viktiga nyckeln `operationState`är , som avgör om du behöver fortsätta avsökningen.

Använd [REST API för att övervaka åtgärder på en kunskapsbas](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Köra appen

Kör programmet `node rest-apis.js` med kommando från programkatalogen.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Självstudiekurs: Skapa och svara på en KB](../tutorials/create-publish-query-in-portal.md)

* [Vad är QnA Maker API?](../Overview/overview.md)
* [Redigera en kunskapsbas](../how-to/edit-knowledge-base.md)
* [Hämta användningsanalys](../how-to/get-analytics-knowledge-base.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).