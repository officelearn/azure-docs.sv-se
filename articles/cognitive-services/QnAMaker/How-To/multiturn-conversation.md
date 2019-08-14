---
title: Flera-turn-konversationer – QnA Maker
titleSuffix: Azure Cognitive Services
description: Använd prompter och kontext för att hantera flera sätt, så kallade flera varv, för din robot från en fråga till en annan. Med flera varv kan du få en snabb konversation där föregående frågas sammanhang påverkar nästa fråga och svar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 585dc03503a61ff6666d3da3374586287e24283f
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966705"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Använd uppföljnings anvisningarna för att skapa flera av en konversation

Använd uppföljnings-och kontext hantering för att hantera flera _sätt_, så kallade flera varv, för din robot från en fråga till en annan.

Se följande demonstrations video om du vill se hur multi-turn fungerar:

[![Flera inaktiverade konversationer i QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Vad är en konversation med flera varv?

Vissa frågor kan inte besvaras i en enda tur. När du utformar dina klient program (Chat robot)-konversationer kan en användare ställa en fråga som behöver filtreras eller finjusteras för att fastställa rätt svar. Du gör det här flödet genom de frågor som är möjliga genom attpresentera användaren med uppföljnings anvisningarna.

När en användare ställer en fråga returnerar QnA Maker svaret _och_ eventuella uppföljnings frågor. Med det här svaret kan du Visa uppföljnings frågorna som alternativ. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exempel på flera samtal med Chat-roboten

Med flera turn hanterar en chat-robot en konversation med en användare för att fastställa det slutliga svaret, som du ser i följande bild:

![En dialog ruta med flera inaktivitet med instruktioner som vägleder användaren via en konversation](../media/conversational-context/conversation-in-bot.png)

I föregående bild har en användare startat en konversation genom att ange **mitt konto**. Kunskaps basen har tre länkade fråga-och-svar-par. Användaren kan förfina svaret genom att välja ett av de tre alternativen i kunskaps basen. Frågan (#1) har tre uppföljnings anvisningar som visas i Chat-roboten som tre alternativ (#2). 

När användaren väljer ett alternativ (#3) visas nästa lista över raffinerings alternativ (#4). Den här sekvensen fortsätter (#5) tills användaren bestämmer rätt, slut svar (#6).

> [!NOTE]
> I föregående bild har kryss rutan **aktivera flera-turn** marker ATS för att se till att prompterna visas. 

### <a name="use-multi-turn-in-a-bot"></a>Använda multi-turn i en robot

Om du vill hantera den kontextuella konversationen ändrar du klient programmet genom [att lägga till kod i din robot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Genom att lägga till koden kan användarna se prompterna.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Skapa en konversation med flera varv från ett dokuments struktur

När du skapar en kunskaps bas visar avsnittet **fylla i ditt KB** en **Aktivera extrahering av flera turn-filer från URL: er,. PDF eller. docx-filer** . 

![Kryss ruta för att aktivera extrahering av flera sätt](../media/conversational-context/enable-multi-turn.png)

När du väljer det här alternativet för ett importerat dokument kan konversationen med flera varv vara underförstådd från dokument strukturen. Om den strukturen finns skapar QnA Maker uppföljnings guiden som parar frågor och svar åt dig som en del av import processen. 

Flera-turn-strukturen kan bara härledas från URL: er, PDF-filer eller DOCX-filer. Ett exempel på en struktur finns i en bild av en [manuell PDF-fil för Microsoft Surface-användare](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). På grund av storleken på den här PDF-filen kräver QnA Maker resursen en **Sök pris nivå** på **B** (15 index) eller mer. 

![! [Exempel på en struktur i en Användar handbok] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

När du importerar PDF-dokumentet anger QnA Maker uppföljnings anvisningarna från strukturen för att skapa ett konversations flöde. 

1. I QnA Maker väljer du **skapa en kunskaps bas**.
1. Skapa eller Använd en befintlig QnA Maker-tjänst. I föregående exempel på Microsoft-arbetsytan, eftersom PDF-filen är för stor för en mindre nivå, använder du en QnA Maker tjänst med en **Sök tjänst** på **B** (15 index) eller mer.
1. Ange ett namn för din kunskaps bas, till exempel **Surface manual**.
1. Markera kryss rutan **Aktivera extrahering av flera turn från URL: er,. PDF eller. docx-filer** . 
1. Välj ytans manuella URL **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Välj knappen **skapa din KB** . 

    När kunskaps basen har skapats visas en vy över fråga-och-svar-par.

## <a name="show-questions-and-answers-with-context"></a>Visa frågor och svar med kontext

Minska antalet frågor och svar-par som visas till endast de med sammanhangsbaserade konversationer. 

Välj **visnings alternativ**och välj sedan **Visa kontext (för hands version)** . I listan visas fråga-och-svar-par som innehåller uppföljnings anvisningarna. 

![Filtrera frågor och svar-par efter sammanhangsbaserade konversationer](../media/conversational-context/filter-question-and-answers-by-context.png)

Kontexten för flera turn visas i den första kolumnen.

![! [Kolumnen "kontext (förhands granskning)"] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

I föregående bild anger **#1** fet text i kolumnen som visar den aktuella frågan. Den överordnade frågan är det översta objektet på raden. Alla frågor nedan är de länkade frågorna och svars paren. Dessa objekt är valbara, så att du kan gå direkt till andra kontext objekt. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Lägg till ett befintligt fråga-och-svar-par som en uppföljnings fråga

Den ursprungliga frågan, **mitt konto**, har uppföljnings frågor, till exempel **konton och inloggning**. 

![Svar och Uppföljnings frågor för "konton och signering"](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Lägg till en uppföljnings fråga till ett befintligt fråga-och-svar-par som inte är länkat. Eftersom frågan inte är länkad till några frågor och svar-par måste den aktuella visnings inställningen ändras.

1. Om du vill länka ett befintligt fråga-och-svar-par som en uppföljnings fråga väljer du raden för fråga-och-svar-paret. För Surface manual söker du efter **Logga ut** för att minska listan.
1. Välj **Lägg till uppföljnings fråga**i kolumnen **svar** i raden för att **Logga in**.
1. Ange följande värden i fälten i popup-fönstret för **uppföljnings prompten (för hands version)** :

    |Fält|Value|
    |--|--|
    |Visa text|Ange **Stäng av enheten**. Det här är en anpassad text som visas i uppföljnings anvisningarna.|
    |Endast Sammanhangs beroende| Markera den här kryss rutan. Ett svar returneras bara om frågan anger sammanhang.|
    |Länk till svar|Ange **Använd inloggnings skärmen** för att hitta det befintliga fråga-och-svar-paret.|


1.  En matchning returneras. Välj det här svaret som uppföljning och välj sedan **Spara**. 

    ![Sidan "uppföljnings prompt (förhands granskning)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. När du har lagt till uppföljnings anvisningarna väljer du **Spara och träna** i det övre navigerings fönstret.
  
### <a name="edit-the-display-text"></a>Redigera visnings texten 

När en uppföljnings fråga skapas och ett befintligt fråga-och-svar-par anges som **länk till svar**, kan du ange en ny **visnings text**. Den här texten ersätter inte den befintliga frågan och lägger inte till någon ny alternativ fråga. Den är separat från dessa värden. 

1. Om du vill redigera visnings texten söker du efter och väljer frågan i **kontext** fältet.
1. På raden för den frågan väljer du uppföljnings anvisningarna i kolumnen svar. 
1. Välj den visnings text som du vill redigera och välj sedan **Redigera**.

    ![Kommandot edit för visnings texten](../media/conversational-context/edit-existing-display-text.png)

1. Ändra den befintliga visnings texten i popup-fönstret för **uppföljnings frågan** . 
1. När du är klar med att redigera visnings texten väljer du **Spara**. 
1. I det övre navigerings fältet **sparar och tränar**du.


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

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Lägg till ett nytt fråga-och-svar-par som en uppföljnings fråga

När du lägger till ett nytt fråga-och-svar-par i kunskaps basen ska varje par länkas till en befintlig fråga som en uppföljnings fråga.

1. I verktygsfältet kunskaps bas söker du efter och väljer det befintliga fråge-och svars paret för **konton och loggar in**. 

1. Välj **Lägg till uppföljnings fråga**i **svars** kolumnen för den här frågan. 
1. Under **uppföljnings prompt (för hands version)** skapar du en ny uppföljning genom att ange följande värden: 

    |Fält|Value|
    |--|--|
    |Visa text|*Skapa ett Windows-konto*. Den anpassade text som ska visas i uppföljnings anvisningarna.|
    |Endast Sammanhangs beroende|Markera den här kryss rutan. Svaret returneras endast om frågan anger sammanhang.|
    |Länk till svar|Ange följande text som svar:<br>*[Skapa](https://account.microsoft.com/) ett Windows-konto med ett nytt eller befintligt e-postkonto*.<br>När du sparar och tränar databasen kommer den här texten att konverteras. |
    |||

    ![Skapa en ny fråga och svara på frågor](../media/conversational-context/create-child-prompt-from-parent.png)


1. Välj **Skapa ny**och välj sedan **Spara**. 

    Den här åtgärden skapar ett nytt par för frågor och svar och länkar den valda frågan som en uppföljnings fråga. I kolumnen **context** visas en uppföljnings relation för båda frågorna. 

1. Välj **visnings alternativ**och välj sedan [**Visa kontext (för hands version)** ](#show-questions-and-answers-with-context).

    Den nya frågan visar hur den är länkad.

    ![Skapa en ny uppföljnings prompt](../media/conversational-context/new-qna-follow-up-prompt.png)

    Den överordnade frågan visar en ny fråga som ett alternativ.

    ![! [Kontext kolumnen för båda frågorna indikerar en uppföljnings relation] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. När du har lagt till uppföljnings anvisningarna väljer du **Spara och träna** i det övre navigerings fältet.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Aktivera multi-turn vid testning av uppföljnings-prompter

När du testar frågan med uppföljnings anvisningarna i **test** fönstret väljer du **aktivera flera varv**och anger sedan din fråga. Svaret innehåller uppföljnings anvisningarna.

![Svaret innehåller uppföljnings anvisningarna](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Om du inte aktiverar flera varv returneras svaret men uppföljnings anvisningarna returneras inte.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>En JSON-begäran för att returnera ett första svar och Uppföljnings frågor

Använd det tomma `context` objektet för att begära svar på användarens fråga och inkludera uppföljnings frågor. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Ett JSON-svar för att returnera ett första svar och Uppföljnings frågor

Föregående avsnitt begärde ett svar och eventuella uppföljnings frågor till **konton och inloggning**. Svaret innehåller prompt-information som finns i *svar [0]. context*och texten som ska visas för användaren. 

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

Matrisen innehåller text `displayText` i egenskapen och `qnaId` värdet. `prompts` Du kan visa svaren som nästa alternativ i konversations flödet och sedan skicka `qnaId` tillbaka tillbaka till QNA Maker i följande begäran. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>En JSON-begäran om att returnera ett svar som inte är första gången och Uppföljnings frågor

Fyll i `context` objektet så att föregående kontext tas med.

I följande JSON-begäran använder den aktuella frågan *Windows Hello för att logga in* och föregående fråga var *konton och loggade in*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Ett JSON-svar för att returnera ett icke-initialt svar och Uppföljnings frågor

_GenerateAnswer_ JSON-svaret innehåller följande prompter i `context` egenskapen för det `answers` första objektet i objektet: QNA Maker

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Fråga kunskaps basen med QnA Maker-ID

Vid den första frågans svar returneras eventuella uppföljnings frågor och dess associerade `qnaId` . Nu när du har ID: t kan du skicka detta i begär ande texten för uppföljnings frågan. Om texten i begäran innehåller `qnaId`, och objektet context (som innehåller föregående QNA Maker egenskaper), returnerar GenerateAnswer den exakta frågan efter ID, i stället för att använda rangordnings algoritmen för att hitta svaret från frågetexten. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Visa prompter och skicka kontext i klient programmet 

Du har lagt till prompter i din kunskaps bas och testat flödet i test fönstret. Nu måste du använda dessa meddelanden i klient programmet. I bot Framework visas inte prompterna automatiskt i klient programmen. Du kan visa prompterna som föreslagna åtgärder eller knappar som en del av svaret på användarens fråga i klient program genom att inkludera detta [robot Framework-exempel](https://aka.ms/qnamakermultiturnsample) i koden. Klient programmet måste lagra det aktuella QnA Maker-ID: t och användar frågan och skicka dem i [kontext objekt för GenerateAnswer-API: et](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) för nästa användar fråga. 

## <a name="display-order-is-supported-in-the-update-api"></a>Visnings ordningen stöds i uppdaterings-API: et

[Visnings texten och visnings ordningen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), som returneras i JSON-svaret, stöds för redigering av uppdaterings- [API: et](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Skapa kunskaps bas med flera turn-prompter med Create API

Du kan skapa ett kunskaps ärende med flera turn-prompter med hjälp av [QNA Maker skapa API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Prompterna läggs till i `context` `prompts` egenskaps mat ris. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Lägg till eller ta bort prompter med uppdaterings-API: et

Du kan lägga till eller ta bort prompter med flera turn-meddelanden med hjälp av [API: et QNA Maker Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Prompterna läggs till i `context` `promptsToAdd` egenskaps mat ris och `promptsToDelete` matrisen. 


## <a name="next-steps"></a>Nästa steg

Lär dig mer om sammanhangsbaserade konversationer från det här [dialog exemplet](https://aka.ms/qnamakermultiturnsample) eller Lär dig mer om [konceptuella robot design för flera-turn-konversationer](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrera en kunskaps bas](../Tutorials/migrate-knowledge-base.md)
