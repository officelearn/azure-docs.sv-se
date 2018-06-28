---
title: Självstudie om att skapa en LUIS-app för att extrahera data – Azure | Microsoft Docs
description: I den här självstudien skapar du en enkel LUIS-app med hjälp av avsikter och en enkel entitet för att extrahera maskininlärningsdata.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265368"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Självstudie: skapa app som använder enkel entitet
I den här självstudien skapar du en app som visar hur det går till att extrahera maskininlärningsdata från ett yttrande med hjälp av entiteten **Simple** (Enkel).

<!-- green checkmark -->
> [!div class="checklist"]
> * Förstå enkla entiteter 
> * Skapa ny LUIS-app för kommunikationsdomänen med avsikten SendMessage
> * Lägga till avsikten _None_ (Ingen) och lägga till exempelyttranden
> * Lägg till enkel entitet för att extrahera meddelandeinnehåll från yttrande
> * Träna och publicera app
> * Skicka en fråga till appens slutpunkt för att se LUIS JSON-svar

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto][LUIS] för att kunna redigera LUIS-programmet.

## <a name="purpose-of-the-app"></a>Syftet med appen
Den här appen visar hur det går till att hämta data från ett yttrande. Ta följande yttrande från en chattrobot som exempel:

```JSON
Send a message telling them to stop
```

Avsikten är att skicka ett meddelande. De data i yttrandet som är viktiga är meddelandet i sig, `telling them to stop`.  

## <a name="purpose-of-the-simple-entity"></a>Syftet med den enkla entiteten
Syftet med den enkla entiteten är att lära LUIS vad ett meddelande är och var det kan hittas i ett yttrande. Den del av yttrandet som utgör meddelandet kan variera från yttrande till yttrande baserat på ordval och yttrandets längd. LUIS behöver exempel på meddelanden i yttranden över alla avsikter.  

För den här enkla appen kommer meddelandet i slutet av yttrandet. 

## <a name="create-a-new-app"></a>Skapa en ny app
1. Logga in på [LUIS-webbplatsen][LUIS]. Se till att logga in på den region där du behöver få LUIS-slutpunkterna publicerade.

2. På [LUIS-webbplatsen][LUIS] väljer du **Create new app** (Skapa ny app).  

    ![Lista över LUIS-appar](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. I popup-dialogrutan anger du namnet `MyCommunicator`. 

    ![Lista över LUIS-appar](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. När processen är klar visar appen sidan **Intents** (Avsikter) med avsikten **None** (Ingen). 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "Skärmbild på sidan LUIS Intents (LUIS-avsikter) med avsikten None (Ingen)")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>Skapa en ny avsikt

1. På sidan **Intents** (Avsikter) väljer du **Create new intent** (Skapa ny avsikt). 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "Skärmbild på LUIS med knappen ”Create new intent” (Skapa ny avsikt) markerad")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. Ange det nya avsiktsnamnet `SendMessage`. Den här avsikten ska väljas när en användare vill skicka ett meddelande.

    Genom att skapa en avsikt skapar du den primära kategorin för information som du vill identifiera. Tack vare att kategorin får ett namn kan andra program som använder LUIS-frågeresultaten använda det kategorinamnet för att hitta ett lämpligt svar eller utföra lämpliga åtgärder. LUIS svarar inte på de här frågorna, utan identifierar bara vilken typ av information som det frågas om i naturligt språk. 

    ![Ange avsiktsnamnet SendMessage](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. Lägg till sju yttranden till avsikten `SendMessage` som du förväntar dig att en användare begär, till exempel:

    | Exempel på yttranden|
    |--|
    |Svara med Jag har fått ditt meddelandet och svarar i morgon|
    |Skicka meddelande När är du hemma?|
    |Skicka SMS Jag är upptagen|
    |Berätta att det måste göras i dag|
    |Skicka snabbmeddelande att jag kör bil och svarar senare|
    |Skapa meddelande till David där det står När var det?|
    |säg glenn hej|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "Skärmbild på LUIS med angivna yttranden")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Lägga till yttranden till avsikten None (Ingen)

LUIS-appen har för närvarande inga yttranden för avsikten **None** (Ingen). Den behöver yttranden som du inte vill att appen svarar på. Därför behöver den ha yttranden i avsikten **None** (Ingen). Lämna den inte tom. 
    
1. Välj **Intents** (Avsikter) på den vänstra panelen. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "Skärmbild på LUIS med knappen ”Intents” (Avsikter) markerad")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. Välj avsikten **None** (Ingen). 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "Skärmbild på avsikten None (Ingen) som väljs ")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. Lägg till tre yttranden som din använda kan tänkas ange men som inte är relevanta för appen. Några bra **None**-yttranden (Inget) är:

    | Exempel på yttranden|
    |--|
    |Avbryt!|
    |Hej då|
    |Vad är det som händer?|
    
    I det LUIS-anropande programmet, till exempel en chattrobot, kan roboten om LUIS returnerar avsikten **None** (Ingen) för ett yttrande fråga om användaren vill avsluta konversationen. Roboten kan även ge fler anvisningar för att fortsätta konversationen om användaren inte vill avsluta den. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "Skärmbild på LUIS med yttranden för avsikten None (Ingen)")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>Skapa en enkel entitet för att extrahera meddelande 
