---
title: Flera aktivera konversationer
titleSuffix: Azure Cognitive Services
description: Använd anvisningarna och kontext för att hantera flera aktiverar, kallas även flera aktivera för din robot från en fråga till en annan. Flera aktivera är möjligheten att ha en konversation fram och tillbaka där föregående fråga kontext påverkar nästa frågor och svar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508134"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Använd uppföljning uppmanas du för att skapa flera varv en konversation

Använda Uppföljnings uppmaningar och kontext för att hantera flera aktiverar, kallas _flera aktivera_, för din robot från en fråga till en annan.

Visa följande demonstration videoklipp om du vill se hur det fungerar med flera aktivera:

[![Flera aktivera konversationen i QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Vad är en konversation med flera aktivera?

Några frågor kan inte besvaras i en enda aktivera. När du utformar din klient program (chattrobot) konversationer, kan en användare Ställ en fråga som ska filtreras eller finjusteras för att fastställa rätt svar. Du gör det här flödet igenom frågorna möjligt genom att presentera användaren med *uppföljning uppmanar*.

När en användare ställer en fråga, QnA Maker returnerar svaret _och_ eventuella Uppföljnings uppmaningar. Svaret kan du presentera uppföljande frågor som alternativ. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exempel flera aktivera konversationen med chattrobot

Med flera tur och ordning hanterar en chattrobot en konversation med en användare att avgöra det slutliga svaret som du ser i följande bild:

![En dialogruta för flera aktivera uppmaningar som hjälper användaren genom en konversation](../media/conversational-context/conversation-in-bot.png)

I den föregående bilden är en användare har startat en konversation genom att ange **mitt konto**. Kunskapsbasen har tre länkade frågor och svar-par. För att förfina svaret väljer användaren en av de tre alternativen i knowledge base. Fråga (#1), har tre Uppföljnings anvisningarna som visas i chattrobot som tre alternativ (#2). 

När användaren väljer ett alternativ (3), visas nästa listan över förfina alternativ (#4). Den här sekvensen fortsätter (5) tills användaren anger rätt, sista svaret (6).

> [!NOTE]
> I den föregående bilden är det **aktivera flera aktivera** kryssrutan har valts för att säkerställa att anvisningarna visas. 

### <a name="use-multi-turn-in-a-bot"></a>Använda flera aktivera i en robot

För att hantera sammanhangsberoende konversationen, ändra ditt klientprogram av [att lägga till kod till din robot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Lägga till koden kan användarna se anvisningarna.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Skapa en konversation med flera aktivera från ett dokument struktur

När du skapar en kunskapsbas den **fylla i din Kunskapsbas** avsnittet visar en **aktivera flera aktivera extraheringen från URL: er, .pdf eller .docx filer** markerar du kryssrutan. 

![Markera kryssrutan för att aktivera flera aktivera extrahering](../media/conversational-context/enable-multi-turn.png)

När du väljer det här alternativet för ett importerat dokument, kan flera aktivera konversationen ges från dokumentstrukturen. Om denna struktur finns, skapar QnA Maker Uppföljnings uppmaningen att par frågor och svar som en del av importprocessen. 

Flera aktivera struktur kan härledas från URL: er, PDF-filer eller DOCX-filer. Ett exempel på struktur, visa en bild av en [manuell PDF-fil i Microsoft Surface-användaren](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). På grund av storleken på den här PDF-fil, QnA Maker-resursen kräver en **Search prisnivån** av **B** (15 index) eller större. 

![! [Exempel på strukturen i en användarhandboken] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

När du importerar PDF-dokumentet anger QnA Maker uppföljning uppmaningar från strukturen för att skapa konversationsanpassade flöde. 

1. Välj i QnA Maker **skapa en kunskapsbas**.
1. Skapa eller använda en befintlig QnA Maker-tjänst. I föregående exempel för Microsoft Surface, eftersom PDF-filen är för stor för en mindre nivå, använda en QnA Maker-tjänsten med en **söktjänsten** av **B** (15 index) eller större.
1. Ange ett namn för din kunskapsbas som **Surface manuell**.
1. Välj den **aktivera flera aktivera extraheringen från URL: er, .pdf eller .docx filer** markerar du kryssrutan. 
1. Välj Manuell Webbadressen Surface **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Välj den **skapa din Kunskapsbas** knappen. 

    När kunskapsbasen har skapats, visas en vy över frågor och svar-par.

## <a name="show-questions-and-answers-with-context"></a>Visa frågor och svar med kontext

Minska visas frågor och svar-par till sådana med sammanhangsberoende konversationer. 

Välj **Visningsalternativ**, och välj sedan **Show kontext (FÖRHANDSVERSION)** . Listan visar frågor och svar-värdepar som innehåller Uppföljnings anvisningarna. 

![Filtrera frågor och svar-par av sammanhangsbaserad konversationer](../media/conversational-context/filter-question-and-answers-by-context.png)

Flera aktivera kontexten visas i den första kolumnen.

![! [Kolumnen ”kontext (FÖRHANDSVERSION)”] (.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

I den föregående bilden **#1** anger fetstil i kolumnen, vilket innebär det att den aktuella frågan. Den överordnade frågan är det översta objektet i raden. Frågor under den är länkad frågor och svar-par. Dessa objekt är valbara, så att du omedelbart kan gå till de andra objekt i kontexten. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Lägg till ett befintligt nyckelpar med frågor och svar som Kommandotolken uppföljning

Den ursprungliga frågan **mitt konto**, har Uppföljnings anvisningarna som **konton och logga in**. 

![Den ”konton och logga in” svar och anvisningarna för uppföljning](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Lägg till en uppföljning uppmaning till ett befintligt nyckelpar med frågor och svar som för närvarande inte är länkad. Eftersom frågan inte är kopplad till alla frågor och svar-par, måste den aktuella vyn inställningen ändras.

1. För att länka ett befintligt nyckelpar med frågor och svar som Kommandotolken Uppföljnings, Välj raden för frågor och svar-par. Surface manuell, söker du efter **logga ut** att minska listan.
1. På raden för **Sidomeny**i den **svar** kolumnen, markerar **Lägg till Uppföljnings prompten**.
1. I fälten i den **uppföljning fråga (FÖRHANDSVERSION)** popup-fönstret anger du följande värden:

    |Fält|Värde|
    |--|--|
    |Visa text|Ange **inaktivera enheten**. Det här är anpassad text att visa Uppföljnings-prompten.|
    |Endast kontext| Markera den här kryssrutan. Ett svar returneras bara om frågan anger kontexten.|
    |Länk för att besvara|Ange **använda på inloggningsskärmen** att hitta den befintliga kombinationen av frågor och svar.|


1.  En matchning returneras. Välj det här svaret som uppföljningen och välj sedan **spara**. 

    ![Sidan ”Uppföljning fråga (FÖRHANDSVERSION)”](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. När du har lagt till Uppföljnings Kommandotolken, Välj **spara och träna** i det övre navigeringsfältet.
  
### <a name="edit-the-display-text"></a>Redigera texten 

När Kommandotolken Uppföljnings har skapats och ett befintligt nyckelpar med frågor och svar har angetts som den **länk till svar**, du kan ange en ny **visa text**. Den här texten ersätta inte den befintliga frågan och läggs en ny alternativa fråga inte. Den skiljer sig från dessa värden. 

1. Om du vill redigera texten, Sök efter och Välj fråga i den **kontext** fält.
1. Markera kryssrutan Uppföljnings fråga i kolumnen svar på raden för frågan. 
1. Välj Visa texten som du vill redigera och välj sedan **redigera**.

    ![Med redigeringskommandot för texten](../media/conversational-context/edit-existing-display-text.png)

1. I den **uppföljning prompten** popup-fönster, ändra befintliga texten som visas. 
1. När du är klar redigera texten som visas, Välj **spara**. 
1. I det övre navigeringsfältet **spara och träna**.


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Lägg till ett nytt par frågor och svar som Kommandotolken uppföljning

När du lägger till ett nytt par frågor och svar i kunskapsbasen bör varje par vara kopplat till en befintlig fråga som en Uppföljnings-kommandotolk.

1. I verktygsfältet kunskapsbas Sök efter och välj den befintliga frågor och svar-par för **konton och logga in**. 

1. I den **svar** kolumnen för den här frågan väljer **Lägg till Uppföljnings prompten**. 
1. Under **uppföljning fråga (FÖRHANDSVERSION)** , skapa en ny Uppföljnings-kommandotolk genom att ange följande värden: 

    |Fält|Värde|
    |--|--|
    |Visa text|*Skapa ett Windows-konto*. Anpassad text att visa Uppföljnings-prompten.|
    |Endast kontext|Markera den här kryssrutan. Det här svaret returneras bara om frågan anger kontexten.|
    |Länk för att besvara|Ange följande text som svar:<br>*[Skapa](https://account.microsoft.com/) ett Windows-konto med ett nytt eller befintligt e-postkonto*.<br>När du sparar och träna databasen kommer att konverteras den här texten. |
    |||

    ![Skapa en ny fråga frågor och svar](../media/conversational-context/create-child-prompt-from-parent.png)


1. Välj **Skapa nytt**, och välj sedan **spara**. 

    Den här åtgärden skapar ett nytt par för frågor och svar och länkar den valda frågan som en Uppföljnings-kommandotolk. Den **kontext** kolumn för båda frågor, visar en uppföljning fråga relation. 

1. Välj **Visningsalternativ**, och välj sedan [ **Show kontext (FÖRHANDSVERSION)** ](#show-questions-and-answers-with-context).

    Den nya frågan visar hur den är länkad.

    ![Skapa en ny Uppföljnings-kommandotolk](../media/conversational-context/new-qna-follow-up-prompt.png)

    Den överordnade frågan visar en ny fråga som en av dess alternativ.

    ![! [Kolumnen kontext för båda frågor, visar en uppföljning fråga relation] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. När du har lagt till Uppföljnings Kommandotolken, Välj **spara och träna** i det övre navigeringsfältet.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Aktivera flera aktivera under testningen av Uppföljnings anvisningarna

När du testar att fråga med uppföljning meddelanden i den **testa** väljer **aktivera flera aktivera**, och sedan ange din fråga. Svaret innehåller Uppföljnings anvisningarna.

![Svaret innehåller anvisningarna för uppföljning](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Om du inte aktiverar flera aktivera svaret returneras men Uppföljnings anvisningarna returneras inte.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>En JSON-begäran för att returnera ett inledande svar och fråga om uppföljning

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

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Ett JSON-svar för att returnera ett inledande svar och anvisningarna för uppföljning

Föregående avsnitt begärt svar och eventuella Uppföljnings uppmaningar att **konton och logga in**. Svaret innehåller den fråga informationen, som finns i *svar [0] .context*, och texten som ska visas för användaren. 

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
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
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

Den `prompts` matris innehåller texten i den `displayText` egenskapen och `qnaId` värde. Du kan visa dessa frågor som nästa visas valen i konversationen flow och skicka sedan den valda `qnaId` tillbaka till QnA Maker i följande begäran. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>En JSON-begäran för att returnera ett icke-första svar och fråga om uppföljning

Fyll i `context` objekt som inkluderas tidigare kontexten.

I följande JSON-begäran, den aktuella frågan är *Använd Windows Hello för att logga in* och föregående fråga var *konton och logga in*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Ett JSON-svar för att returnera ett icke-första svar och anvisningarna för uppföljning

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Söka i knowledge base med QnA Maker-ID

I den första frågan svar, eventuella Uppföljnings uppmaningar och dess associerade `qnaId` returneras. Nu när du har det ID: T kan skicka du det i begärandetexten för uppföljning prompten. Om begärandetexten innehåller den `qnaId`, och context-objektet (som innehåller de föregående QnA Maker-egenskaperna), och sedan GenerateAnswer returnerar exakta fråga efter ID, istället för att använda Rangordningsalgoritmen för att hitta svaret efter frågetexten. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Visa meddelanden och skicka kontexten i klientprogrammet 

Du har lagt till anvisningarna i kunskapsbasen och testas flödet i rutan. Nu ska du använda de här anvisningarna i klientprogrammet. För Bot Framework visas anvisningarna automatiskt inte i klientprogram. Du kan visa anvisningarna som föreslagna åtgärder eller knappar som en del av svaret på användarens fråga i klientprogram genom att inkludera detta [Bot Framework exempel](https://aka.ms/qnamakermultiturnsample) i din kod. Klientprogrammet lagra det aktuella QnA Maker-ID och användarfrågan och skicka dem i den [context-objektet GenerateAnswer API](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) för nästa användarfråga. 

## <a name="display-order-is-supported-in-the-update-api"></a>Visningsordningen stöds i Update-API

Den [text som visas och visningsordning](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), returneras i JSON-svar, det finns stöd för redigering av den [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Skapa en kunskapsbas med flera aktivera anvisningarna med Create API

Du kan skapa ett ärende knowledge flera aktivera uppmaningar med hjälp av den [skapa API för QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Lägger till anvisningarna i den `context` egenskapens `prompts` matris. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Lägga till eller ta bort flera aktivera frågorna med Update-API

Du kan lägga till eller ta bort flera Aktivera meddelanden med hjälp av den [QnA Maker Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Lägger till anvisningarna i den `context` egenskapens `promptsToAdd` matris och `promptsToDelete` matris. 


## <a name="next-steps"></a>Nästa steg

Mer information om sammanhangsberoende konversationer från den här [dialogrutan exempel](https://aka.ms/qnamakermultiturnsample) eller Läs mer om [konceptuella bot utforma för flera aktivera konversationer](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)
