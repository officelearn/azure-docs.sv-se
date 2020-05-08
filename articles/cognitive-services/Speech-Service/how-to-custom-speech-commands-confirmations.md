---
title: 'Gör så här: Lägg till en bekräftelse till ett anpassat kommando (förhands granskning)'
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du implementerar bekräftelser för ett kommando i anpassade kommandon.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858227"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Gör så här: Lägg till en bekräftelse till ett anpassat kommando (förhands granskning)

I den här artikeln får du lära dig hur du lägger till en bekräftelse på ett kommando.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:
> [!div class="checklist"]
> *  [Snabb start: skapa ett anpassat kommando (förhands granskning)](./quickstart-custom-speech-commands-create-new.md)
> * [Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Skapa ett SetAlarm-kommando

För att demonstrera bekräftelser ska vi skapa ett nytt kommando som gör att användaren kan ställa in ett larm.

1. Öppna dina tidigare skapade program för anpassade kommandon i [tal Studio](https://speech.microsoft.com/).
1. Skapa ett nytt kommando `SetAlarm`.
1. Lägg till en parameter `DateTime` med namnet med följande konfiguration.

   | Inställningen                           | Föreslaget värde                     |  Beskrivning                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Name                              | DateTime                                | Ett beskrivande namn för parametern                                |
   | Krävs                          | analysera                                 | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
   | Svar för obligatorisk parameter   | Enkelt redigerings program – > vilken tid?                              | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd |
   | Typ                              | DateTime                                | Typ av parameter, till exempel Number, String, datum tid eller geografi   |
   | Standardinställningar för datum                     | Om datumet saknas används idag            | Standardvärdet för variabeln som ska användas om den inte anges av användaren.  |  
   | Standardvärden                     | Om tiden saknas i början av dagen     |  Standardvärdet för variabeln som ska användas om den inte anges av användaren.|

1. Lägg till några exempel meningar.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Lägg till en regel för slut för ande för att bekräfta resultatet.

   | Inställningen    | Föreslaget värde                               |Beskrivning                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Regelnamn  | Ange alarm                                               |    Ett namn som beskriver syftet med regeln |
   | Åtgärder    | Skicka tal svar – OK, alarm set för {DateTime}    |Den åtgärd som ska vidtas när regel villkoret är sant

## <a name="try-it-out"></a>Prova nu

1. Välj `Train` ikonen som finns överst i den högra rutan.

1. När inlärningen är klar väljer `Test`du.
    - Inmatade: Ställ in alarm i morgon kl. 12.00
    - Utdata: OK, alarm set för 2020-05-02 12:00:00
    - Inmatade: Ställ in ett larm
    - Utdata: vilken tid?
    - Inmatade: 17
    - Utdata: OK, alarm set för 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>Lägg till avancerade regler för bekräftelse

Bekräftelser uppnås genom tillägg av interaktions regler.

1. I det befintliga `SetAlarm` kommandot lägger du till en **interaktions regel** enligt markerings `+Add` ikonen i den mellersta rutan och väljer sedan kommandot **interaktions regler** -> **Bekräfta**.

    Den här regeln uppmanar användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (Ja/Nej) för nästa turn.

   | Inställningen               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Bekräfta datum och tid                                                                | Ett namn som beskriver syftet med regeln          |
   | Villkor            | Obligatorisk parameter-> DateTime                                                    | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | Skicka tal svar – > är du säker på att du vill ställa in ett larm för {DateTime}?     | Den åtgärd som ska vidtas när regel villkoret är sant |
   | Prestationer          | Förväntar bekräftelse från användare                                                 | Förväntar dig för nästa turn                      |
   | Efter körnings tillstånd  | Vänta på användarens indata                                                            | Tillstånd för användaren efter turn                  |
  
      > [!div class="mx-imgBorder"]
      > ![Skapa nödvändigt parameter svar](media/custom-speech-commands/add-validation-set-temperature.png)

1. Lägg till en annan interaktions regel för att hantera en lyckad bekräftelse (användaren säger ja)

   | Inställningen               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Godkänd bekräftelse                                                            | Ett namn som beskriver syftet med regeln          |
   | Villkor            | Bekräftelsen lyckades & obligatorisk parameter-> DateTime                      | Villkor som avgör när regeln kan köras    |   
   | Efter körnings tillstånd | Regler för att köra slut för ande                                                          | Användarens tillstånd efter aktivering                   |

1. Lägg till en avancerad regel för att hantera en bekräftelse nekad (användaren sade nej)

   | Inställningen               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Nekad bekräftelse                                                                   | Ett namn som beskriver syftet med regeln          |
   | Villkor            | Bekräftelse nekades & obligatorisk parameter-> DateTime                               | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | Rensa parameter värde – > DateTime & skicka tal svar – > inga problem, vilken tid sedan?  | Den åtgärd som ska vidtas när regel villkoret är sant |
   | Tillstånd efter körning | Vänta på indatamängd                                                                   | Användarens tillstånd efter aktivering                   |
   | Prestationer          | Förväntade parametrar indata från användaren-> DateTime                           | Förväntar dig för nästa turn                      |

## <a name="try-out-the-changes"></a>Prova ändringarna

Välj `Train`, vänta tills inlärningen är klar `Test`och välj.

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
> [Gör så här: Lägg till en steg korrigering i ett anpassat kommando (förhands granskning)](./how-to-custom-speech-commands-one-step-correction.md)