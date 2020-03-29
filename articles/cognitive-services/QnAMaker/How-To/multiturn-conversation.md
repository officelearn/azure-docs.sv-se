---
title: Samtal med flera svänger - QnA Maker
description: Använd uppmaningar och kontext för att hantera flera varv, så kallade multi-turn, för din bot från en fråga till en annan. Multi-turn är förmågan att ha en fram och tillbaka konversation där den föregående frågan sammanhang påverkar nästa fråga och svar.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: abdde09fbb1f6b066772366c5cea933824cb5864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220626"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Använd uppföljande frågor för att skapa en längre konversation

Använd uppföljningsan följande och kontext för att hantera flera varv, så kallade _multi-turn_, för din bot från en fråga till en annan.

Om du vill se hur flera varv fungerar kan du visa följande demonstrationsvideo:

[![Konversation med flera svänger i QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Vad är en konversation med flera svängar?

Vissa frågor kan inte besvaras i en enda sväng. När du utformar konversationer för klientprogram (chattrobot) kan en användare ställa en fråga som måste filtreras eller förfinas för att avgöra rätt svar. Du gör detta flöde genom frågorna möjligt genom att presentera användaren med *uppföljningsan följande.*

När en användare ställer en fråga returnerar QnA Maker svaret _och_ eventuella uppföljningsansaner. Med det här svaret kan du presentera följdfrågorna som val.

> [!CAUTION]
> Flersvängningar extraheras inte från vanliga frågor och svar. Om du behöver extrahering med flera svängar tar du bort de frågetecken som anger QnA-paren som vanliga frågor.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exempel på flersvängningskonversation med chattrobot

Med flera svängar hanterar en chattrobot en konversation med en användare för att avgöra det slutliga svaret, som visas i följande bild:

![En dialogruta med flera varv med uppmaningar som vägleder en användare genom en konversation](../media/conversational-context/conversation-in-bot.png)

I föregående bild har en användare startat en konversation genom att ange **Mitt konto**. Kunskapsbasen har tre sammanlänkade fråge- och svarspar. För att förfina svaret väljer användaren ett av de tre alternativen i kunskapsbasen. Frågan (#1), har tre uppföljningsan följande, som presenteras i chatten bot som tre alternativ (#2).

När användaren väljer ett alternativ (#3) visas nästa lista över raffineringsalternativ (#4). Den här sekvensen fortsätter (#5) tills användaren bestämmer det korrekta, slutliga svaret (#6).


### <a name="use-multi-turn-in-a-bot"></a>Använd flera svängar i en bot

När du har publicerat din KB kan du välja knappen **Skapa bot** för att distribuera din QnA Maker-bot till Azure bot-tjänsten. Uppmaningarna visas i chatten klienter som du har aktiverat för din bot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Skapa en konversation med flera varv från ett dokuments struktur

När du skapar en kunskapsbas visas kryssrutan **Aktivera extrahering med flera svängar från webbadresser, PDF- eller .docx-filer** i avsnittet **Fyll i KB.**

![Kryssruta för att aktivera extrahering med flera varv](../media/conversational-context/enable-multi-turn.png)

När du väljer det här alternativet extraherar QnA Maker hierarkin som finns i dokumentstrukturen. Hierarkin konverteras för att följa upp uppmaningar och roten i hierarkin fungerar som den överordnade QnA. I vissa dokument har roten i hierarkin inte innehåll som kan fungera som ett svar, du kan ange "Standards svarstext" som ska användas som ersättningssvarstext för att extrahera sådana hierarkier.

Struktur med flera svängar kan endast härledas från webbadresser, PDF-filer eller DOCX-filer. Ett exempel på struktur kan du visa en bild av en [ANVÄNDARDOKUMENTÄR FÖR Microsoft Surface.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)

![! [Exempel på struktur i en bruksanvisning] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Bygga ditt eget multi-turn-dokument

Om du skapar ett dokument med flera svängar bör du tänka på följande riktlinjer:

* Använd rubriker och underrubriker för att beteckna hierarki. Du kan till exempel H1 ange den överordnade QnA och h2 för att beteckna QnA som ska tas som prompt. Använd liten rubrikstorlek för att ange efterföljande hierarki. Använd inte stil, färg eller någon annan mekanism för att antyda struktur i dokumentet, QnA Maker kommer inte att extrahera flera varvsansningar.

* Det första tecknet i rubriken måste aktiveras.

* Avsluta inte en rubrik med `?`ett frågetecken.

* Du kan använda [exempeldokumentet](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) som ett exempel för att skapa ett eget dokument med flera varv.

### <a name="adding-files-to-a-multi-turn-kb"></a>Lägga till filer i en KB med flera varv

När du lägger till ett hierarkiskt dokument bestämmer QnA Maker uppföljningsan följande från strukturen för att skapa konversationsflöde.

1. I QnA Maker väljer du en befintlig kunskapsbas som skapades med **Aktivera extrahering med flera svängar från URL:er, PDF- eller .docx-filer.** Aktiverat.
1. Gå till sidan **Inställningar** och välj den fil eller webbadress som ska läggas till.
1. **Spara och träna** kunskapsbasen.

> [!Caution]
> Stöd för att använda en exporterad TSV- eller XLS-kunskapsbasfil med flera varv som en datakälla för en ny eller tom kunskapsbas stöds inte. Du måste **importera** den filtypen från sidan **Inställningar** i QnA Maker-portalen för att lägga till exporterade flersvängsfrågor i en kunskapsbas.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Skapa kunskapsbas med flersvängsuppmaningar med Api:et Skapa

Du kan skapa ett kunskapsfall med flersnäckningsmeddelanden med hjälp av [API:et](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)för skapare för QnA Maker . Uppmaningarna läggs till `context` i egenskapens `prompts` matris.

## <a name="show-questions-and-answers-with-context"></a>Visa frågor och svar med sammanhang

Minska de visade fråge- och svarsparen till endast de med kontextuella konversationer.

Välj **Visa alternativ**och välj sedan Visa **kontext**. Listan visar fråge- och svarspar som innehåller uppföljningsanvisningar.

![Filtrera fråge- och svarspar efter kontextuella konversationer](../media/conversational-context/filter-question-and-answers-by-context.png)

Kontexten för flera varv visas i den första kolumnen.

![! [Kolumnen "Kontext (FÖRHANDSVERSION)" ] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

I föregående bild anger **#1** fet text i kolumnen, vilket betyder den aktuella frågan. Den överordnade frågan är det översta objektet på raden. Alla frågor under det är de länkade fråga-och-svar par. Dessa objekt är valbara, så att du omedelbart kan gå till de andra kontextobjekten.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Lägga till ett befintligt fråge- och svarspar som en uppföljningsprompt

Den ursprungliga frågan, **Mitt konto,** har uppföljningsanspråk, till exempel **Konton och inloggning**.

![Svaren och uppföljningsmeddelandena "Konton och inloggning"](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Lägg till en uppföljningsfråga i ett befintligt fråge- och svarspar som för närvarande inte är länkat. Eftersom frågan inte är länkad till något fråge- och svarspar måste den aktuella vyinställningen ändras.

1. Om du vill länka ett befintligt fråge- och svarspar som en uppföljningsprompt markerar du raden för fråge- och svarsparet. Sök efter **Logga ut** för att minska listan i Surface-handboken.
1. Välj **Lägg till uppföljningsprompt**i kolumnen **Svar** på raden för **Signering**.
1. I fälten i **popup-fönstret Uppföljningsfråga** anger du följande värden:

    |Field|Värde|
    |--|--|
    |Visningstext|Ange **Stäng av enheten**. Det här är anpassad text som ska visas i uppföljningsprompten.|
    |Endast kontext| Markera den här kryssrutan. Ett svar returneras endast om frågan anger kontext.|
    |Länk för att svara|Ange **Använd inloggningsskärmen** för att hitta det befintliga fråge- och svarsparet.|


1.  En match är tillbaka. Välj det här svaret som uppföljning och välj sedan **Spara**.

    ![Sidan "Uppföljningsfråga (PREVIEW)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. När du har lagt till uppföljningsprompten väljer du **Spara och tränar** i den övre navigeringen.

### <a name="edit-the-display-text"></a>Redigera visningstexten

När en uppföljningsfråga skapas och ett befintligt fråge- och svarspar anges som **länk till svar**kan du skriva in ny **visningstext**. Den här texten ersätter inte den befintliga frågan och lägger inte till en ny alternativ fråga. Det är skilt från dessa värden.

1. Om du vill redigera visningstexten söker du efter och markerar frågan i fältet **Sammanhang.**
1. På raden för den frågan väljer du uppföljningsprompten i svarskolumnen.
1. Markera den visningstext som du vill redigera och välj sedan **Redigera**.

    ![Kommandot Redigera för visningstexten](../media/conversational-context/edit-existing-display-text.png)

1. Ändra **Follow-up prompt** den befintliga visningstexten i popup-fönstret Uppföljningsuppdaning.
1. När du är klar med redigeringen av visningstexten väljer du **Spara**.
1. Spara **och träna**i det övre navigeringsfältet .


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Lägga till ett nytt fråge- och svarspar som en uppföljningsprompt

När du lägger till ett nytt fråge- och svarspar i kunskapsbasen bör varje par länkas till en befintlig fråga som en uppföljningsprompt.

1. Sök efter och välj det befintliga fråge- och svarsparet för **konton och logga in i**verktygsfältet Kunskapsbas.

1. I kolumnen **Svara** för den här frågan väljer du **Lägg till uppföljningsprompt**.
1. Under **Uppföljningsprompt (PREVIEW)** skapar du en ny uppföljningsfråga genom att ange följande värden:

    |Field|Värde|
    |--|--|
    |Visningstext|*Skapa ett Windows-konto*. Den anpassade text som ska visas i uppföljningsprompten.|
    |Endast kontext|Markera den här kryssrutan. Det här svaret returneras endast om frågan anger kontext.|
    |Länk för att svara|Ange följande text som svar:<br>* [Skapa](https://account.microsoft.com/) ett Windows-konto med ett nytt eller befintligt e-postkonto*.<br>När du sparar och tränar databasen konverteras den här texten. |
    |||

    ![Skapa en ny fråga och svar](../media/conversational-context/create-child-prompt-from-parent.png)


1. Välj **Skapa ny**och välj sedan **Spara**.

    Den här åtgärden skapar ett nytt fråge- och svarspar och länkar den valda frågan som en uppföljningsprompt. Kolumnen **Kontext,** för båda frågorna, anger en uppföljande snabb relation.

1. Välj **Visa alternativ**och välj sedan Visa [**kontext (PREVIEW)**](#show-questions-and-answers-with-context).

    Den nya frågan visar hur det är kopplat.

    ![Skapa en ny uppföljningsfråga](../media/conversational-context/new-qna-follow-up-prompt.png)

    Den överordnade frågan visar en ny fråga som ett av dess val.

    ![! [Kolumnen Sammanhang, för båda frågorna, anger en uppföljningsrelation] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. När du har lagt till uppföljningsprompten väljer du **Spara och tränar** i det övre navigeringsfältet.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Aktivera multi-turn under testning av uppföljningsansningar

När du testar frågan med uppföljningsansanningar i **testfönstret** väljer du **Aktivera multi-turn**och anger sedan din fråga. Svaret innehåller uppföljningsansanserna.

![Svaret omfattar uppföljningsan följande](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Om du inte aktiverar multi-turn returneras svaret men uppföljningsansningar returneras inte.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>En JSON-begäran om att returnera ett första svar och uppföljningsansaner

Använd det `context` tomma objektet för att begära svaret på användarens fråga och inkludera uppföljningsansans.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Ett JSON-svar för att returnera ett första svar och uppföljningsansningar

I föregående avsnitt begärdes ett svar och eventuella uppföljningsan följande till **konton och inloggning**. Svaret innehåller snabbinformationen, som finns vid *svar[0].kontext*, och texten som ska visas för användaren.

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

Matrisen `prompts` tillhandahåller text `displayText` i `qnaId` egenskapen och värdet. Du kan visa dessa svar som nästa visade val i `qnaId` konversationsflödet och sedan skicka tillbaka det valda till QnA Maker i följande begäran.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>En JSON-begäran om att returnera en icke-inledande svars- och uppföljningsans

Fyll `context` i objektet så att det innehåller föregående kontext.

I följande JSON-begäran är den aktuella frågan *Använd Windows Hello för att logga in* och föregående fråga var konton och logga *in*.

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Ett JSON-svar för att returnera ett icke-inledande svar och uppföljningsansans

QnA Maker _GenerateAnswer_ JSON-svaret innehåller uppföljningsan `context` följande i egenskapen `answers` för det första objektet i objektet:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Fråga kunskapsbasen med QnA Maker ID

Om du skapar ett anpassat program med funktionen för flera varv. I den första frågans svar returneras eventuella `qnaId` uppföljningsan följande och tillhörande. Nu när du har ID kan du vidarebefordra detta i uppföljningspromptens begärandetext. Om förfrågningstexten `qnaId`innehåller , och kontextobjektet (som innehåller de tidigare QnA Maker-egenskaperna) returnerar GenerateAnswer den exakta frågan efter ID, i stället för att använda rangordningsalgoritmen för att hitta svaret med frågetexten.


## <a name="display-order-is-supported-in-the-update-api"></a>Visningsordning stöds i uppdaterings-API:et

[Visningstexten och visningsordningen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), som returneras i JSON-svaret, stöds för redigering av [uppdaterings-API: et](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Lägga till eller ta bort flersvängsuppmaningar med uppdaterings-API:et

Du kan lägga till eller ta bort flersvängningar med [QnA Maker Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Uppmaningarna läggs till `context` i egenskapens `promptsToAdd` `promptsToDelete` matris och i matrisen.

## <a name="export-knowledge-base-for-version-control"></a>Exportera kunskapsbas för versionskontroll

QnA Maker stöder versionskontroll genom att inkludera konversationssteg för flera varv i den exporterade filen.

## <a name="next-steps"></a>Nästa steg

Läs mer om kontextuella konversationer från det här [dialogrutan](https://aka.ms/qnamakermultiturnsample) eller läs mer om [konceptuell botdesign för konversationer med flera svängar](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)
