---
title: 'Snabb start: Lägg till frågor och svar i QnA Maker Portal'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: bf88928ca24a1205ec7a1ddd2fd20af0d0e91468
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422664"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Snabb start: Lägg till frågor och svar med QnA Maker Portal

När en kunskaps bas har skapats lägger du till frågor och svars uppsättningar med metadata så att användarna kan hitta rätt svar på sina frågor.

Rätt svar är ett enda svar, men det kan finnas många sätt som en kund kan ställa frågan som leder till det enskilda svaret.

Frågorna i följande tabell är till exempel om begränsningar för Azure-tjänster, men var och en måste göra med en annan Azure-tjänst. 

<a name="qna-table"></a>


|Ange|Frågor|Svar|Metadata|
|--|--|--|--|
|Nr 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|Nr 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

När metadata har lagts till i en fråga-och-svars uppsättning kan klient programmet:

* Begär svar som bara matchar vissa metadata.
* Ta emot alla svar men efter bearbetningen av svaren beroende på metadata för varje svar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

* En QnA Maker tjänst
* En kunskaps bas som skapats i QnA Maker tjänsten

Båda har skapats i den [första snabb](../how-to/create-knowledge-base.md)starten.

## <a name="sign-in-to-the-qna-maker-portal"></a>Logga in på QnA Maker Portal

1. Logga in på [QNA Maker Portal](https://www.qnamaker.ai).

1. Välj din befintliga kunskaps bas. Om du inte har någon kunskaps bas går du tillbaka till [föregående snabb start](../how-to/create-knowledge-base.md) och slutför stegen för att skapa din kunskaps bas.

## <a name="add-additional-alternatively-phrased-questions"></a>Lägg till ytterligare, frasbaserade frågor 

Den aktuella kunskaps basen från den [tidigare snabb](../how-to/create-knowledge-base.md)starten har QNA Maker fel söknings fråga och svars uppsättningar. De här uppsättningarna skapades när URL: en lades till i kunskaps basen under skapande processen. 

När den här webb adressen har importer ATS skapades bara en fråga med ett svar. 

I den här proceduren lägger du till ytterligare frågor.

1. På sidan **Redigera** använder du sökrutan ovanför frågan och svars uppsättningarna för att hitta frågan `How large a knowledge base can I create?`

1. I kolumnen **fråga** väljer du **+ Lägg till alternativ formuleringen** och lägger sedan till varje ny formuleringen, som finns i följande tabell.

    |Alternativ formuleringen|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. Välj **Spara och träna** för att träna om kunskaps basen. 

1. Välj **test**och ange sedan en fråga som är nära en av de nya alternativa ordföljder, men inte exakt samma formulering:

    `What GB size can a knowledge base be?`

    Rätt svar returneras i markdown-format: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Om du väljer **Granska** under det returnerade svaret kan du se fler svar som uppfyllde frågan men inte med samma höga förtroende nivå. 

    Lägg inte till varje möjlig kombination av alternativa formuleringen. Aktivera QnA Maker s [aktiva utbildning](../how-to/improve-knowledge-base.md)hittar det alternativa ordföljder som bäst hjälper din kunskaps bas att uppfylla användarnas behov.

1. Välj **testa** igen för att stänga test fönstret.

## <a name="add-metadata-to-filter-the-answers"></a>Lägg till metadata för att filtrera Svaren

Genom att lägga till metadata till en fråga och en svars uppsättning kan klient programmet begära filtrerade svar. Filtret används innan den [första och andra rang](../concepts/knowledge-base.md#ranker-process) listan används.

1. Lägg till den andra frågan och svars uppsättningen, utan metadata, från den [första tabellen i den här snabb](#qna-table)starten och fortsätt sedan med följande steg. 

1. Välj **visnings alternativ**och välj sedan **Visa metadata**. 

1. För frågan och svars uppsättningen som du nyss lade till väljer du **Lägg till metadata-Taggar**och lägger till namnet på `service` och värdet för `search``service:search`.

1. Lägg till ytterligare metadata-Taggar med namnet på `link_in_answer` och värdet för `false``link_in_answer:false`.

1. Sök efter det första svaret i tabellen `How large a knowledge base can I create?`. 
1. Lägg till metadata-par för samma två metadata-Taggar:

    `link_in_answer`: `true`<br>
    `server`: `qna_maker`

    Nu har du två frågor med samma metadata-Taggar med olika värden. 

1. Välj **Spara och träna** för att träna om kunskaps basen. 

1. Välj **publicera** på den översta menyn för att gå till sidan publicera. 
1. Välj knappen **publicera** för att publicera den aktuella kunskaps basen till en slut punkt för frågor. 
1. När kunskaps basen har publicerats väljer du fliken **vändning** för att se ett exempel på ett spiral kommando som används för att generera ett svar från kunskaps basen.
1. Kopiera kommandot till en antecknings knapp eller annan redigerings bar miljö så att du kan redigera kommandot. Redigera för ditt eget resurs namn, kunskaps bas-ID och slut punkts nyckel:

    |Ersätt|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Observera att frågan bara är ett enda ord, `size`, som kan returnera antingen frågor och svars uppsättningar. `strictFilters` matrisen visar svaret för att minska till bara `qna_maker` svar. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. Svaret innehåller bara det svar som uppfyller filter kriterierna. 

    Följande spiral svar har formaterats för läsbarhet:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Om det finns en fråga och en svars uppsättning som inte uppfyllde Sök termen men som uppfyller filtret, returneras den inte. I stället returneras den allmänna svars `No good match found in KB.`.

    Se till att du behåller metadata-namn och värdepar inom de gränser som krävs. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Få svar med Postman eller sväng](get-answer-from-knowledge-base-using-url-tool.md)