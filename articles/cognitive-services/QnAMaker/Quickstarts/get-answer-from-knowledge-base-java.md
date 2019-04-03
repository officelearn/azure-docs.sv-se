---
title: 'Snabbstart: Få svar från kunskapsbas – REST, Java – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Denna Java REST-baserade snabbstart vägleder dig genom att hämta ett svar från en kunskapsbas programmässigt.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: a8e51534fec3dacb577ef9b79bb42c48557dc15b
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884949"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Få svar på en fråga med hjälp av en kunskapsbas med Java

Den här snabbstarten vägleder dig genom att programmatiskt hämta ett svar från en publicerad QnA Maker-kunskapsbas. Kunskapsbasen innehåller frågor och svar från [datakällor](../Concepts/data-sources-supported.md) , till exempel vanliga frågor och svar. Den [fråga](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) skickas till QnA Maker-tjänsten. Den [svar](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) innehåller top-förutse svaret. 

## <a name="prerequisites"></a>Förutsättningar

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* I det här exemplet används Apache [HTTP-klienten](http://hc.apache.org/httpcomponents-client-ga/) från HTTP Components. Du behöver lägga till följande Apache HTTP-klientbibliotek i projektet: 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio-koden](https://code.visualstudio.com/)
* Du måste ha en [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). Hämta nyckeln genom att välja **Nycklar** under **Resurshantering** på Azure-instrumentpanelen för din QnA Maker-resurs. 
* **Publicera** sidinställningar. Om du inte har en publicerad kunskapsbas skapar du en tom kunskapsbas, importerar en kunskapsbas på sidan **Inställningar** och publicerar sedan. Du kan ladda ned och använda [den här grundläggande kunskapsbasen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    Inställningarna för att publicera sida omfattar värden för POST route (POST-väg), Host (Värd) samt EndpointKey (Slutpunktsnyckel). 

    ![Publiceringinställningar](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Koden för den här snabbstarten finns i lagringsplatsen [https://github.com/Azure-Samples/cognitive-services-qnamaker-java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-java-file"></a>Skapa en Java-fil

Öppna VSCode, skapa en ny fil med namnet `GetAnswer.java` och lägg till följande klass:

```Java
public class GetAnswer {

    public static void main(String[] args) 
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

Den här snabbstarten använder Apache klasser för HTTP-begäranden. Ovanför klassen GetAnswer, längst upp i filen `GetAnswer.java`, lägger du till nödvändiga beroenden i projektet:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Lägga till nödvändiga konstanter

Längst upp i funktionen `GetAnswer.java` lägger du till de nödvändiga konstanterna för att få åtkomst till QnA Maker. De här värdena finns på sidan **Publicera** när du har publicerat kunskapsbasen.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Lägg till en POST-begäran för att skicka fråga

Följande kod gör en HTTPS-begäran till API för QnA Maker för att skicka frågan till kunskapsbasen och tar emot svaret:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

Värdet för `Authorization`-huvudet innehåller strängen `EndpointKey`. 

Läs mer om den [begäran](../how-to/metadata-generateanswer-usage.md#generateanswer-request) och [svar](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Skapa och köra programmet

Skapa och kör programmet från kommandoraden. Det skickar automatiskt begäran till API:et för QnA Maker, och sedan skrivs svaret ut till konsolfönstret.

1. Skapa filen:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Kör filen:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
