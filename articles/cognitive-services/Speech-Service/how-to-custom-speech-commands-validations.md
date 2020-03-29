---
title: Så här lägger du till valideringar i parametrar för anpassat kommando (förhandsversion)
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
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156462"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Så här lägger du till valideringar i parametrar för anpassat kommando (förhandsversion)

I den här artikeln får du lära dig hur du lägger till verifieringar i parametrar och frågar efter korrigering.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:

- [Snabbstart: Skapa ett anpassat kommando (förhandsgranskning)](./quickstart-custom-speech-commands-create-new.md)
- [Snabbstart: Skapa ett anpassat kommando med parametrar (förhandsgranskning)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Skapa ett SetTemperature-kommando

Om du vill demonstrera valideringar ska vi skapa ett nytt kommando som gör det möjligt för användaren att ställa in temperaturen.

1. Öppna ditt tidigare skapade program för anpassade kommandon i [Speech Studio](https://speech.microsoft.com/)
1. Skapa en ny **kommandouppsättningTemperatur**
1. Lägga till en parameter för måltemperaturen
1. Lägga till en validering för temperaturparametern
   > [!div class="mx-imgBorder"]
   > ![Lägga till en områdesverifiering](media/custom-speech-commands/validations-add-temperature.png)

   | Inställning           | Föreslaget värde                                          | Beskrivning                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Namn              | Temperatur                                              | Ett beskrivande namn för kommandoparametern                                                    |
   | Krävs          | true                                                     | Kryssruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
   | Mall för svar | "- Vilken temperatur vill du ha?"                     | En uppmaning att be om värdet av den här parametern när den inte är känd                              |
   | Typ              | Tal                                                   | Typ av parameter, till exempel Tal, Sträng eller Datumtid                                      |
   | Validering        | Min Värde: 60, Max Värde: 80                             | För parametrar för tal är det tillåtna värdeintervallet för parametern                             |
   | Mall för svar | "- Tyvärr, jag kan bara ställa in mellan 60 och 80 grader"      | Fråga om att be om ett uppdaterat värde om valideringen misslyckas                                       |

1. Lägga till några exempelmeningar

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Lägga till en slutföranderegel för att bekräfta resultatet

   | Inställning    | Föreslaget värde                                           | Beskrivning                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn  | Bekräftelsemeddelande                                      | Ett namn som beskriver syftet med regeln          |
   | Villkor | Obligatorisk parameter - Temperatur                          | Villkor som avgör när regeln kan köras    |
   | Åtgärder    | SpeechResponse - "- Ok, inställning till {Temperatur} grader" | Åtgärden som ska vidtas när regelvillkoret är sant |

> [!TIP]
> I det här exemplet används ett talsvar för att bekräfta resultatet. Exempel på hur du slutför kommandot med en klientåtgärd finns: [Så här fyller du kommandon på klienten med tal-SDK (förhandsversion)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Prova det

Välj testpanelen och prova några interaktioner.

- Ingång: Ställ in temperaturen på 72 grader
- Utgång: "Ok, inställning till 72 grader"

- Ingång: Ställ in temperaturen på 45 grader
- Output: "Tyvärr, jag kan bara ställa in mellan 60 och 80 grader"
- Ingång: gör det 72 grader istället
- Utgång: "Ok, inställning till 72 grader"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här lägger du till en bekräftelse i ett anpassat kommando (förhandsgranskning)](./how-to-custom-speech-commands-confirmations.md)
