---
title: 'Instruktion: skapa program med enkla kommandon – tal service'
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du skapar och testar ett värdbaserade anpassade kommando program med hjälp av enkla kommandon.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d166257dd28773d89a4f1fd56de3cb1a22242523
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284174"
---
# <a name="create-application-with-simple-commands"></a>Skapa program med enkla kommandon

I den här artikeln kan du se hur du:
 - Skapa ett tomt program
 - Uppdatera LUIS-resurser
 - Lägg till några grundläggande kommandon i programmet för anpassade kommandon

## <a name="create-empty-application"></a>Skapa ett tomt program
Skapa ett tomt program för anpassade kommandon. Mer information finns i [snabb](quickstart-custom-commands-application.md)starten. Den här gången skapar du ett tomt projekt i stället för att importera ett projekt.

1. I rutan **namn** anger du projekt namnet som `Smart-Room-Lite` (eller något annat alternativ).
1. I listan **språk** väljer du **engelska (USA)**.
1. Välj eller skapa en valfri LUIS-resurs.

   > [!div class="mx-imgBorder"]
   > ![Skapa ett projekt](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>Uppdatera LUIS-resurser (valfritt)

Du kan uppdatera den redigerings resurs som du har valt i fönstret **nytt projekt** och ange en förutsägelse resurs. Förutsägelse resurs används för igenkänning när programmet för anpassade kommandon publiceras. Du behöver ingen förutsägelse resurs under utvecklings-och test faserna.

## <a name="add-turnon-command"></a>Lägg till TurnOn-kommando

I det tomma programmet för **Smart-Room-lite** anpassade kommandon som du nyss skapade, lägger du till ett enkelt kommando som bearbetar en uttryck, `turn on the tv` och svarar på meddelandet `Ok, turning the tv on` .

1. Skapa ett nytt kommando genom att välja **nytt kommando** längst upp i det vänstra fönstret. Det **nya kommando** fönstret öppnas.
1. Ange ett värde för fältet **namn** som **TurnON**.
1. Välj **Skapa**.

I fönstret i mitten visas olika egenskaper för kommandot. Du konfigurerar följande egenskaper för kommandot. Förklaringar av alla konfigurations egenskaper för ett kommando finns i [referenser](./custom-commands-references.md).

| Konfiguration            | Beskrivning                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Exempel på meningar** | Exempel yttranden användaren kan säga att utlösa det här kommandot                                                                 |
| **Parametrar**       | Information som krävs för att slutföra kommandot                                                                                |
| **Regler för slut för ande** | De åtgärder som ska vidtas för att utföra kommandot. Till exempel för att svara på användaren eller kommunicera med en annan webb tjänst. |
| **Interaktions regler**   | Ytterligare regler för att hantera mer detaljerade eller komplexa situationer                                                              |


> [!div class="mx-imgBorder"]
> ![Skapa ett kommando](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Lägg till exempel meningar

Vi börjar med **exempel menings** avsnittet och ger ett exempel på vad användaren kan säga.

1. Markera avsnittet **exempel meningar** i mitten av fönstret.
1. I det högra fönstret lägger du till exempel:

    ```
    turn on the tv
    ```

1.  Välj **Spara** längst upp i fönstret.

För tillfället har vi inga parametrar, så vi kan gå vidare till avsnittet **regler för slut för ande** .

### <a name="add-a-completion-rule"></a>Lägg till en regel för slut för ande

Sedan måste kommandot ha en regel för slut för ande. Den här regeln anger användaren att en uppfyllelse åtgärd utförs. Om du vill läsa mer om regler och regler för slut för ande går du till [referenser](./custom-commands-references.md).

1. Välj standard regel för slut för **ande slutförd** och redigera den på följande sätt:

    
    | Inställning    | Föreslaget värde                          | Beskrivning                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Namn**       | ConfirmationResponse                  | Ett namn som beskriver syftet med regeln          |
    | **Villkor** | Inget                                     | Villkor som avgör när regeln kan köras    |
    | **Åtgärder**    | Skicka tal svar > enkel redigerare > första variationen > `Ok, turning the tv on` | Den åtgärd som ska vidtas när regel villkoret är sant |
    



   > [!div class="mx-imgBorder"]
   > ![Skapa ett tal svar](media/custom-commands/create-speech-response-action.png)

1. Spara åtgärden genom att välja **Spara** .
1. Gå tillbaka till avsnittet **regler för slut för ande** och välj **Spara** för att spara alla ändringar. 


    > [!NOTE]
    > Du behöver inte använda standard regeln för slut för ande som medföljer kommandot. Om det behövs kan du ta bort den befintliga regeln för standard slut för ande och lägga till en egen regel.

### <a name="try-it-out"></a>Prova nu

Testa beteendet med hjälp av panelen test chatt
1. Välj **träna** ikon överst i den högra rutan.
1. När inlärningen är klar väljer du **test**. Prova följande uttryck via röst/text:
    - Inmatade: slå på TV
    - Utdata: OK, aktivera TV på


> [!div class="mx-imgBorder"]
> ![Testa med Web Chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> I test panelen kan du välja **Aktivera** information för information om hur den här rösten/text indatan bearbetades.  

## <a name="add-settemperature-command"></a>Lägg till SetTemperature-kommando

Lägg nu till ytterligare en kommando **SetTemperature** som tar en enda uttryck, `set the temperature to 40 degrees` och svara på meddelandet `Ok, setting temperature to 40 degrees` .

Följ stegen som visas för kommandot **TurnON** för att skapa ett nytt kommando med hjälp av exempel meningen "**Ställ in temperatur på 40 grader**".

Redigera sedan **de befintliga reglerna för färdig komplettering** enligt följande:

| Inställning    | Föreslaget värde                          |
| ---------- | ---------------------------------------- |
| Namn  | ConfirmationResponse                  |
| Villkor | Inget                                     |
| Åtgärder    | Skicka tal svar > enkel redigerare > första variationen > `Ok, setting temperature to 40 degrees` |

Välj **Spara** för att spara alla ändringar i kommandot.

## <a name="add-setalarm-command"></a>Lägg till SetAlarm-kommando
Skapa ett nytt kommando **SetAlarm** med hjälp av exempel meningen "**Ange ett larm för 9 am imorgon**". Redigera sedan **de befintliga reglerna för färdig komplettering** enligt följande:

| Inställning    | Föreslaget värde                          |
| ---------- | ---------------------------------------- |
| Regelnamn  | ConfirmationResponse                  |
| Villkor | Inget                                     |
| Åtgärder    | Skicka tal svar > enkel redigerare > första variationen >`Ok, setting an alarm for 9 am tomorrow` |

Välj **Spara** för att spara alla ändringar i kommandot.

## <a name="try-it-out"></a>Prova nu

Testa beteendet med hjälp av panelen test chatt
1. Välj **Träna** (Träna). När utbildningen är klar väljer du **testa** och provar:
    - Du skriver: Ange temperatur till 40 grader
    - Förväntat svar: OK, ställer in temperatur på 40 grader
    - Du skriver: Aktivera TV: n
    - Förväntat svar: OK och aktivera TV på
    - Du skriver: Ange ett larm för 9 am imorgon
    - Förväntat svar: OK, ange ett larm för 9 am imorgon

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: lägga till parametrar till kommandon](./how-to-custom-commands-add-parameters-to-commands.md)
