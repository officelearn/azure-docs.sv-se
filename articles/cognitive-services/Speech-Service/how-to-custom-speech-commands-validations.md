---
title: Lägg till valideringar i förhands granskning av anpassade kommandon – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du lägger till valideringar i en kommando parameter i en för hands version av anpassade kommandon.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: nitinme
ms.openlocfilehash: eb011510a9f636aea9910a4be445cd094acf0c21
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509584"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Lägga till valideringar i en kommando parameter i ett för hands versions program för anpassade kommandon

I den här artikeln får du lära dig hur du lägger till valideringar i parametrar och prompter för korrigering.

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i följande artiklar:

> [!div class="checklist"]
 
> * [Snabb start: skapa en anpassad kommando förhands gransknings app](./quickstart-custom-speech-commands-create-new.md)
> * [Snabb start: skapa en anpassad kommando förhands gransknings app med parametrar](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Skapa ett SetTemperature-kommando

Skapa ett nytt kommando som gör det möjligt för användarna att ange temperatur för att demonstrera giltiga inställningar.

1. Öppna den förhands gransknings app för anpassade kommandon som du skapade i [tal Studio](https://speech.microsoft.com/).
1. Skapa ett nytt **SetTemperature** -kommando.
1. Lägg till en temperatur parameter som har följande konfiguration:

   | Parameter konfiguration           | Föreslaget värde    |Description                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Namn**              | **Temperatur**                       | Ett beskrivande namn för parametern                                |
   | **Obligatoriskt**          | Markerad                           | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
   | **Svar för obligatorisk parameter**     | **Enkelt redigerings program – > vilken temperatur vill du ha?**  | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd |
   | **Typ**              | **Nummer**                            | Typ av parameter, till exempel Number, String, DateTime eller geografi   |

1. Lägg till en verifiering för temperatur parametern.

    1. På sidan **parametrar** -konfiguration för parametern temperatur väljer du **Lägg till en verifiering** i avsnittet **valideringar** .

    1. I popup-fönstret **ny validering** konfigurerar du verifieringen enligt följande:
  
       | Parameter konfiguration         | Föreslaget värde                                          | Description                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Minvärde**        | **60**               | För Number-parametrar kan det minsta värdet som denna parameter anta |
       | **Maxvärde**        | **80**               | För Number-parametrar kan det högsta värdet som den här parametern anta |
       | **Felsvar – enkelt redigerings program**| **Första variationen – tyvärr kan jag bara ange mellan 60 och 80 grader**      | Fråga om du vill ange ett nytt värde om valideringen Miss lyckas                                       |

       > [!div class="mx-imgBorder"]
       > ![Lägg till en intervall validering](media/custom-speech-commands/validations-add-temperature.png)

1. Välj **Skapa**.

1. Lägg till några exempel meningar.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Lägg till en regel för slut för ande som har följande konfiguration. Den här regeln bekräftar resultatet.

   | Inställningen    | Föreslaget värde                                           |Beskrivning                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | Bekräftelse meddelande                                      |Ett namn som beskriver syftet med regeln |
   | **Villkor** | **Obligatoriska parametrar-temperatur**                       |Villkor som avgör när regeln kan köras    |   
   | **Åtgärder**    | **Skicka tal svar – OK, ställa in temperatur på {temperatur} grader** | Den åtgärd som ska vidtas när regel villkoret är sant |

> [!TIP]
> I det här exemplet används ett tal svar för att bekräfta resultatet. Exempel på hur du slutför kommandot med en klient åtgärd finns i [How to: uppfylla kommandon på klienten med tal-SDK](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Prova nu

1. Välj **träna**.

1. När utbildningen är färdig väljer du **test**och försöker sedan utföra följande interaktioner:

    - Inmatade: Ange temperatur till 72 grader
    - Utdata: OK, ställa in temperatur på 72 grader
    - Inmatade: Ange temperatur till 45 grader
    - Utdata: Tyvärr, det går bara att ange mellan 60 och 80 grader
    - Inmatade: gör det till 72 grader i stället
    - Utdata: OK, ställa in temperatur på 72 grader

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till bekräftelser i ett kommando i en förhands gransknings app för anpassade kommandon](./how-to-custom-speech-commands-confirmations.md)
