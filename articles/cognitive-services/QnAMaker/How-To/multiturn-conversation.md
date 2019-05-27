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
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 0aab2250d6692d7db99c55b23604c08f5fe619a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235707"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Använd uppföljning uppmanas du för att skapa flera varv en konversation

Använda Uppföljnings uppmaningar och kontext för att hantera flera aktiverar, kallas _flera aktivera_, för din robot från en fråga till en annan.

Titta på följande videoklipp för demonstration om du vill se hur det är klart.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Vad är en konversation med flera aktivera?

Några frågor kan inte besvaras i en enda aktivera. När du utformar din klient program (chattrobot) konversationer, kan en användare Ställ en fråga som ska filtreras eller finjusteras för att fastställa rätt svar. Det här flödet igenom frågorna är möjligt genom att presentera användaren med **uppföljning uppmanar**.

När användaren ber frågan, QnA Maker returnerar svaret _och_ eventuella Uppföljnings uppmaningar. På så sätt kan du presentera uppföljande frågor som alternativ. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exempel flera aktivera konversationen med chattrobot

En chattrobot hanterar konversationen med användaren, fråga genom att fråga, för att avgöra det slutliga svaret.

![Hantera konversationen tillstånd inom konversationsanpassade flödet, i ett system med flera Stäng dialogrutan genom att tillhandahålla meddelanden i svaren visas som alternativ för att fortsätta konversationen.](../media/conversational-context/conversation-in-bot.png)

