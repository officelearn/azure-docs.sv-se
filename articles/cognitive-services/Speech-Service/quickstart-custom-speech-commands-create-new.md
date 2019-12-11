---
title: 'Snabb start: skapa ett anpassat kommando (för hands version) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar och testar du ett program med en värd för anpassade kommandon.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 056dd4331d30335078ea68350f711e37a7b42070
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976629"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Snabb start: skapa ett anpassat kommando (förhands granskning)

I den här artikeln får du lära dig hur du skapar och testar ett program med en värd för anpassade kommandon.
Programmet kommer att identifiera en uttryck som "slå på TV" och svara med ett enkelt meddelande "OK, som aktiverar TV".

## <a name="prerequisites"></a>Krav

- En tal prenumeration. [Prova röst tjänsten kostnads fritt](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > Under för hands versionen stöds endast regionen westus2 för prenumerations nycklar.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Gå till tal Studio för anpassade kommandon

1. Öppna webbläsaren och gå till [tal Studio](https://speech.microsoft.com/)
1. Ange dina autentiseringsuppgifter för att logga in på portalen

   - Standardvyn är listan med tal prenumerationer
     > [!NOTE]
     > Om du inte ser sidan Välj prenumeration kan du gå dit genom att välja "tal resurser" på menyn Inställningar i det översta fältet.

1. Välj din tal prenumeration och välj sedan **gå till Studio**
1. Välj **anpassade kommandon (förhands granskning)**

Standardvyn är en lista över de anpassade kommando program som du har skapat.

## <a name="create-a-custom-commands-project"></a>Skapa ett anpassat kommandon-projekt

1. Välj **nytt projekt** för att skapa ett nytt projekt

   > [!div class="mx-imgBorder"]
   > ![skapa ett projekt](media/custom-speech-commands/create-new-project.png)

1. Ange projekt namn och språk.
1. Välj en redigerings resurs. Om det inte finns några giltiga redigerings resurser skapar du en genom att välja **Skapa ny resurs**.

   > [!div class="mx-imgBorder"]
   > ![Skapa en resurs](media/custom-speech-commands/create-new-resource.png)

   1. Ange resurs namn, grupp, plats och pris nivå.

         > [!NOTE]
         > Du kan skapa resurs grupper genom att ange önskat resurs grupp namn i fältet resurs grupp. Resurs gruppen skapas när du väljer **skapa** .

1. Skapa projektet genom att klicka på **skapa** .
1. När du har skapat väljer du ditt projekt.

Din vy bör nu vara en översikt över ditt anpassade kommandon-program.

## <a name="create-a-new-command"></a>Skapa ett nytt kommando

Nu kan du skapa ett kommando. Vi använder ett exempel som tar en enskild uttryck, `turn on the tv`och svarar med meddelandet `Ok, turning on the TV`.

1. Skapa ett nytt kommando genom att välja ikonen `+` bredvid kommandon och ge den namnet `TurnOn`
1. Välj **Spara**

> [!div class="mx-imgBorder"]
> ![skapa ett kommando](media/custom-speech-commands/create-add-command.png)

Ett kommando är en uppsättning:

| Grupp            | Beskrivning                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Exempelmeningar | Exempel yttranden användaren kan säga att utlösa det här kommandot                                                                 |
| Parametrar       | Information som krävs för att slutföra kommandot                                                                                |
| Regler för slut för ande | De åtgärder som ska vidtas för att utföra kommandot. Till exempel för att svara på användaren eller kommunicera med en annan webb tjänst |
| Avancerade regler   | Ytterligare regler för att hantera mer detaljerade eller komplexa situationer                                                              |

### <a name="add-a-sample-sentence"></a>Lägg till en exempel mening

Vi börjar med exempel meningar och ger ett exempel på vad användaren kan säga:

```
turn on the tv
```

För närvarande har vi inga parametrar så att vi kan gå vidare till slut för ande regler.

### <a name="add-a-completion-rule"></a>Lägg till en regel för slut för ande

Lägg nu till en regel för slut för ande för att svara på användaren som anger att en åtgärd utförs.

1. Skapa en ny regel för slut för ande genom att välja `+` ikonen bredvid slut för ande regler
1. Ange regel namnet
1. Lägga till en åtgärd
   1. Skapa en ny röst svars åtgärd genom att välja ikonen `+` bredvid åtgärder och välj `SpeechResponse`
   1. Ange svaret

   > [!NOTE]
   > Vanlig text måste börja med ett bindestreck. Mer information finns [här](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![skapa ett tal svar](media/custom-speech-commands/create-speech-response-action.png)

1. Klicka på **Spara** för att spara regeln

> [!div class="mx-imgBorder"]
> ![skapa en regel för slut för ande](media/custom-speech-commands/create-basic-completion-response-rule.png)


| Inställning    | Föreslaget värde                        | Beskrivning                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Regelnamn  | "ConfirmationResponse"                 | Ett namn som beskriver syftet med regeln          |
| Villkor | Inget                                   | Villkor som avgör när regeln kan köras    |
| Åtgärder    | SpeechResponse "– OK, aktivera TV" | Den åtgärd som ska vidtas när regel villkoret är sant |

## <a name="try-it-out"></a>Prova

Testa beteendet med hjälp av panelen test chatt.

> [!div class="mx-imgBorder"]
> ![test med Web Chat](media/custom-speech-commands/create-basic-test-chat.png)

- Du skriver: "slå på TV"
- Förväntat svar: "OK, aktivera TV"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)
