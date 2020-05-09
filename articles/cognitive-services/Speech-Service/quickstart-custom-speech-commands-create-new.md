---
title: 'Snabb start: skapa ett anpassat kommando (för hands version) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar och testar du ett program med en värd för anpassade kommandon.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872519"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Snabb start: skapa en anpassad kommando-app (för hands version)

I den här snabb starten får du lära dig hur du skapar och testar ett program för anpassade kommandon.
Det skapade programmet bearbetar yttranden som "slå på TV" och svarar med ett enkelt meddelande "OK och aktiverar TV".

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure tal-resurs<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > För tillfället stöder anpassade kommandon bara tal prenumerationer i regionerna väst, westus2 och neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Gå till tal Studio för anpassade kommandon

1. Öppna webbläsaren och gå till [tal Studio](https://speech.microsoft.com/)
1. Ange dina autentiseringsuppgifter och logga in på portalen.

   - Standardvyn är listan med tal prenumerationer.
     > [!NOTE]
     > Om du inte ser sidan Välj prenumeration kan du gå dit genom att välja "tal resurser" på menyn Inställningar i det översta fältet.

1. Välj din tal prenumeration och välj sedan **gå till Studio**.
1. Välj **anpassade kommandon**.

     - Standardvyn är en lista över de anpassade kommandon som du har i den valda prenumerationen.

## <a name="create-a-custom-commands-project"></a>Skapa ett anpassat kommandon-projekt

1. Välj **nytt projekt** för att skapa ett nytt projekt.

   > [!div class="mx-imgBorder"]
   > ![Skapa ett projekt](media/custom-speech-commands/create-new-project.png)

1. Ange projekt namnet.
1. Välj språk i list rutan.
1. Välj en redigerings resurs från List rutan. Om det inte finns några giltiga redigerings resurser skapar du en genom att klicka på **Skapa ny Luis Authoring-resurs**.

   > [!div class="mx-imgBorder"]
   > ![Skapa en resurs](media/custom-speech-commands/create-new-resource.png)

   - Ange resurs namnet, resurs gruppen.
   - Välj värde för plats och pris nivå i list rutan.

      > [!NOTE]
      > Du kan skapa resurs grupper genom att ange önskat resurs grupp namn i fältet resurs grupp. Resurs gruppen skapas när du väljer **skapa** .

1. Välj sedan **skapa** för att skapa projektet.
1. När du har skapat väljer du ditt projekt.

    - Din vy bör nu vara en översikt över ditt nyligen skapade program för anpassade kommandon.

## <a name="update-luis-resources-optional"></a>Uppdatera LUIS-resurser (valfritt)

Du kan uppdatera den redigerings resurs som angavs i fönstret nytt projekt och ange en förutsägelse resurs. Förutsägelse resurs används för igenkänning när ditt program för anpassade kommandon publiceras. Du behöver ingen förutsägelse resurs för utvecklings-och test faserna.

1. Välj **Inställningar** i den vänstra rutan och gå sedan till avsnittet **Luis-resurser** i den mellersta rutan.
1. Välj en förutsägelse resurs eller skapa en genom att välja **Skapa ny resurs**.
1. Välj **Spara**.
    
    > [!div class="mx-imgBorder"]
    > ![Ange LUIS-resurser](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Eftersom redigerings resursen endast stöder 1 000 förutsägelse slut punkts begär Anden per månad måste du mandatorily ange en LUIS förutsägelse resurs innan du publicerar dina anpassade kommandon.


## <a name="create-a-new-command"></a>Skapa ett nytt kommando

Nu ska vi skapa ett enkelt kommando som tar en enda uttryck, `turn on the tv`och svarar på meddelandet `Ok, turning on the tv`.

1. Skapa ett nytt kommando genom att välja `+ New command` ikonen som finns överst i det vänstra fönstret. Ett nytt popup-kommando visas med namnet **New**.
1. Ange ett värde för fältet **namn** som `TurnOn`.
1. Välj **Skapa**.

I den mellersta rutan visas de olika egenskaperna för ett kommando:


| Konfiguration            | Beskrivning                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Exempel på meningar | Exempel yttranden användaren kan säga att utlösa det här kommandot                                                                 |
| Parametrar       | Information som krävs för att slutföra kommandot                                                                                |
| Regler för slut för ande | De åtgärder som ska vidtas för att utföra kommandot. Till exempel för att svara på användaren eller kommunicera med en annan webb tjänst. |
| Interaktions regler   | Ytterligare regler för att hantera mer detaljerade eller komplexa situationer                                                              |


> [!div class="mx-imgBorder"]
> ![Skapa ett kommando](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Lägg till exempel meningar

Vi börjar med exempel menings avsnittet och ger ett exempel på vad användaren kan säga.

1. Välj avsnittet **exempel meningar** i mitten av fönstret och i det högra fönstret, Lägg till exempel:

    ```
    turn on the tv
    ```

1. Välj `Save` ikonen som finns överst i det här fönstret.

För närvarande har vi inga parametrar så att vi kan gå vidare till **reglerna för slut för ande av regler** .

### <a name="add-a-completion-rule"></a>Lägg till en regel för slut för ande

Lägg nu till en regel för slut för ande av följande konfiguration. Den här regeln anger användaren att en uppfyllelse åtgärd utförs.


| Inställningen    | Föreslaget värde                          | Beskrivning                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Regelnamn  | ConfirmationResponse                  | Ett namn som beskriver syftet med regeln          |
| Villkor | Inga                                     | Villkor som avgör när regeln kan köras    |
| Åtgärder    | SpeechResponse "– OK, aktivera TV" | Den åtgärd som ska vidtas när regel villkoret är sant |

1. Skapa en ny regel för slut för ande `+Add` genom att välja ikonen överst i den mellersta rutan.
1. Ange värde i avsnittet **Name** .
1. Lägga till en åtgärd
   1. Skapa en ny åtgärd genom att markera kryss rutan **+ Lägg till en åtgärd** i avsnittet **åtgärder** .
   1. I popup-fönstret **ny åtgärd** väljer `Send speech response` du bland de nedrullningsbara alternativen för **typ**.
   1. Välj `Simple editor` för fältet **svar** .
       - I det **första Variations** fältet anger du ett värde för svar som`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Skapa ett tal svar](media/custom-speech-commands/create-speech-response-action.png)

1. Spara regeln genom att klicka på **Spara** .
1. Gå tillbaka till avsnittet **regler för slut för ande** och välj **Spara** för att spara alla ändringar. 

> [!div class="mx-imgBorder"]
> ![Skapa en regel för slut för ande](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Prova nu

Testa beteendet med hjälp av panelen test chatt
1. Välj `Train` ikonen som finns överst i den högra rutan.
1. När inlärningen är klar väljer `Test`du. Ett nytt **test av ditt program** -fönster visas.
    - Du skriver: Aktivera TV: n
    - Förväntat svar: OK, aktivera TV


> [!div class="mx-imgBorder"]
> ![Testa med Web Chat](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)
