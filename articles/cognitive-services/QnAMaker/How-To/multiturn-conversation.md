---
title: Flera aktivera konversationer
titleSuffix: Azure Cognitive Services
description: Använd anvisningarna och kontext för att hantera flera aktiverar, kallas även flera aktivera för din robot från en fråga till en annan. Flera aktivera är möjligheten att ha en tillbaka- och fråntrafiken konversationen där föregående fråga kontext påverkar nästa frågor och svar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 24158d5949c0a31e5444c609cbfced970bdeca07
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471934"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Använd uppföljning uppmanas du för att skapa flera varv en konversation

Använda Uppföljnings uppmaningar och kontext för att hantera flera aktiverar, kallas _flera aktivera_, för din robot från en fråga till en annan.

Lär dig från den [demonstrationsvideo](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Vad är en konversation med flera aktivera?

Vissa typer av konversationen kan inte slutföras i en enda aktivera. När du utformar din klient program (chattrobot) konversationer, kan en användare Ställ en fråga som ska filtreras eller finjusteras för att fastställa rätt svar. Det här flödet igenom frågorna är möjligt genom att presentera användaren med **uppföljning uppmanar**.

När användaren ber frågan, QnA Maker returnerar svaret _och_ eventuella Uppföljnings uppmaningar. På så sätt kan du presentera uppföljande frågor som alternativ. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exempel flera aktivera konversationen med chattrobot

En chattrobot hanterar konversationen kan fråga genom att fråga med användaren för att avgöra det slutliga svaret.

![Hantera konversationen tillstånd inom konversationsanpassade flödet, i ett system med flera Stäng dialogrutan genom att tillhandahålla meddelanden i svaren visas som alternativ för att fortsätta konversationen.](../media/conversational-context/conversation-in-bot.png)

Användarens fråga måste vara förfinat innan det returneras svaret i den föregående bilden. Fråga (#1), har fyra Uppföljnings anvisningarna som visas i chattrobot som fyra alternativ (#2) i knowledge base. 

När användaren väljer ett alternativ (3), visas nästa listan över förfina alternativ (#4). Detta kan fortsätta (5) tills rätt och slutlig svaret (6) bestäms.

Du måste ändra ditt klientprogram för att hantera sammanhangsberoende konversationen.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Skapa en konversation med flera aktivera från dokumentets struktur
När du skapar en kunskapsbas visas ett valfritt kryssrutan om du vill aktivera flera aktivera extrahering. Om du väljer det här alternativet när du importerar ett dokument, kan flera aktivera konversationen ges från strukturen. Om denna struktur finns skapar QnA Maker Uppföljnings fråga QnA-par. Flera aktivera struktur kan endast härledas från URL: er, PDF eller DOCX filer. 

Följande bild av en Microsoft Surface- [PDF-fil](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) är avsedd att användas som en manuell. 

![! [Om du importerar ett dokument, sammanhangsberoende konversationen kan ges från strukturen. Om denna struktur finns skapar QnA Maker Uppföljnings fråga QnA-par, som en del av dokumentet importen.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

När du importerar PDF-dokumentet, anger QnA Maker Uppföljnings anvisningarna från struktur att skapa konversationsanpassade flöde. 

![! [När du importerar PDF-dokumentet, anger Uppföljnings anvisningarna från struktur att skapa konversationsanpassade flöde med QnA Maker. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="filter-questions-and-answers-by-context"></a>Filtrera frågor och svar av kontext

1. Minska frågor och svar-par som visas för bara de med sammanhangsberoende konversationer. Välj **Visningsalternativ**och välj sedan **Show kontext (FÖRHANDSVERSION)**. Listan ska vara tom tills du lägger till det första nyckelparet med frågor och svar med en uppföljning uppmaning. 

    ![Filtrera frågan och besvara par av sammanhangsbaserad konversationer](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Lägga till nya frågor och svar om par som uppföljning fråga

1. Välj **Lägg till frågor och svar om par**. 
1. Ange ny text för fråga, `Give feedback.` med ett svar av `What kind of feedback do you have?`.

1. I den **svar** kolumnen för den här frågan väljer **Lägg till Uppföljnings prompten**. 
1. Den **uppföljning prompten** standardarbetsytan i dialogrutan kan du söka efter en befintlig fråga eller ange en ny fråga. Ange texten i den här proceduren `Feedback on an QnA Maker service`, för den **visa text**. 
1. Kontrollera **endast kontext**. Den **endast kontext** alternativet anger att den här användaren texten är att förstå _endast_ om svar till föregående fråga. Texten tjänar inte något som en fristående fråga i det här scenariot, det finns ingen anledning från kontexten för den föregående frågan.
1. I den **länk att besvara** text anger svaret, `How would you rate QnA Maker?`.
1. Välj **Skapa nytt** därefter **spara**. 

    ![Skapa ny fråga frågor och svar](../media/conversational-context/create-child-prompt-from-parent.png)

    Detta skapas ett nytt par frågor och svar och länka den valda frågan som en Uppföljnings-kommandotolk. Den **kontext** kolumnen båda frågor kan indikera en uppföljning fråga relation. 

    ![! [Kolumnen kontext för båda frågor, anger en uppföljning fråga relation.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Välj **Lägg till Uppföljnings prompten** för den `Give feedback` fråga att lägga till en annan Uppföljnings prompten. 
1. Skapa en ny fråga genom att ange `Feedback on an existing feature`, med svaret `Which feature would you like to give feedback on?`.  

1.  Kontrollera **endast kontext**. Den **endast kontext** alternativet anger att den här användaren texten är att förstå _endast_ om svar till föregående fråga. Texten tjänar inte något som en fristående fråga i det här scenariot, det finns ingen anledning från kontexten för den föregående frågan.
1.  Välj **Spara**. 

    Detta skapas en ny fråga och länkade frågan som en uppföljning fråga fråga till den `Give feedback` fråga.
    
    Nu översta frågan har två uppföljande frågor som gillade till föregående fråga `Give feedback`.

    ![! [Nu översta frågan har två uppföljande frågor som gillade till föregående fråga, ge feedback'] (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Välj **spara och träna** att träna en kunskapsbas med nya frågor. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Lägga till befintliga QnA-par som uppföljning fråga

1. Om du vill länka en befintlig QnA-par som Kommandotolken Uppföljnings väljer du raden för frågor och svar-par.
1. Välj **Lägg till Uppföljnings prompten** på den raden.
1. Ange frågetexten i sökrutan i popup-dialogrutan. Alla matchningar returneras. Välj den fråga du vill ha som uppföljningen och kontrollera **endast kontext**och välj sedan **spara**. 

    När ou har lagt till Uppföljnings prompten, Kom ihåg att välja **spara och träna**.
  
## <a name="add-metadata-to-follow-up-prompts"></a>Lägga till metadata Uppföljnings anvisningarna 

I kunskapsbasen, när ett par frågor och svar är länkad till uppföljning uppmanar, filter för filmetadata tillämpas först och sedan de uppföljning, så returneras.

1. Lägg till metadata till var och en för de två Uppföljnings QnA-par:

    |Fråga|lägga till metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|”Funktionen”: ”alla”|
    |`Feedback on an existing feature`|”Funktionen”: ”en”|
    
    ![Lägger till metadata Uppföljnings fråga så att den kan filtreras i konversationen svaret från tjänsten](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Spara och träna. 

    När du skickar frågan `Give feedback` med filtret metadata `Feature` med värdet `all`, frågor och svar om paret med dessa metadata kommer att returneras. Frågor och svar om paren returneras inte eftersom de båda inte matchar filtret. 

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>Testa QnA ange för att hämta alla uppföljning uppmanar

När testningen fråga med uppföljning meddelanden i den **Test** väljer **aktivera flera aktivera**, och ange din fråga. Svaret innehåller Uppföljnings anvisningarna.

![När du testar frågan i rutan innehåller svaret Uppföljnings anvisningarna.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON-begäran för att returnera inledande svar och fråga om uppföljning

Använd tomt `context` objekt för att begära svaret på användarens fråga och inkludera Uppföljnings anvisningarna. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>JSON-svar för att returnera inledande svar och anvisningarna för uppföljning

Föregående avsnitt begärt svar och eventuella Uppföljnings uppmaningar att `Accounts and signing in`. Svaret innehåller informationen fråga på `answers[0].context`, innehåller texten som ska visas för användaren. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

Den `prompts` matris innehåller texten i den `displayText` egenskapen och `qnaId` värde så att du kan visa dessa frågor som nästa visas alternativen i flödet för konversationen. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON-begäran för att returnera icke första svar och fråga om uppföljning

Fyll i `context` objekt som inkluderas tidigare kontext.

I följande JSON-begäran, den aktuella frågan är `Use Windows Hello to sign in` och föregående fråga var `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON-svar för att returnera icke första svar och anvisningarna för uppföljning

QnA Maker _GenerateAnswer_ JSON-svaret innehåller Uppföljnings anvisningarna i den `context` egenskapen för det första objektet i den `answers` objekt:

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Visa meddelanden och skicka kontexten i klientprogrammet 

Det visar alla frågor med ett alternativ för användare att visa anvisningarna som föreslagna åtgärder eller knappar i klientprogrammet.
Klientprogrammet sedan lagrar den aktuella frågor och svar om ett nyckelpar och användaren frågan som skickas vidare med nästa användarfråga-kontexten. 

Använd det här [Bot Framework exempel](https://aka.ms/qnamakermultiturnsample) att se flera Stäng dialogrutan fungerande slutpunkt till slutpunkt i en QnA Maker-robot.


## <a name="prompt-order-supported-in-api"></a>Stöds i API: et för promptfrågor

Fråga ordern, returneras i JSON-svar finns stöd för redigering av API: et endast. 

## <a name="next-steps"></a>Nästa steg

Mer information om sammanhangsberoende konversationer från den [dialogrutan exempel](https://aka.ms/qnamakermultiturnsample) eller Lär dig mer [konceptuella bot utforma för flera aktivera konversationer](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)
