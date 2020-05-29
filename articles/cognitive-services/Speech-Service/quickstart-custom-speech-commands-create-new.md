---
title: 'Snabb start: skapa ett anpassat kommando förhandsgranska app-Speech service'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar och testar du ett värdbaserade anpassade kommandon för för hands versions program. Programmet kommer att bearbeta yttranden.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142358"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Snabb start: skapa en anpassad kommando förhands gransknings app

I den här snabb starten får du lära dig hur du skapar och testar ett program för anpassade kommandon.
Programmet bearbetar yttranden som "slå på TV" och svarar med ett enkelt meddelande som "OK" och aktiverar TV: n. "

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure tal-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > För tillfället stöder anpassade kommandon bara tal prenumerationer i regionerna väst, westus2 och neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Gå till tal Studio för anpassade kommandon

1. I en webbläsare går du till [tal Studio](https://speech.microsoft.com/).
1. Ange dina autentiseringsuppgifter och logga in på portalen.

   Standardvyn är listan med tal prenumerationer.
    > [!NOTE]
    > Om du inte ser sidan Välj prenumeration kan du gå dit genom att välja **tal resurser** på menyn Inställningar högst upp på skärmen.

1. Välj din tal prenumeration och välj sedan **gå till Studio**.
1. Välj **anpassade kommandon**.

     Standardvyn är en lista med program för anpassade kommandon som du har i den valda prenumerationen.

## <a name="create-a-custom-commands-project"></a>Skapa ett anpassat kommandon-projekt

1. Välj **nytt projekt** för att skapa ett projekt.

   > [!div class="mx-imgBorder"]
   > ![Skapa ett projekt](media/custom-speech-commands/create-new-project.png)

1. I rutan **namn** anger du ett projekt namn.
1. Välj ett språk i listan **språk** .
1. Välj en redigerings resurs i listan **Luis Authoring Resource** . Om det inte finns några giltiga redigerings resurser skapar du en genom att välja **Skapa ny Luis Authoring-resurs**.

   > [!div class="mx-imgBorder"]
   > ![Skapa en resurs](media/custom-speech-commands/create-new-resource.png)

   1. Ange namnet på resursen i rutan **resurs namn** .
   1. Välj en resurs grupp i listan **resurs grupp** .
   1. Välj en plats i listan **plats** .
   1. Välj en nivå i listan **pris nivå** .

      > [!NOTE]
      > Du kan skapa en resurs grupp genom att ange ett resurs grupp namn i rutan **resurs grupp** . Resurs gruppen skapas när du väljer **skapa**.

1. Välj **Skapa**.
1. När projektet har skapats väljer du det.

    Nu bör du se en översikt över ditt nya program för anpassade kommandon.

## <a name="update-luis-resources-optional"></a>Uppdatera LUIS-resurser (valfritt)

Du kan uppdatera den redigerings resurs som du har valt i fönstret **nytt projekt** och ange en förutsägelse resurs. Förutsägelse resursen används för igenkänning när programmet för anpassade kommandon publiceras. Du behöver ingen förutsägelse resurs under utvecklings-och test faserna.

1. Välj **Inställningar** i den vänstra rutan och välj sedan **Luis-resurser** i den mellersta rutan.
1. Välj en förutsägelse resurs eller skapa en genom att välja **Skapa ny resurs**.
1. Välj **Spara**.
    
    > [!div class="mx-imgBorder"]
    > ![Ange LUIS-resurser](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Eftersom redigerings resursen endast stöder 1 000 förutsägelse slut punkts begär Anden per månad måste du ange en LUIS förutsägelse resurs innan du publicerar dina anpassade kommandon-program.


## <a name="create-a-command"></a>Skapa ett kommando

Nu ska vi skapa ett enkelt kommando som tar en enda uttryck, `turn on the tv` och svarar på meddelandet `Ok, turning on the tv` .

1. Skapa ett kommando genom att välja **nytt kommando** längst upp i det vänstra fönstret. Det **nya kommando** fönstret öppnas.
1. I rutan **namn** anger du **TurnON**.
1. Välj **Skapa**.

I fönstret i mitten visas egenskaperna för kommandot:


| Konfiguration            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Exempel på meningar** | Exempel på yttranden som användaren kan säga för att utlösa kommandot.                                                                 |
| **Parametrar**       | Information som krävs för att slutföra kommandot.                                                                                |
| **Regler för slut för ande** | Åtgärder som ska vidtas för att utföra kommandot. Till exempel svara på användaren eller kommunicera med en annan webb tjänst. |
| **Interaktions regler**   | Ytterligare regler för att hantera mer detaljerade eller komplexa situationer.                                                              |


> [!div class="mx-imgBorder"]
> ![Skapa ett kommando](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Lägg till exempel meningar

Vi börjar med **exempel menings** avsnittet. Vi ger ett exempel på vad användaren kan säga.

1. Välj **exempel meningar** i mitten av fönstret. 
1. I den högra rutan lägger du till exempel:

    ```
    turn on the tv
    ```

1. Välj **Spara** längst upp i fönstret.

För tillfället har vi inga parametrar, så vi kan gå vidare till avsnittet **regler för slut för ande** .

### <a name="add-a-completion-rule"></a>Lägg till en regel för slut för ande

Lägg nu till en regel för slut för ande som har följande konfiguration. Den här regeln anger användaren att en uppfyllelse åtgärd utförs.


| Inställningen    | Föreslaget värde                          | Description                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Namn**  | **ConfirmationResponse**                  | Ett namn som beskriver syftet med regeln.          |
| **Villkor** | Inga                                     | Villkor som avgör när regeln kan köras.    |
| **Åtgärder**    | **Skicka tal svar – > OK, aktivera TV** | Den åtgärd som ska vidtas när regel villkoret är sant. |

1. Skapa en ny regel för slut för ande genom att välja **Lägg till** överst i den mellersta rutan.
1. Ange ett namn i rutan **Namn**.
1. Lägg till en åtgärd.
   1. Skapa en åtgärd genom att välja **Lägg till en åtgärd** i avsnittet **åtgärder** .
   1. I fönstret **Redigera åtgärd** väljer du **Skicka tal svar**i listan **typ** .
   1. Under **svar**väljer du **enkel redigerare**. I den **första variant** rutan, anger **du OK och aktiverar TV**.

   > [!div class="mx-imgBorder"]
   > ![Skapa ett svar](media/custom-speech-commands/create-speech-response-action.png)

1. Spara regeln genom att välja **Spara** .
1. Gå tillbaka till avsnittet **regler för slut för ande** och välj **Spara** för att spara alla ändringar. 

> [!div class="mx-imgBorder"]
> ![Skapa en regel för slut för ande](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Prova nu

Testa beteendet med hjälp av panelen testa chatt.
1. Välj **träna** överst i den högra rutan.
1. När utbildningen är färdig väljer du **test**. Ett nytt **test av ditt program** -fönster visas.
    - Ange **Aktivera TV**
    - Förväntat svar: **OK, aktivera TV**


> [!div class="mx-imgBorder"]
> ![Testa beteendet](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: skapa ett anpassat kommando för hands versions program med parametrar](./quickstart-custom-speech-commands-create-parameters.md)
