---
title: 'Snabb start: skapa en ny app i LUIS-portalen'
description: I den här snabb starten skapar du grundläggande delar av en app, avsikter och entiteter samt test med exempel uttryck på LUIS-portalen.
ms.topic: quickstart
ms.date: 04/14/2020
ms.openlocfilehash: 2d601646c43c0f0d99dc6934cf1f1c960e0b0f79
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382572"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Snabb start: skapa en ny app i LUIS-portalen

I den här snabb starten skapar du en ny app i LUIS-portalen. Börja med att skapa grundläggande delar av en app, **avsikter**och **entiteter**. Testa sedan appen genom att tillhandahålla ett exempel på användarens uttryck i den interaktiva test panelen för att få den förväntade avsikten.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Skapa en app

1. Välj **+ ny app för konversation** i verktygsfältet kontext och välj sedan **ny app för konversation**.

    > [!div class="mx-imgBorder"]
    > [![Skapa en ny app i LUIS-portalen](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. I popup-fönstret konfigurerar du appen med följande inställningar och väljer sedan **Slutför**.

   |Inställningsnamn| Värde | Syfte|
   |--|--|--|
   |Name|`myEnglishApp`|Unikt namn på LUIS-app<br>krävs|
   |Culture (Kultur)|**engelska**|Språk för yttranden från användare, **en-US**<br>krävs|
   |Beskrivning (valfritt)|`App made with LUIS Portal`|Beskrivning av app<br>valfri|
   |Förutsägelse resurs (valfritt) |-  |Välj inte. LUIS ger dig en start nyckel som du kan använda utan kostnad för redigering och 1 000 förutsägelse slut punkts begär Anden. |

   ![Ange nya inställningar för app](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Skapa avsikter

När LUIS-appen har skapats måste du skapa avsikter. Avsikter är ett sätt att kategorisera text från användare. Till exempel kan en personal-app ha två funktioner. För att hjälpa personer:

 1. Sök efter och Ansök om jobb
 1. Sök efter formulär som ska användas för jobb

Appens två olika _avsikter_ anpassas till följande syften:

|Avsikt|Exempel text från användare<br>kallas en _uttryck_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Utför följande steg för att skapa avsikter:

1. När appen har skapats finns du på sidan **syften** i avsnittet **build** . Välj **Skapa**.

   [![Välj Skapa för att skapa ny avsikt](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Ange namnet `FindForm`på avsikten och välj sedan **färdig**.

## <a name="add-an-example-utterance"></a>Lägg till ett exempel uttryck

Du lägger till exempel yttranden när du har skapat avsikter. Exempel på yttranden är text som en användare anger i en chat-robot eller något annat klient program. De mappar avsikten med användarens text till ett LUISt syfte.

I det här exempel programmets `FindForm` syfte innehåller exempel yttranden formulär numret. Klient programmet behöver formulär numret för att uppfylla användarens begäran, så det är viktigt att inkludera det i uttryck.

> [!div class="mx-imgBorder"]
> [![Ange exempel yttranden för FindForm-avsikten](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Lägg till följande 15 exempel yttranden i `FindForm` avsikten.

|#|Exempel på yttranden|
|--|--|
|1|Söker efter HRF – 123456|
|2|Var är personal formuläret HRF-234591?|
|3|HRF – 345623, där är det|
|4|Är det möjligt att skicka mig HRF-345794|
|5|Behöver jag HRF-234695 för att ansöka om ett internt jobb?|
|6|Behöver min chef veta att jag använder för ett jobb med HRF-234091|
|7|Var skickar jag HRF-234918? Får jag ett e-postmeddelande om att det togs emot?|
|8|HRF – 234555|
|9|När var HRF-234987 uppdaterad?|
|10|Använder jag formatet HRF-876345 för att ansöka om tekniska platser|
|11|Har en ny version av HRF-765234 skickats för mina öppna REQ?|
|12|Använder jag HRF-234234 för internationella jobb?|
|13|HRF – 234598 stavnings fel|
|14|kommer HRF-234567 att redige ras för nya krav|
|15|HRF-123456, HRF-123123, HRF-234567|

Som design kan dessa exempel yttranden variera på följande sätt:

* uttryck längd
* [skiljetecken](luis-reference-application-settings.md#punctuation-normalization)
* Word-alternativ
* verb (är, är, är)
* ord ordning


## <a name="create-a-regular-expression-entity"></a>Skapa en entitet för reguljära uttryck

Om du vill returnera formulär numret i svars förutsägelsens svar måste formuläret markeras som en entitet. Eftersom formulär nummer texten är mycket strukturerad kan du markera den med en entitet för reguljära uttryck. Skapa entiteten med följande steg:

1. Välj **entiteter** från menyn till vänster.

1. Välj **skapa** på sidan **entiteter** .

1. Ange namnet `Human Resources Form Number`, Välj typ av **regex** -enhet och välj sedan **Nästa**.

   ![Skapa entitet för reguljära uttryck](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Ange uttrycket för reguljära uttryck (**regex**) `hrf-[0-9]{6}`,. Den här posten matchar litteral tecken `hrf-`, och tillåter exakt 6 siffror och välj sedan **skapa**.

   ![Ange reguljärt uttryck för entitet](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen

**Ingen** avsikt är återställnings avsikten och får inte vara tom. Avsikten bör innehålla en uttryck för varje 10-exempel yttranden som du har lagt till för andra avsikter av appen.

Yttranden för **inget** exempel bör ligga utanför din klient program domän.

1. Välj **avsikter** på menyn till vänster och välj sedan **ingen** från listan över försök.

1. Lägg till följande exempel yttranden till avsikten:

   |Ingen avsikts exempel yttranden|
   |--|
   |Skällande hundar är irriterande|
   |Beställ en pizza åt mig|
   |Pingviner i havet|

   I den här appen är dessa exempel yttranden utanför domänen. Om din domän innehåller djur, livsmedel eller oceanen bör du använda olika exempel-yttranden för **ingen** avsikt.

## <a name="train-the-app"></a>Träna appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Titta på entiteten reguljära uttryck i exemplet yttranden

1. Verifiera att entiteten finns i **FindForm** avsikt genom att välja **avsikter** på den vänstra menyn. Välj sedan **FindForm** avsikt.

   Entiteten är markerad där den visas i exemplet yttranden. Om du vill se den ursprungliga texten i stället för enhets namnet växlar du **vyn entiteter** från verktygsfältet.

   > [!div class="mx-imgBorder"]
   > [![Alla exempel yttranden markerade med entiteter](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testa din nya app med det interaktiva test fönstret

Använd det interaktiva **test** fönstret i Luis-portalen för att verifiera att entiteten extraheras från en ny yttranden att appen inte har setts än.

1. Välj **test** på menyn längst upp till höger.

1. Lägg till en ny uttryck och tryck sedan på RETUR:

   ```Is there a form named hrf-234098```

    Välj **Granska** för att se enhets förutsägelser.

   > [!div class="mx-imgBorder"]
   > ![Testa nya uttryck i test fönstret](./media/get-started-portal-build-app/test-new-utterance.png)

   Den mest förväntade avsikten är korrekt **FindForm** med över 90% konfidensgrad (0,977). Entiteten **personal formulär nummer** extraheras med värdet hrf-234098.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabb starten och inte fortsätter till nästa snabb start väljer du **Mina appar** på den översta navigerings menyn. Markera sedan appens vänstra kryss ruta i listan och välj **ta bort** från verktygsfältet kontext ovanför listan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [2. distribuera en app](get-started-portal-deploy-app.md)
