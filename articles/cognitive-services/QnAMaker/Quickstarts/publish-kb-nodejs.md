---
title: 'Snabb start: QnA Maker med REST API: er för Node.js'
description: 'Den här snabb starten visar hur du kommer igång med QnA Maker REST-API: er för Node.js. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.'
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: 645f9ffb1d907eb576f5414b593ef57679d26daa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351154"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Snabb start: QnA Maker REST-API: er för Node.js

Kom igång med QnA Maker REST-API: er för Node.js. Följ de här stegen för att testa exempel koden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.

Använd QnA Maker REST-API: er för Node.js till:

* Skapa en kunskapsbas
* Ersätta en kunskaps bas
* Publicera en kunskapsbas
* Ta bort en kunskapsbas
* Hämta en kunskaps bas
* Hämta status för en åtgärd

[Referens dokumentation](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Node.js exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org).
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta din nyckel och slut punkt (som innehåller resurs namnet) väljer du **snabb start** för resursen i Azure Portal.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med hjälp av [Azure Portal](../../cognitive-services-apis-create-account.md) eller [Azure CLI](../../cognitive-services-apis-create-account-cli.md) på den lokala datorn.

När du har hämtat en nyckel från resursen [skapar du miljövariabler](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för resursen, med `QNAMAKER_RESOURCE_KEY` namnet `QNAMAKER_AUTHORING_ENDPOINT` och. Använd de nyckel-och slut punkts värden som finns på resursens **snabb start** sida i Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den.

```console
mkdir myapp && cd myapp
```

Kör `npm init -y` kommandot för att skapa en Node- `package.json` fil.

```console
npm init -y
```

Lägg till `reqeuestretry` och `request` NPM-paketen:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med QnA Maker REST-API: er för Node.js:

* [Skapa en kunskaps bas](#create-a-knowledge-base)
* [Ersätta en kunskaps bas](#replace-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Hämta en kunskaps bas](#download-the-knowledge-base)
* [Hämta status för en åtgärd](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Skapa en fil med namnet `rest-apis.js` och Lägg till följande beroenden.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>Lägg till verktygs funktioner

Lägg till följande verktyg funktioner.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>Lägg till information om Azure-resurser

Skapa variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln.

Ange följande miljö värden:

* `QNAMAKER_RESOURCE_KEY` – **Nyckeln** är en 32 tecken sträng och är tillgänglig i Azure Portal på den QNA Maker resursen på sidan **snabb start** . Detta är inte samma som för förutsägelse slut punkts nyckel.
* `QNAMAKER_AUTHORING_ENDPOINT` – Din redigerings slut punkt, i formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` , innehåller ditt **resurs namn**. Detta är inte samma URL som används för att fråga efter förutsägelse slut punkten.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskaps bas lagrar fråge-och svars par som skapats från ett JSON-objekt av:

* **Redaktionellt innehåll**.
* **Filer** – lokala filer som inte kräver några behörigheter.
* **URL** : er – offentligt tillgängliga URL: er.

Använd [REST API för att skapa en kunskaps bas](/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>Ersätta en kunskaps bas

Använd [REST API för att ersätta en kunskaps bas](/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskaps basen. Den här processen gör kunskaps basen tillgänglig från en HTTP-fråga förutsägelse slut punkt.

Använd [REST API för att publicera en kunskaps bas](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>Hämta kunskaps basen

Använd [REST API för att ladda ned en kunskaps bas](/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

När du är färdig med kunskaps basen tar du bort den.

Använd [REST API för att ta bort en kunskaps bas](/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Tids krävande processer, till exempel skapande processen, returnerar ett åtgärds-ID som måste kontrol leras med ett separat REST API-anrop. Den här funktionen tar bröd texten i Create-svaret. Den viktiga nyckeln är `operationState` , som avgör om du behöver fortsätta avsökningen.

Använd [REST API för att övervaka åtgärder i en kunskaps bas](/rest/api/cognitiveservices/qnamaker/operations/getdetails).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>Lägg till main-metod

Lägg till följande `main` metod.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node rest-apis.js` kommandot från program katalogen.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Självstudie: skapa och svara på en KB](./create-publish-knowledge-base.md)

* [Vad är API för QnA Maker?](../Overview/overview.md)
* [Redigera en kunskapsbas](../how-to/edit-knowledge-base.md)
* [Hämta användnings analys](../how-to/get-analytics-knowledge-base.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).