---
title: 'Snabbstart: Skapa ett anpassat kommando (förhandsgranskning) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar och testar du ett värdprogram för anpassade kommandon.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155595"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Snabbstart: Skapa ett anpassat kommando (förhandsgranskning)

I den här artikeln får du lära dig hur du skapar och testar ett värdprogram för anpassade kommandon.
Ansökan kommer att känna igen ett uttryck som "slå på TV" och svara med ett enkelt meddelande "Ok, slå på TV".

## <a name="prerequisites"></a>Krav

- En Talprenumeration.

Om du inte har en talprenumeration kan du skapa en genom att navigera till [Talstudion](https://speech.microsoft.com/) och välja **Skapa en talresurs**.

  > [!div class="mx-imgBorder"]
  > [![Skapa ett](media/custom-speech-commands/create-new-subscription.png) projekt](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Under förhandsgranskningen stöds endast westus2-regionen.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Gå till Talstudion för anpassade kommandon

1. Öppna webbläsaren och navigera till [Talstudion](https://speech.microsoft.com/)
1. Ange dina autentiseringsuppgifter för att logga in på portalen

   - Standardvyn är din lista över talprenumerationer
     > [!NOTE]
     > Om du inte ser den valda prenumerationssidan kan du navigera dit genom att välja "Talresurser" på inställningsmenyn i det övre fältet.

1. Välj din Talprenumeration och välj sedan **Gå till Studio**
1. Välj **anpassade kommandon (förhandsgranskning)**

Standardvyn är en lista över de anpassade kommandoprogram som du har skapat.

## <a name="create-a-custom-commands-project"></a>Skapa ett projekt för anpassade kommandon

1. Välj **Nytt projekt** för att skapa ett nytt projekt

   > [!div class="mx-imgBorder"]
   > ![Skapa ett projekt](media/custom-speech-commands/create-new-project.png)

1. Ange projektets namn och språk.
1. Välj en redigeringsresurs. Om det inte finns några giltiga redigeringsresurser skapar du en genom att välja **Skapa ny resurs**.

   > [!div class="mx-imgBorder"]
   > ![Skapa en resurs](media/custom-speech-commands/create-new-resource.png)

   1. Ange resursnamn, grupp, plats och prisnivå.

         > [!NOTE]
         > Du kan skapa resursgrupper genom att ange önskat resursgruppsnamn i fältet "Resursgrupp". Resursgruppen skapas när **Skapa** är markerat.

1. Klicka på **Skapa** om du vill skapa projektet.
1. När du har skapat det väljer du projektet.

Vyn bör nu vara en översikt över programmet Anpassade kommandon.

## <a name="update-luis-resources-optional"></a>Uppdatera LUIS-resurser (valfritt)

Du kan uppdatera redigeringsresursuppsättningen i det nya projektfönstret och ange en förutsägelseresurs som används för att känna igen indata under körning.

> [!NOTE]
> Du måste ange en förutsägelseresurs innan programmet begär förutsägelser utöver de 1 000 begäranden som tillhandahålls av redigeringsresursen.

> [!div class="mx-imgBorder"]
> ![Ange LUIS-resurser](media/custom-speech-commands/set-luis-resources.png)

1. Navigera till fönstret LUIS-resurser genom att välja **Inställningar** i den vänstra rutan och sedan **LUIS-resurser** från mittenfönstret.
1. Välj en förutsägelseresurs eller skapa en genom att välja **Skapa ny resurs**
1. Välj **Spara**

## <a name="create-a-new-command"></a>Skapa ett nytt kommando

Nu kan du skapa ett kommando. Låt oss använda ett exempel som tar `turn on the tv`ett enda uttryck `Ok, turning on the TV`och svarar med meddelandet .

1. Skapa ett nytt kommando `+` genom att markera ikonen bredvid kommandon och ge det namnet`TurnOn`
1. Välj **Spara**

> [!div class="mx-imgBorder"]
> ![Skapa ett kommando](media/custom-speech-commands/create-add-command.png)

Ett kommando är en uppsättning:

| Grupp            | Beskrivning                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Exempel på meningar | Exempel yttranden användaren kan säga att utlösa detta kommando                                                                 |
| Parametrar       | Information som krävs för att slutföra kommandot                                                                                |
| Regler för slutförande | De åtgärder som ska vidtas för att uppfylla kommandot. Om du till exempel vill svara på användaren eller kommunicera med en annan webbtjänst |
| Avancerade regler   | Ytterligare regler för att hantera mer specifika eller komplexa situationer                                                              |

### <a name="add-a-sample-sentence"></a>Lägga till en exempel mening

Låt oss börja med Exempelmeningar och ge ett exempel på vad användaren kan säga:

```
turn on the tv
```

För nu har vi inga parametrar så vi kan gå vidare till slutföranderegler.

### <a name="add-a-completion-rule"></a>Lägga till en slutföranderegel

Lägg nu till en slutföranderegel för att svara användaren och ange att en åtgärd vidtas.

1. Skapa en ny slutföranderegel `+` genom att välja ikonen bredvid Slutföranderegler
1. Ange regelnamnet
1. Lägga till en åtgärd
   1. Skapa en ny talsvarsåtgärd `+` genom att välja ikonen bredvid Åtgärder och välja`SpeechResponse`
   1. Ange svaret

   > [!NOTE]
   > Vanlig text måste börja med ett streck. För mer information, gå [hit](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Skapa ett talsvar](media/custom-speech-commands/create-speech-response-action.png)

1. Klicka på **Spara** för att spara regeln

> [!div class="mx-imgBorder"]
> ![Skapa en slutföranderegel](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Inställning    | Föreslaget värde                          | Beskrivning                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Regelnamn  | "BekräftelseSvar"                   | Ett namn som beskriver syftet med regeln          |
| Villkor | Inget                                     | Villkor som avgör när regeln kan köras    |
| Åtgärder    | SpeechResponse "- Ok, slå på TV: n" | Åtgärden som ska vidtas när regelvillkoret är sant |

## <a name="try-it-out"></a>Prova det

Testa beteendet med hjälp av testchattpanelen.

> [!div class="mx-imgBorder"]
> ![Testa med webbchatt](media/custom-speech-commands/create-basic-test-chat.png)

- Du skriver: "slå på tv: n"
- Förväntad respons: "Ok, slå på tv: n"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett anpassat kommando med parametrar (förhandsgranskning)](./quickstart-custom-speech-commands-create-parameters.md)
