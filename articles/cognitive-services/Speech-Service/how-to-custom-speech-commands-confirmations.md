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
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456502"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Gör så här: Lägg till en bekräftelse till ett anpassat kommando (förhands granskning)

I den här artikeln får du lära dig hur du lägger till en bekräftelse på ett kommando.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:

- [Snabb start: skapa ett anpassat kommando (förhands granskning)](./quickstart-custom-speech-commands-create-new.md)
- [Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Skapa ett SetAlarm-kommando

För att demonstrera valideringar ska vi skapa ett nytt kommando som gör att användaren kan ställa in ett larm.

1. Öppna dina tidigare skapade program för anpassade kommandon i [tal Studio](https://speech.microsoft.com/)
1. Skapa ett nytt kommando **SetAlarm**
1. Lägg till en parameter med namnet DateTime

   | Inställningen           | Föreslaget värde                                          | Beskrivning                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Name              | DateTime                                                 | Ett beskrivande namn för kommando parametern                                                    |
   | Krävs          | true                                                     | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
   | Svarsmall | "-Vilken tid?"                                           | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd                              |
   | Typ              | DateTime                                                 | Parameter typ, t. ex. tal, sträng eller datum/tid                                      |
   | Standardinställningar för datum     | Om datumet saknas används idag                             |                                                                                                  |
   | Standardvärden     | Om tiden saknas i början av dagen                      |                                                                                                  | 

1. Lägg till några exempel meningar
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Lägg till en regel för slut för ande för att bekräfta resultatet

   | Inställningen    | Föreslaget värde                                         | Beskrivning                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn  | Ange alarm                                               | Ett namn som beskriver syftet med regeln          |
   | Åtgärder    | SpeechResponse-"-OK, alarm set för {DateTime}"       | Den åtgärd som ska vidtas när regel villkoret är sant |

## <a name="try-it-out"></a>Prova nu

Välj panelen test och prova några interaktioner.

- Inmatade: Ställ in alarm i morgon kl. 12.00
- Utdata: "OK, alarm set för 12/06/2019 12:00:00"

- Inmatade: Ställ in ett larm
- Utdata: "vilken tid?"
- Inmatade: 17
- Utdata: "OK, alarm set för 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Lägg till avancerade regler för bekräftelse

1. Lägg till en avancerad regel för bekräftelse. 

    Den här regeln uppmanar användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (Ja/Nej) för nästa turn.

   | Inställningen               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Bekräfta datum och tid                                                                | Ett namn som beskriver syftet med regeln          |
   | Villkor            | Obligatorisk parameter-DateTime                                                    | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | SpeechResponse-"-är du säker på att du vill ange ett larm för {DateTime}?"       | Den åtgärd som ska vidtas när regel villkoret är sant |
   | Tillstånd efter körning | Vänta på indatamängd                                                                   | Tillstånd för användaren efter turn                  |
   | Prestationer          | Bekräftelse                                                                     | Förväntar dig för nästa turn                      |

1. Lägg till en avancerad regel för att hantera en lyckad bekräftelse (användaren säger ja)

   | Inställningen               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Godkänd bekräftelse                                                            | Ett namn som beskriver syftet med regeln          |
   | Villkor            | SuccessfulConfirmation & obligatorisk parameter-DateTime                           | Villkor som avgör när regeln kan köras    |   
   | Tillstånd efter körning | Klar för slut för ande                                                             | Användarens tillstånd efter aktivering                   |

1. Lägg till en avancerad regel för att hantera en bekräftelse nekad (användaren sade nej)

   | Inställningen               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Nekad bekräftelse                                                                   | Ett namn som beskriver syftet med regeln          |
   | Villkor            | DeniedConfirmation & obligatorisk parameter-DateTime                               | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | ClearParameter-DateTime & SpeechResponse-"– inga problem, vilken tid sedan?"     | Den åtgärd som ska vidtas när regel villkoret är sant |
   | Tillstånd efter körning | Vänta på indatamängd                                                                   | Användarens tillstånd efter aktivering                   |
   | Prestationer          | ElicitParameters-DateTime                                                      | Förväntar dig för nästa turn                      |

## <a name="try-it-out"></a>Prova nu

Välj panelen test och prova några interaktioner.

- Inmatade: Ställ in alarm i morgon kl. 12.00
- Utdata: "är du säker på att du vill ställa in ett larm för 12/07/2019 12:00:00?"
- Inmatade: Nej
- Output: "inga problem, vilken tid sedan?"
- Inmatade: 17
- Utdata: "är du säker på att du vill ställa in ett larm för 12/06/2019 17:00:00?"
- Inmatade: Ja
- Utdata: "OK, alarm set för 12/06/2019 17:00:00"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: Lägg till en steg korrigering i ett anpassat kommando (förhands granskning)](./how-to-custom-speech-commands-one-step-correction.md)