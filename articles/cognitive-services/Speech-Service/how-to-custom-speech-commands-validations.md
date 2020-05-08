---
title: 'Gör så här: Lägg till valideringar i anpassade kommando parametrar'
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklarar vi hur du lägger till valideringar i en parameter i anpassade kommandon.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857182"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Gör så här: Lägg till valideringar i anpassade kommando parametrar (för hands version)

I den här artikeln ska du lägga till verifieringar i parametrar och prompter för korrigering.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:

> [!div class="checklist"]
> * [Snabb start: skapa ett anpassat kommando](./quickstart-custom-speech-commands-create-new.md)
> * [Snabb start: skapa ett anpassat kommando med parametrar](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Skapa ett SetTemperature-kommando

För att demonstrera valideringar ska vi skapa ett nytt kommando som gör det möjligt för användarna att ange temperatur.

1. Öppna dina tidigare skapade program för anpassade kommandon i [tal Studio](https://speech.microsoft.com/)
1. Skapa ett nytt kommando`SetTemperature`
1. Lägg till en parameter för mål temperaturen.

   | Parameter konfiguration           | Föreslaget värde    |Beskrivning                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Name              | Temperatur                       | Ett beskrivande namn för parametern                                |
   | Krävs          | analysera                           | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
   | Svar för obligatorisk parameter     | Enkelt redigerings program – > vilken temperatur vill du ha?  | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd |
   | Typ              | Antal                            | Typ av parameter, till exempel Number, String, datum tid eller geografi   |

1. Lägg till en verifiering för temperatur parametern.

    - På sidan **parametrar** -konfiguration för `Temperature` parameter väljer `Add a validation` du från avsnittet validering.
    - Fyll i värdena i popup-fönstret **ny validering** enligt följande och välj **skapa**.

  
       | Parameter konfiguration         | Föreslaget värde                                          | Beskrivning                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Minvärde        | 60               | För Number-parametrar kan det minsta värdet som denna parameter anta |
       | Maxvärde        | 80               | För Number-parametrar kan det högsta värdet som den här parametern anta |
       | Felsvar – enkelt redigerings program| Första variationen – tyvärr kan jag bara ange mellan 60 och 80 grader      | Fråga om du vill ange ett nytt värde om valideringen Miss lyckas                                       |

       > [!div class="mx-imgBorder"]
       > ![Lägg till en intervall validering](media/custom-speech-commands/validations-add-temperature.png)

1. Lägg till några exempel meningar

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Lägg till en regel för slut för ande för att bekräfta resultatet

   | Inställningen    | Föreslaget värde                                           |Beskrivning                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | Bekräftelse meddelande                                      |Ett namn som beskriver syftet med regeln |
   | Villkor | Obligatoriska parametrar-`Temperature`                       |Villkor som avgör när regeln kan köras    |   
   | Åtgärder    | Skicka tal svar –`Ok, setting temperature to {Temperature} degrees` | Den åtgärd som ska vidtas när regel villkoret är sant |

> [!TIP]
> I det här exemplet används ett tal svar för att bekräfta resultatet. Exempel på hur du slutför kommandot med en klient åtgärd finns i: [så här gör du för att utföra kommandon på klienten med tal-SDK: n](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Prova nu
1. Välj `Train` ikonen som finns överst i den högra rutan.

1. När inlärningen är klar väljer `Test` du och provar några interaktioner.

    - Inmatade: Ange temperatur till 72 grader
    - Utdata: OK, ställa in temperatur på 72 grader
    - Inmatade: Ange temperatur till 45 grader
    - Utdata: Tyvärr, det går bara att ange mellan 60 och 80 grader
    - Inmatade: gör det till 72 grader i stället
    - Utdata: OK, ställa in temperatur på 72 grader

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: Lägg till en bekräftelse till ett anpassat kommando (förhands granskning)](./how-to-custom-speech-commands-confirmations.md)
