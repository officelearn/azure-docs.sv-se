---
title: 'Snabbstart: Skapa en ny app i LUIS-portalen'
description: I den här snabbstarten skapar du de grundläggande delarna av en app, avsikter och entiteter samt testar med exempelyttrande i LUIS-portalen.
ms.topic: quickstart
ms.date: 04/14/2020
ms.openlocfilehash: 2d601646c43c0f0d99dc6934cf1f1c960e0b0f79
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382572"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Snabbstart: Skapa en ny app i LUIS-portalen

I den här snabbstarten skapar du en ny app i LUIS-portalen. Skapa först de grundläggande delarna av en app, **avsikter**och **entiteter**. Testa sedan appen genom att tillhandahålla ett exempel på användarutsägelse i den interaktiva testpanelen för att få den förväntade avsikten.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Skapa en app

1. Välj **+ Ny app för konversation** i kontextverktygsfältet och välj sedan Ny app för **konversation**.

    > [!div class="mx-imgBorder"]
    > [![Skapa ny app i LUIS-portalen](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Konfigurera appen med följande inställningar i popup-fönstret och välj sedan **Klar**.

   |Inställningsnamn| Värde | Syfte|
   |--|--|--|
   |Namn|`myEnglishApp`|Unikt LUIS-appnamn<br>krävs|
   |Kultur|**engelska**|Språk yttranden från användare, **en-us**<br>krävs|
   |Beskrivning (valfritt)|`App made with LUIS Portal`|Beskrivning av appen<br>valfri|
   |Prediktionsresurs (valfritt) |-  |Välj inte. LUIS ger dig en startnyckel att använda gratis för redigering och 1000 begäranden om förutsägelseslutpunkt. |

   ![Ange nya appinställningar](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Skapa avsikter

När LUIS-appen har skapats måste du skapa avsikter. Avsikter är ett sätt att kategorisera text från användare. En personalapp kan till exempel ha två funktioner. Så här hjälper du människor:

 1. Hitta och sök jobb
 1. Hitta formulär som ska sökas jobb

Appens två olika _avsikter anpassar_ sig till följande avsikter:

|Avsikt|Exempel på text från användare<br>kallas _yttrande_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|Sökformulär|`Where is the job transfer form hrf-123456?`|

Så här skapar du avsikter:

1. När appen har **skapats** finns du på sidan Avsikter i avsnittet **Skapa.** Välj **Skapa**.

   [![Välj Skapa för att skapa ny avsikt](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Ange avsiktsnamnet `FindForm`och välj sedan **Klar**.

## <a name="add-an-example-utterance"></a>Lägga till ett exempel yttrande

Du lägger till exempelyttranden när du har skapat avsikter. Exempel yttranden är text som en användare anger i en chatt bot eller annat klientprogram. De mappar avsikten med användarens text till en LUIS-avsikt.

I det här `FindForm` exemplet innehåller exempelyttranden formulärnumret. Klientprogrammet behöver formulärnumret för att uppfylla användarens begäran, så det är viktigt att inkludera det i uttrycket.

> [!div class="mx-imgBorder"]
> [![Ange exempelyttranden för avsikten Sökformulär](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Lägg till följande 15 exempel `FindForm` yttranden till avsikten.

|#|Exempel på yttranden|
|--|--|
|1|Söker hrf-123456|
|2|Var finns personalavdelningen hrf-234591?|
|3|hrf-345623, var är det|
|4|Är det möjligt att skicka mig hrf-345794|
|5|Behöver jag hrf-234695 för att söka ett internt jobb?|
|6|Behöver min chef veta att jag söker jobb hos HRF-234091|
|7|Var skickar jag hrf-234918? Får jag ett e-postsvar som det togs emot?|
|8|hrf-234555|
|9|När uppdaterades hrf-234987?|
|10|Använder jag blankett hrf-876345 för att söka ingenjörstjänster|
|11|Var en ny version av HRF-765234 in för min öppna req?|
|12|Använder jag hrf-234234 för internationella jobb?|
|13|hrf-234598 stavfel|
|14|hrf-234567 redigeras för nya krav|
|15|hrf-123456, hrf-123123, hrf-234567|

Efter design varierar de här exempelyttrandena på följande sätt:

* uttryckslängd
* [Skiljetecken](luis-reference-application-settings.md#punctuation-normalization)
* ordval
* verb tempus (är, var, kommer att vara)
* ordföljd


## <a name="create-a-regular-expression-entity"></a>Skapa entitet för reguljärt uttryck

Om du vill returnera formulärnumret i förutsägelsesvaret för körning måste formuläret markeras som en entitet. Eftersom formulärnummertexten är mycket strukturerad kan du markera den med ett reguljärt uttryck. Skapa entiteten med följande steg:

1. Välj **Entiteter** på menyn till vänster.

1. Välj **Skapa** på sidan **Entiteter.**

1. Ange namnet `Human Resources Form Number`, välj regexentitetstypen och välj sedan **Nästa**. **Regex**

   ![Skapa entitet för reguljärt uttryck](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Ange uttrycket **(RegEx)** `hrf-[0-9]{6}`i det reguljära uttrycket ( RegEx). Den här posten matchar `hrf-`de bokstavliga tecknen och tillåter exakt 6 siffror och väljer sedan **Skapa**.

   ![Ange reguljärt uttryck för entitet](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen

**Avsikten Ingen** är reservavsikten och bör inte lämnas tom. Den här avsikten bör innehålla ett uttryck för varje 10 exempel yttranden som du har lagt till för andra avsikter i appen.

**Ingen** avsiktens exempel yttranden bör vara utanför din klientprogramdomän.

1. Välj **Avsikter** på den vänstra menyn och välj sedan **Ingen** i avsiktslistan.

1. Lägg till följande exempelyttranden i avsikt:

   |Inga avsiktsexempel yttranden|
   |--|
   |Skällande hundar är irriterande|
   |Beställ en pizza åt mig|
   |Pingviner i havet|

   För den här appen ligger dessa exempelyttranden utanför domänen. Om domänen innehåller djur, mat eller havet bör du använda olika exempelyttranden för **avsikten Ingen.**

## <a name="train-the-app"></a>Träna appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Titta på entiteten för reguljära uttryck i exempelyttrandena

1. Kontrollera att entiteten finns i avsikten **Sökformulär** genom att välja **Avsikter** på den vänstra menyn. Välj sedan **FindForm-avsikt.**

   Entiteten markeras där den visas i exempelyttrandena. Om du vill visa den ursprungliga texten i stället för entitetsnamnet växlar du **entitetsvyn** från verktygsfältet.

   > [!div class="mx-imgBorder"]
   > [![Alla exempel yttranden markerade med entiteter](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testa din nya app med den interaktiva testfönstret

Använd det interaktiva **testfönstret** i LUIS-portalen för att verifiera att entiteten extraheras från nya yttranden som appen inte har sett ännu.

1. Välj **Testa** på menyn längst upp till höger.

1. Lägg till ett nytt uttryck och tryck sedan på Retur:

   ```Is there a form named hrf-234098```

    Välj **Granska** om du vill visa entitetsprognoser.

   > [!div class="mx-imgBorder"]
   > ![Testa nytt uttryck i testfönstret](./media/get-started-portal-build-app/test-new-utterance.png)

   Den översta förväntade avsikten är korrekt **FindForm** med över 90% förtroende (0,977). Entiteten **Personalformnummer** extraheras med värdet hrf-234098.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstarten och inte går vidare till nästa snabbstart väljer du **Mina appar** på den övre navigeringsmenyn. Markera sedan appens vänstra kryssruta i listan och välj **Ta bort** från kontextverktygsfältet ovanför listan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [2. Distribuera en app](get-started-portal-deploy-app.md)