1. Välj **Intents** (Avsikter) på den vänstra menyn.

    ![Välj länken Intents (Avsikter)](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. Välj `SendMessage` från listan över avsikter.

    ![Välj avsikten SendMessage](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. I yttrandet `Reply with I got your message, I will have the answer tomorrow` markerar du det första ordet i meddelandetexten, `I`, och det sista ordet i meddelandetexten, `tomorrow`. Alla de här orden markeras för meddelandet, och en listrutemeny visas med textrutan längst upp.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "Skärmbild på markerade ord i yttrandet för meddelandet")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. Ange entitetsnamnet `Message` i textrutan.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "Skärmbild på entitetsnamn som anges i rutan")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. Välj **Create new entity** (Skapa ny entitet) i listrutemenyn. Syftet med entiteten är att hämta den text som utgör meddelandetexten. I den här LUIS-appen är textmeddelandet i slutet av yttrandet, men yttrandet och meddelandet kan ha vilken längd som helst. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "Skärmbild på ny entitet som skapas från yttrande")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. I popup-fönstret är standardentitetstypen **Simple** (Enkel), och entitetsnamnet är `Message`. Behåll de här inställningarna och välj **Done** (Klar).

    ![Verifiera entitetstyp](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. Nu när entiteten har skapats och ett yttrande har märkts ska du märka de återstående yttrandena med den entiteten. Välj ett yttrande och välj sedan det första och det sista ordet i ett meddelande. I listrutemenyn väljer du entiteten `Message`. Meddelandet är nu märkt i entiteten. Fortsätt att märka alla meddelandefraser i de återstående yttrandena.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "Skärmbild på alla meddelandeyttranden som är märkta")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    Standardvyn för yttrandena är **Entities view** (Entitetsvy). Välj kontrollen **Entities view** (Entitetsvy) ovanför yttrandena. **Tokens view** (Tokenvy) visar yttrandetexten. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "Skärmbild på yttranden i Tokens view (Tokenvy)")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>Träna LUIS-appen
LUIS känner inte till ändringarna av avsikterna och entiteterna (modellen) förrän den tränas. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Train** (Träna).

    ![Välj knappen train (träna)](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. Träningen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

    ![Meddelande om att träning är klar](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL
För att få en LUIS-förutsägelse i en chattrobot eller i ett annat program måste du publicera appen. 

1. Längst uppe till höger på LUIS-webbplatsen väljer du knappen **Publish** (Publicera). 

2. Välj platsen Production (Produktionsplats) och knappen **Publish** (Publicera).

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Skärmbild på sidan Publish (Publicera) med knappen Publish to production slot (Publicera till produktionsplats) markerad")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. Publiceringen är klar när du ser det gröna statusfältet som bekräftar att det är klart längst upp på webbplatsen.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande
På sidan **Publish** (Publicera) väljer du länken **endpoint** (slutpunkt) längst ned på sidan. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Skärmbild på sidan Publish (Publicera) med slutpunkt markerad")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Den här åtgärden öppnar ett nytt webbläsarfönster med slutpunkts-URL i adressfältet. Gå till slutet av URL:en i adressen och ange `text I'm driving and will be 30 minutes late to the meeting`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera yttrandena `SendMessage`.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Vad har den här LUIS-appen åstadkommit?
Med hjälp av endast två avsikter och en entitet har den här appen identifierat en frågeavsikt i naturligt språk och returnerat meddelandedata. 

JSON-resultatet identifierar avsikten `SendMessage` med högst poäng och ett resultat på 0,987501. Alla poäng är mellan 1 och 0, ju närmare 1 desto bättre. Poängen för avsikten `None` är 0,111048922, vilket är mycket närmare noll. 

Meddelandedata har en typ, `Message`, och ett värde, `i ' m driving and will be 30 minutes late to the meeting`. 

Din chattrobot har nu tillräckligt med information för att bestämma den primära åtgärden, `SendMessage`, och en parameter för åtgärden, texten i meddelandet. 

## <a name="where-is-this-luis-data-used"></a>Var används dessa LUIS-data? 
LUIS är klar med den här begäran. Det anropande programmet, till exempel en chattrobot, kan använda topScoringIntent-resultatet och data från entiteten för att skicka meddelandet via ett tredjeparts-API. Om det finns andra programmässiga alternativ för roboten eller det anropande programmet utför inte LUIS det arbetet. LUIS tar endast reda på vad användarens avsikt är. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. För att göra det väljer du menyn med tre punkter (...) till höger om appnamnet i applistan och väljer **Delete** (Ta bort). På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du lägger till en hierarkisk entitet](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
