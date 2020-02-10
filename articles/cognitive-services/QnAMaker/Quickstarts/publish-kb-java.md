---
title: 'Snabb start: publicera kunskaps bas, REST, Java-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Den här Java REST-baserade snabb starten publicerar din kunskaps bas och skapar en slut punkt som kan anropas i ditt program eller chatt-robot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 48da92374a29b5319f0ae5eaa830f2e03511b512
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109479"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Snabbstart: Publicera en kunskapsbas i QnA Maker med hjälp av Java

Den här REST-baserade snabbstarten går igenom hur du programmatiskt publicerar din kunskapsbas (KB). Publiceringen skickar push-överför den senaste versionen av kunskaps basen till ett dedikerat Azure Kognitiv sökning-index och skapar en slut punkt som kan anropas i ditt program eller chatt-robot.

Den här snabbstarten anropar API:er för QnA Maker:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) (Publicera) – detta API kräver inte någon information i brödtexten för begäran.

## <a name="prerequisites"></a>Förutsättningar

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* I det här exemplet används Apache [HTTP-klienten](https://hc.apache.org/httpcomponents-client-ga/) från HTTP Components. Du behöver lägga till följande Apache HTTP-klientbibliotek i projektet:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Om du vill hämta din nyckel och slut punkt (som innehåller resurs namnet) väljer du **snabb start** för resursen i Azure Portal.
* QnA Maker kunskaps bas (KB) ID hittades i URL: en i URL: en för frågesträngen `kbid` som visas nedan.

    ![QnA Maker-kunskapsbas-ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Om du inte har en kunskapsbas ännu kan du kan skapa en exempelkunskapsbas för den här snabbstarten: [Skapa en ny kunskapsbas](create-new-kb-csharp.md).

> [!NOTE]
> Kompletta lösningsfiler finns på [**Azure-Samples/cognitive-services-qnamaker-java** GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Skapa en Java-fil

Öppna VSCode och skapa en ny fil med namnet `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Längst upp i `PublishKB.java`, ovanför klassen, lägger du till följande rader för att lägga till nödvändiga beroenden i projektet:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Skapa PublishKB-klass med main-metoden

Efter beroendena lägger du till följande klass:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Lägg till nödvändiga konstanter

I **main-metoden** lägger du sedan till de nödvändiga konstanterna för att få åtkomst till QnA Maker. Ersätt värdena med dina egna.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Lägga till POST-begäran för att publicera kunskapsbas

Efter de konstanter som krävs lägger du till följande kod, som gör en HTTPS-begäran för API för QnA Maker för att publicera en kunskapsbas och tar emot svaret:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

API-anropet returnerar 204-status för en lyckad publicering utan något innehåll i svarets brödtext. Koden lägger till innehåll för 204-svar.

För andra svar returneras svaret oförändrat.

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet från kommandoraden. Det skickar automatiskt begäran till API:et för QnA Maker, och sedan skrivs svaret ut till konsolfönstret.

1. Skapa filen:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Kör filen:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

När kunskapsbasen publiceras behöver du [slutpunktens webbadress för att generera ett svar](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
