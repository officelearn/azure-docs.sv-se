---
title: Lägg till frågor och svar i QnA Maker Portal
description: Den här artikeln visar hur du lägger till fråge-och svars par med metadata så att användarna kan hitta rätt svar på sina frågor.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 53c0afa1cdb8c9920875b7ba694339107714bd54
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462201"
---
# <a name="add-questions-and-answer-with-qna-maker-portal"></a>Lägg till frågor och svar med QnA Maker Portal

När en kunskaps bas har skapats kan du lägga till fråge-och svars-par (QnA) med metadata för att filtrera svaret. Frågorna i följande tabell är om begränsningar för Azure-tjänster, men var och en måste göra med en annan Azure Search-tjänst.

<a name="qna-table"></a>

|Par|Frågor|Svar|Metadata|
|--|--|--|--|
|Nr 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|Nr 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

När metadata har lagts till i ett QnA-par kan klient programmet:

* Begär svar som bara matchar vissa metadata.
* Ta emot alla svar men efter bearbetningen av svaren beroende på metadata för varje svar.


## <a name="prerequisites"></a>Förutsättningar

* Slutför den [tidigare snabb](./create-publish-knowledge-base.md) starten

## <a name="sign-in-to-the-qna-maker-portal"></a>Logga in på QnA Maker Portal

1. Logga in på [QNA Maker Portal](https://www.qnamaker.ai).

1. Välj din befintliga kunskaps bas från [föregående snabb start](./create-publish-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Lägg till ytterligare, frasbaserade frågor

Den aktuella kunskaps basen har QnA Maker felsöka QnA-par. Dessa par skapades när URL: en lades till i kunskaps basen under skapande processen.

När den här webb adressen har importer ATS skapades bara en fråga med ett svar. I den här proceduren lägger du till ytterligare frågor.

1. På sidan **Redigera** använder du sökrutan ovanför fråge-och svars paren för att hitta frågan `How large a knowledge base can I create?`

1. I kolumnen **fråga** väljer du **+ Lägg till alternativ formuleringen** och lägger sedan till varje ny formuleringen, som finns i följande tabell.

    |Alternativ formuleringen|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Välj **Spara och träna** för att träna om kunskaps basen.

1. Välj **test** och ange sedan en fråga som är nära en av de nya alternativa ordföljder, men inte exakt samma formulering:

    `What GB size can a knowledge base be?`

    Rätt svar returneras i markdown-format:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`

    Om du väljer **Granska** under det returnerade svaret kan du se fler svar som uppfyllde frågan men inte med samma höga förtroende nivå.

    Lägg inte till varje möjlig kombination av alternativa formuleringen. När du aktiverar QnA Makerens [aktiva utbildning](../how-to/improve-knowledge-base.md)hittar du den alternativa ordföljder som bäst hjälper din kunskaps bas att uppfylla användarnas behov.

1. Välj **testa** igen för att stänga test fönstret.

## <a name="add-metadata-to-filter-the-answers"></a>Lägg till metadata för att filtrera Svaren

Genom att lägga till metadata till ett fråge-och svars par kan klient programmet begära filtrerade svar. Filtret används innan den [första och andra rang](../concepts/query-knowledge-base.md#ranker-process) listan används.

1. Lägg till den andra frågan och svars paret utan metadata från den [första tabellen i den här snabb](#qna-table)starten och fortsätt sedan med följande steg.

1. Välj **visnings alternativ** och välj sedan **Visa metadata**.

1. För det QnA-par som du nyss lade till väljer du **Lägg till metadata-Taggar** och lägger sedan till namnet på `service` och värdet för `search` . Det ser ut så här: `service:search` .

1. Lägg till en annan metadata-tagg med namnet `link_in_answer` och värdet `false` . Det ser ut så här: `link_in_answer:false` .

1. Sök efter det första svaret i tabellen, `How large a knowledge base can I create?` .

1. Lägg till metadata-par för samma två metadata-Taggar:

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    Nu har du två frågor med samma metadata-Taggar med olika värden.

1. Välj **Spara och träna** för att träna om kunskaps basen.

1. Välj **publicera** på den översta menyn för att gå till sidan publicera.
1. Välj knappen **publicera** för att publicera den aktuella kunskaps basen till slut punkten.
1. När kunskaps basen har publicerats fortsätter du till nästa snabb start för att lära dig hur du skapar ett svar från din kunskaps bas.

## <a name="what-did-you-accomplish"></a>Vad gjorde du?

Du har redigerat din kunskaps bas för att stödja fler frågor och tillhandahöll namn/värde-par för att stödja filtrering under sökningen efter den översta svars-eller postprocessing efter det att svar eller svar returnerades.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte fortsätter med nästa snabb start tar du bort QnA Maker-och bot Framework-resurserna i Azure Portal.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Få svar med Postman eller cURL](get-answer-from-knowledge-base-using-url-tool.md)