I den föregående bilden användaren angett `My account`. Kunskapsbasen har 3 länkade QnA-par. Användaren måste välja bland de tre alternativen att förfina svaret. Fråga (#1), har tre Uppföljnings anvisningarna som visas i chattrobot som tre alternativ (#2) i knowledge base. 

När användaren väljer ett alternativ (3), visas nästa listan över förfina alternativ (#4). Detta kan fortsätta (5) tills rätt och slutlig svaret (6) bestäms.

Föregående bild har **aktivera flera aktivera** för att kunna visas anvisningarna. 

### <a name="using-multi-turn-in-a-bot"></a>Använda flera aktivera i en robot

Du måste ändra ditt klientprogram för att hantera sammanhangsberoende konversationen. Du måste lägga till [kod till din robot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) att se anvisningarna.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Skapa en konversation med flera aktivera från ett dokument struktur

När du skapar en kunskapsbas visas ett valfritt kryssrutan om du vill aktivera flera aktivera extrahering. 

![När du skapar en kunskapsbas visas ett valfritt kryssrutan om du vill aktivera flera aktivera extrahering.](../media/conversational-context/enable-multi-turn.png)

Om du väljer det här alternativet när du importerar ett dokument, kan flera aktivera konversationen ges från strukturen. Om denna struktur finns skapar QnA Maker Uppföljnings fråga QnA-par. 

Flera aktivera struktur kan endast härledas från URL: er, PDF eller DOCX filer. 

Följande bild av en Microsoft Surface- [PDF-fil](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) är avsedd att användas som en manuell. På grund av storleken på den här PDF-filen kan Azure QnA Maker-resursen kräver sökningen prisnivån för B (15 index) eller större. 

![! [Om du importerar ett dokument, sammanhangsberoende konversationen kan ges från strukturen. Om denna struktur finns skapar QnA Maker Uppföljnings fråga QnA-par, som en del av dokumentet importen.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

När du importerar PDF-dokumentet, anger QnA Maker Uppföljnings anvisningarna från struktur att skapa konversationsanpassade flöde. 

1. I **steg 1**väljer **skapa en kunskapsbas** från det övre navigeringsfältet.
1. I **steg 2**, skapa eller använda en befintlig tjänst för frågor och svar. Se till att använda en frågor och svar om tjänsten med en söktjänst b (15 index) eller senare eftersom Surface manuell PDF-filen är för stor för en mindre nivå.
1. I **steg3**, ange ett namn för din kunskapsbas som `Surface manual`.
1. I **steg 4**väljer **aktivera flera aktivera extraheringen från URL: er, .pdf eller docx-filer.** Välj URL: en för den Surface manuellt

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Välj den **skapa din Kunskapsbas** knappen. 

    När informationen har skapats, visar en översikt över frågor och svar-par.

## <a name="show-questions-and-answers-with-context"></a>Visa frågor och svar med kontext

Minska frågor och svar-par som visas för bara de med sammanhangsberoende konversationer. 

1. Välj **Visningsalternativ**och välj sedan **Show kontext (FÖRHANDSVERSION)** . I listan visas frågor och svar-par som innehåller Uppföljnings anvisningarna. 

    ![Filtrera frågan och besvara par av sammanhangsbaserad konversationer](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Flera aktivera kontexten visar i den första kolumnen.

    ![! [När du importerar PDF-dokumentet, anger Uppföljnings anvisningarna från struktur att skapa konversationsanpassade flöde med QnA Maker. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    I den föregående bilden anger #1 fetstil i kolumnen, vilket innebär det att den aktuella frågan. Den överordnade frågan är det översta objektet i raden. Frågor nedan är de länkade frågor och svar-par. De här objekten är valbara så omedelbart går du till de andra objekt i kontexten. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Lägga till befintliga QnA-par som uppföljning fråga

Den ursprungliga frågan om `My account` har Uppföljnings anvisningarna som `Accounts and signing in`. 

![Den ursprungliga frågan för mitt konto returnerar den ”konton och logga in” besvara och uppföljning anvisningarna länkad finns redan.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Lägg till en uppföljning uppmaning till ett befintligt nyckelpar med frågor och svar om som för närvarande inte är länkad. Eftersom frågan inte är kopplad till någon QnA-par, behöver den aktuella vyn inställningen ändras.

1. Om du vill länka en befintlig QnA-par som Kommandotolken Uppföljnings, Välj raden för frågor och svar-par. Surface manuell, söker du efter `Sign out` att minska listan.
1. På raden för `Signout`väljer **Lägg till Uppföljnings prompten** från den **svar** kolumn.
1. I den **uppföljning fråga (FÖRHANDSVERSION)** popup-fönstret anger du följande:

    |Fält|Värde|
    |--|--|
    |Visa text|`Turn off the device`. Det här är anpassad text som du vill visa Uppföljnings-prompten.|
    |Endast kontext|Valt. Det här svaret returneras bara om frågan anger kontexten.|
    |Länk till svar|Ange `Use the sign-in screen` att hitta befintliga frågor och svar om paret.|


1.  En matchning returneras. Välj det här svaret som uppföljningen och välj sedan **spara**. 

    ![Sök prompten uppföljning länken till svar dialogrutan för ett befintligt svar med texten i svaret.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. När du har lagt till Uppföljnings prompten, Kom ihåg att välja **spara och träna** i det övre navigeringsfältet.
  
### <a name="edit-the-display-text"></a>Redigera texten 

När Kommandotolken Uppföljnings har skapats och ett befintligt nyckelpar med frågor och svar har markerats som den **länk till svar**, du kan ange en ny **visa text**. Den här texten ersätta inte den befintliga frågan och läggs inte till en ny alternativa fråga. Den skiljer sig från dessa värden. 

1. Om du vill redigera texten, Sök efter och Välj fråga i den **kontext** fält.
1. Markera kryssrutan Uppföljnings fråga i kolumnen svar på den frågan rad. 
1. Välj Visa texten som du vill redigera och välj sedan **redigera**.

    ![Välj Visa texten som du vill redigera och välj sedan redigera.](../media/conversational-context/edit-existing-display-text.png)

1. Den **uppföljning prompten** popup-fönstret kan du ändra den befintliga texten som visas. 
1. När du är klar redigera texten som visas, Välj **spara**. 
1. Kom ihåg att välja **spara och träna** i det övre navigeringsfältet.


<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Lägga till nya frågor och svar om par som uppföljning fråga

Lägg till ett nytt QnA-par i kunskapsbasen. Frågor och svar om paret ska kopplas till en befintlig fråga som Kommandotolken uppföljning.

1. I knowledge base verktygsfältet Sök efter och välj den befintliga QnA-par för `Accounts and Signing In`. 

1. I den **svar** kolumnen för den här frågan väljer **Lägg till Uppföljnings prompten**. 
1. Den **uppföljning fråga (FÖRHANDSVERSION)** , skapa en ny Uppföljnings-kommandotolk genom att ange följande värden: 

    |Textfält|Värde|
    |--|--|
    |**Visa Text**|`Create a Windows Account`. Det här är anpassad text som du vill visa Uppföljnings-prompten.|
    |**Endast kontext**|Valt. Det här svaret returneras bara om frågan anger kontexten.|
    |**Länk för att besvara**|Ange följande text som svar:<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>När du sparar och träna databasen, kommer den här texten att konverteras till |
    |||

    ![Skapa ny fråga frågor och svar](../media/conversational-context/create-child-prompt-from-parent.png)


1. Välj **Skapa nytt** därefter **spara**. 

    Detta skapas ett nytt par frågor och svar och länka den valda frågan som en Uppföljnings-kommandotolk. Den **kontext** kolumn för båda frågor, visar en uppföljning fråga relation. 

1. Ändra den **Visningsalternativ** till [visa kontext](#show-questions-and-answers-with-context).

    Den nya frågan visar hur den är länkad.

    ![Skapa en ny Uppföljnings-kommandotolk ](../media/conversational-context/new-qna-follow-up-prompt.png)

    Den överordnade frågan visar den nya frågan som en av dess alternativ.

    ![! [Kolumnen kontext för båda frågor, anger en uppföljning fråga relation.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. När du har lagt till Uppföljnings prompten, Kom ihåg att välja **spara och träna** i det övre navigeringsfältet.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Aktivera flera aktivera när testning uppföljning uppmanar

När testningen fråga med uppföljning meddelanden i den **Test** väljer **aktivera flera aktivera**, och ange din fråga. Svaret innehåller Uppföljnings anvisningarna.

![När du testar frågan i rutan innehåller svaret Uppföljnings anvisningarna.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Om du inte aktiverar flera aktivera svaret returneras men Uppföljnings anvisningarna returneras inte.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON-begäran för att returnera inledande svar och fråga om uppföljning

Använd tomt `context` objekt för att begära svaret på användarens fråga och inkludera Uppföljnings anvisningarna. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
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
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

Den `prompts` matris innehåller texten i den `displayText` egenskapen och `qnaId` värdet så att du kan visa dessa frågor som de nästa visas konversationen flödet skicka det valda värdet för QnA Maker i följande begäran. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON-begäran för att returnera icke första svar och fråga om uppföljning

Fyll i `context` objekt som inkluderas tidigare kontext.

I följande JSON-begäran, den aktuella frågan är `Use Windows Hello to sign in` och föregående fråga var `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
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
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Fråga kunskapsbas med frågor och svar-ID

I den första frågan svar, eventuella Uppföljnings uppmaningar och dess associerade `qnaId` returneras. Nu när du har det ID: T kan skicka du det i begärandetexten för uppföljning prompten. Om begärandetexten innehåller den `qnaId`, och context-objektet (som innehåller egenskaperna som tidigare frågor och svar om) och sedan GenerateAnswer returnerar exakta fråga efter ID, istället för att använda Rangordningsalgoritmen för att hitta svaret efter frågetexten. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Visa meddelanden och skicka kontexten i klientprogrammet 

Du har lagt till anvisningarna i kunskapsbasen och testas flödet i rutan. Nu ska du använda de här anvisningarna i klientprogrammet. För Bot Framework startar anvisningarna automatiskt inte visas i klientprogram. Du kan visa anvisningarna som föreslagna åtgärder eller knappar som en del av svaret på användarens fråga i klienten program genom att inkludera detta [Bot Framework exempel](https://aka.ms/qnamakermultiturnsample) i din kod. Klientprogrammet lagra det aktuella frågor och svar-ID och användarfrågan och skicka dem i den [context-objektet GenerateAnswer API](#json-request-to-return-non-initial-answer-and-follow-up-prompts) för nästa användarfråga. 

## <a name="display-order-supported-in-api"></a>Visningsordningen som stöds i API: et

Den [text som visas och visningsordning](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), returneras i JSON-svar, det finns stöd för redigering av den [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

Åtgärda – måste gå till överordnad och svaret kolumnen och sedan Redigera svar. 

## <a name="next-steps"></a>Nästa steg

Mer information om sammanhangsberoende konversationer från den [dialogrutan exempel](https://aka.ms/qnamakermultiturnsample) eller Lär dig mer [konceptuella bot utforma för flera aktivera konversationer](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)
