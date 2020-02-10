---
title: 'Snabb start: publicera kunskaps bas, REST, python-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Den här python REST-baserade snabb starten publicerar din kunskaps bas och skapar en slut punkt som kan anropas i ditt program eller chatt-robot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 6a9751f5771fe9dd5c33a3a01f2819664d893d53
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109454"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Snabbstart: Publicera en kunskapsbas i QnA Maker med hjälp av Python

Den här REST-baserade snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publiceringen skickar push-överför den senaste versionen av kunskaps basen till ett dedikerat Azure Kognitiv sökning-index och skapar en slut punkt som kan anropas i ditt program eller chatt-robot.

Den här snabb starten anropar QnA Maker REST API: er:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Publicera) – detta API kräver inte någon information i brödtexten för begäran.

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.7](https://www.python.org/downloads/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta din nyckel och slut punkt (som innehåller resurs namnet) väljer du **snabb start** för resursen i Azure Portal.
* QnA Maker kunskaps bas (KB) ID hittades i URL: en i URL: en för frågesträngen `kbid` som visas nedan.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Om du inte har en kunskapsbas ännu kan du kan skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas](../how-to/create-knowledge-base.md).

> [!NOTE]
> Kompletta lösningsfiler är tillgängliga från [**Azure-Samples/cognitive-services-qnamaker-python** GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Skapa en Python-fil för kunskapsbas

Skapa en fil som heter `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Högst upp i `publish-kb-3x.py` lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Lägg till nödvändiga konstanter

När du har lagt till nödvändiga beroenden lägger du till de konstanter som krävs för åtkomst till QnA Maker. Ersätt värdena med dina egna.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Lägga till POST-begäran för att publicera kunskapsbas

Efter de konstanter som krävs lägger du till följande kod, som gör en HTTPS-begäran för API för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext. Koden lägger till innehåll för 204-svar.

För andra svar returneras svaret oförändrat.

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Ange följande kommando på en kommandorad för att köra programmet. Den skickar förfrågan till API:et för QnA Maker för att publicera kunskapsbasen och skriva ut 204 för genomfört eller fel.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

När kunskapsbasen publiceras behöver du [slutpunktens webbadress för att generera ett svar](./get-answer-from-knowledge-base-python.md).

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)

[Översikt över QnA Maker](../Overview/overview.md)
