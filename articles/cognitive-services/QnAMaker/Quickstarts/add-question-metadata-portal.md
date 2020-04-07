---
title: 'Snabbstart: Lägg till frågor och svar i QnA Maker-portalen'
description: Den här snabbstarten visar hur du lägger till fråge- och svarsuppsättningar med metadata så att användarna kan hitta rätt svar på sin fråga.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: f067bae55c38fc783c12bf9d0bc6fbcdf881e4e4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756679"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Snabbstart: Lägg till frågor och svar med QnA Maker-portalen

När en kunskapsbas har skapats lägger du till fråge- och svarsuppsättningar (QnA) med metadata för att filtrera svaret. Frågorna i följande tabell handlar om Azure-tjänstgränser, men var och en har att göra med en annan Azure-tjänst.

<a name="qna-table"></a>

|Ange|Frågor|Svar|Metadata|
|--|--|--|--|
|Nr 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|Nr 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

När metadata har lagts till i ett QnA-par kan klientprogrammet:

* Begär svar som bara matchar vissa metadata.
* Få alla svar utom efter-process svaren beroende på metadata för varje svar.


## <a name="prerequisites"></a>Krav

* Slutför [föregående snabbstart](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Logga in på QnA Maker-portalen

1. Logga in på [QnA Maker-portalen](https://www.qnamaker.ai).

1. Välj din befintliga kunskapsbas från [föregående snabbstart](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Lägg till ytterligare alternativfrasfrågor

Den aktuella kunskapsbasen har QnA Maker felsökning QnA par. Dessa uppsättningar skapades när URL:en lades till i kunskapsbasen under skapandeprocessen.

När den här WEBBADRESSEN importerades skapades bara en fråga med ett svar. I den här proceduren lägger du till ytterligare frågor.

1. På sidan **Redigera** använder du söktextrutan ovanför fråge- och svarsuppsättningarna för att hitta frågan`How large a knowledge base can I create?`

1. I kolumnen **Fråga** väljer du **+ Lägg till alternativ frasering** och lägger sedan till varje ny frasering, som finns i följande tabell.

    |Alternativ frasering|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Välj **Spara och träna** om att träna kunskapsbasen igen.

1. Välj **Testa**och ange sedan en fråga som ligger nära en av de nya alternativa fraserna men som inte är exakt samma formulering:

    `What GB size can a knowledge base be?`

    Det korrekta svaret returneras i markdown-format:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Om du väljer **Granska** under det returnerade svaret kan du se fler svar träffade frågan men inte med samma höga konfidensnivå.

    Lägg inte till alla möjliga kombinationer av alternativ frasering. När du aktiverar QnA Maker [aktiva inlärning](../how-to/improve-knowledge-base.md)hittar du de alternativa fraser som bäst hjälper din kunskapsbas att uppfylla användarnas behov.

1. Välj **Testa** igen om du vill stänga testfönstret.

## <a name="add-metadata-to-filter-the-answers"></a>Lägga till metadata för att filtrera svaren

Genom att lägga till metadata i en fråge- och svarsuppsättning kan klientprogrammet begära filtrerade svar. Det här filtret används innan den [första och andra rankers](../concepts/query-knowledge-base.md#ranker-process) tillämpas.

1. Lägg till den andra fråge- och svarsuppsättningen, utan metadata, från den [första tabellen i den här snabbstarten](#qna-table)och fortsätt sedan med följande steg.

1. Välj **Visa alternativ**och välj sedan Visa **metadata**.

1. För det QnA-par som du just har lagt `service` till väljer `search`du Lägg **till metadatataggar**och lägger sedan till namnet på och värdet på . Det ser ut `service:search`så här: .

1. Lägg till ytterligare en `link_in_answer` metadatatagg `false`med namn och värde på . Det ser ut `link_in_answer:false`så här: .

1. Sök efter det första svaret `How large a knowledge base can I create?`i tabellen.

1. Lägg till metadatapar för samma två metadatataggar:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Du har nu två frågor med samma metadatataggar med olika värden.

1. Välj **Spara och träna** om att träna kunskapsbasen igen.

1. Välj **Publicera** i den övre menyn för att gå till publiceringssidan.
1. Välj knappen **Publicera** om du vill publicera den aktuella kunskapsbasen till slutpunkten.
1. När kunskapsbasen har publicerats fortsätter du till nästa snabbstart för att lära dig hur du skapar ett svar från din kunskapsbas.

## <a name="what-did-you-accomplish"></a>Vad åstadkom du?

Du har redigerat kunskapsbasen för att stödja fler frågor och ange namn-/värdepar för att stödja filtrering under sökningen efter det översta svaret eller efterbearbetningen, efter att svaret eller svaren returnerats.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte fortsätter till nästa snabbstart tar du bort ramresurserna QnA Maker och Bot i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Få svar med Postman eller cURL](get-answer-from-knowledge-base-using-url-tool.md)