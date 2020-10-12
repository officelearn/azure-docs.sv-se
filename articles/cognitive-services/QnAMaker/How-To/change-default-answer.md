---
title: Hämta standard svar – QnA Maker
description: Standard svaret returneras när det inte finns någon matchning till frågan. Du kanske vill ändra standard svaret från standard-standardsvaret.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: 14954f89fcdcbbc1ef4b8654582a3274f4bb0923
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776824"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Ändra standard svar för en QnA Maker resurs

Standard svaret för en kunskaps bas är tänkt att returneras när det inte går att hitta något svar. Om du använder ett klient program, till exempel [Azure bot service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), kan det också ha ett separat standard svar, vilket indikerar att inget svar uppfyllde Poäng tröskelvärdet.

## <a name="types-of-default-answer"></a>Typer av standard svar

Det finns två typer av standard svar i din kunskaps bas. Det är viktigt att förstå hur och när varje returneras från en förutsägelse fråga:


|Typ av fråga|Beskrivning av svar|
|--|--|
|KB-svar när inget svar har fastställts|`No good match found in KB.` – När [GenerateAnswer-API: n](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) inte hittar något matchande svar på frågan, `DefaultAnswer` returneras inställningen för App Service. Alla kunskaps baser i samma QnA Maker resurs delar samma standard svars text.<br>Du kan hantera inställningen i Azure Portal, via App Service eller med REST-API: er för att [Hämta](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) eller [Uppdatera](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) inställningen.|
|Instruktions text för uppföljnings instruktioner|När du använder en uppföljnings fråga i ett konversations flöde kanske du inte behöver ett svar i QnA-paret eftersom du vill att användaren ska välja från uppföljnings anvisningarna. I det här fallet anger du en speciell text genom att ställa in standard svars texten, som returneras med varje förutsägelse för uppföljnings instruktioner. Texten är avsedd att visas som instruktions text för att välja uppföljnings instruktioner. Ett exempel på den här standard svars texten är `Please select from the following choices` . Den här konfigurationen förklaras i nästa avsnitt i det här dokumentet. Kan också anges som en del av kunskaps bas definitionen för att `defaultAnswerUsedForExtraction` använda [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

### <a name="client-application-integration"></a>Integrering av klient program

För ett klient program, till exempel en robot med **Azure bot service**, kan du välja bland följande scenarier:

* Använd kunskaps bas inställningen
* Använd annan text i klient programmet för att särskilja när ett svar returneras men inte uppfyller Poäng tröskelvärdet. Texten kan antingen vara statisk text Sparad i kod eller lagras i klient programmets inställnings lista.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Ange uppföljnings frågans standard svar när du skapar kunskaps basen

När du skapar en ny kunskaps bas är standard svars texten en av inställningarna. Om du väljer att inte ställa in den under skapande processen kan du ändra den senare med följande procedur.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>Ändra uppföljnings frågans standard svar i QnA Maker Portal

Standard svaret för kunskaps basen returneras när inget svar returneras från QnA Makers tjänsten.

1. Logga in på [QNA Maker Portal](https://www.qnamaker.ai/) och välj din kunskaps bas i listan.
1. Välj **Inställningar** i navigerings fältet.
1. Ändra värdet för **standard svars text** i avsnittet **Hantera kunskaps bas** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Skärm bild av QnA Maker Portal, sidan Inställningar, med text rutan standard svar markerad.":::

1. Välj **Spara och träna** för att spara ändringen.

## <a name="next-steps"></a>Nästa steg

* [Skapa en kunskaps bas](../How-to/manage-knowledge-bases.md)
