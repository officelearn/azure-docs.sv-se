---
title: Lägg till bekräftelser i ett anpassat kommando förhandsgranska app-Speech service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du lägger till bekräftelser i kommandon i en för hands version av anpassade kommandon.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310476"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Lägg till bekräftelser i ett kommando i ett för hands versions program för anpassade kommandon

I den här artikeln får du lära dig hur du skapar bekräftelser för kommandon i en för hands version av anpassade kommandon.

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i följande artiklar:
> [!div class="checklist"]
> * [Snabb start: skapa en anpassad kommando förhands gransknings app](./quickstart-custom-speech-commands-create-new.md)
> * [Snabb start: skapa en anpassad kommando förhands gransknings app med parametrar](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Skapa ett SetAlarm-kommando

Skapa ett nytt kommando som anger ett larm för att demonstrera bekräftelser.

1. Öppna den förhands gransknings app för anpassade kommandon som du skapade i [tal Studio](https://speech.microsoft.com/).
1. Skapa ett nytt **SetAlarm** -kommando.
1. Lägg till en **datetime** -parameter som har följande konfiguration:

   | Inställning                           | Föreslaget värde                     |  Description                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Namn**                              | **DateTime**                                | Beskrivande namn för parametern                                |
   | **Obligatoriskt**                          | Markerad                                 | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
   | **Svar för en obligatorisk parameter**   | **Enkelt redigerings program – > vilken tid?**                              | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd |
   | **Typ**                              | **DateTime**                                | Typ av parameter, till exempel Number, String, DateTime eller geografi   |
   | **Standardinställningar för datum**                     | Om datumet saknas använder du dagens datum            | Standardvärdet för den variabel som ska användas om den inte anges av användaren  |  
   | **Standardvärden**                     | Om tiden saknas använder du dag början     |  Standardvärdet för den variabel som ska användas om den inte anges av användaren|

1. Lägg till några exempel meningar.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Lägg till en regel för slut för ande för att bekräfta resultatet. Använd följande konfiguration:

   | Inställning    | Föreslaget värde                               |Description                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Regel namn**  | **Ange alarm**                                               |    Ett namn som beskriver syftet med regeln |
   | **Åtgärder**    | **Skicka tal svar – > OK, alarm set för {DateTime}**    |Den åtgärd som ska vidtas när regel villkoret är sant

## <a name="try-it-out"></a>Prova nu

1. Välj **träna** överst i den högra rutan.

1. När utbildningen är färdig väljer du **test**och försöker sedan med följande interaktioner:
    - Inmatade: Ställ in alarm i morgon kl. 12.00
    - Utdata: OK, alarm set för 2020-05-02 12:00:00
    - Inmatade: Ställ in ett larm
    - Utdata: vilken tid?
    - Inmatade: 17
    - Utdata: OK, alarm set för 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Lägg till interaktions regler för bekräftelsen

Skapa bekräftelser genom att lägga till interaktions regler.

1. I **SetAlarm** -kommandot väljer du **Lägg till** i mitten av fönstret och väljer sedan **interaktions regler**  >  **Bekräfta kommando**.

    Den här regeln uppmanar användaren att bekräfta datum och tid för larmet. Använd de här inställningarna:

   | Inställning               | Föreslaget värde                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Regel namn**             | **Bekräfta datum och tid**                                                                | Ett namn som beskriver syftet med regeln          |
   | **Villkor**            | **Obligatorisk parameter-> DateTime**                                                    | Villkor som avgör när regeln kan köras    |   
   | **Åtgärder**               | **Skicka tal svar – > är du säker på att du vill ställa in ett larm för {DateTime}?**     | Den åtgärd som ska vidtas när regel villkoret är sant |
   | **Prestationer**          | **Förväntar bekräftelse från användare**                                                 | Förväntar dig för nästa turn                      |
   | **Efter körnings tillstånd**  | **Vänta på användarens indata**                                                            | Tillstånd för användaren efter turn                  |
  
      > [!div class="mx-imgBorder"]
      > ![Skapa nödvändigt parameter svar](media/custom-speech-commands/add-validation-set-temperature.png)

1. Lägg till en interaktions regel för en godkänd bekräftelse (användaren säger ja). Använd följande konfiguration:

   | Inställning               | Föreslaget värde                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Regel namn**             | **Godkänd bekräftelse**                                                            | Ett namn som beskriver syftet med regeln          |
   | **Villkor**            | **Bekräftelsen lyckades & obligatorisk parameter-> DateTime**                      | Villkor som avgör när regeln kan köras    |   
   | **Efter körnings tillstånd** | **Regler för att köra slut för ande**                                                          | Användarens tillstånd efter aktivering                   |

1. Lägg till en interaktions regel för en nekad bekräftelse (användaren säger "No"). Använd följande konfiguration:

   | Inställning               | Föreslaget värde                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Regel namn**             | **Nekad bekräftelse**                                                                   | Ett namn som beskriver syftet med regeln          |
   | **Villkor**            | **Bekräftelse nekades & obligatorisk parameter-> DateTime**                               | Villkor som avgör när regeln kan köras    |   
   | **Åtgärder**               | **Rensa parameter värde – > DateTime & skicka tal svar – > inga problem, vilken tid sedan?**  | Den åtgärd som ska vidtas när regel villkoret är sant |
   | **Tillstånd efter körning** | **Vänta på indatamängd**                                                                   | Användarens tillstånd efter aktivering                   |
   | **Prestationer**          | **Parameter indata förväntas från användaren-> DateTime**                           | Förväntar dig för nästa turn                      |

## <a name="try-out-the-changes"></a>Prova ändringarna

1. Välj **träna**.

1. När utbildningen är färdig väljer du **test**och försöker sedan utföra följande interaktioner:

    - Inmatade: Ställ in alarm i morgon kl. 12.00
    - Utdata: är du säker på att du vill ställa in ett larm för 2020-05-02 12:00:00?
    - Inmatade: Nej
    - Utdata: inga problem, vilken tid sedan?
    - Inmatade: 17
    - Utdata: "är du säker på att du vill ställa in ett larm för 2020-05-01 17:00:00?"
    - Inmatade: Ja
    - Utdata: OK, alarm set för 2020-05-01 17:00:00

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till en korrigering i ett steg i ett kommando i en för hands version av anpassade kommandon](./how-to-custom-speech-commands-one-step-correction.md)
