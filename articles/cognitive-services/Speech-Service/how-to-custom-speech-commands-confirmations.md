---
title: Så här lägger du till en bekräftelse i ett anpassat kommando (förhandsgranskning)
titleSuffix: Azure Cognitive Services
description: I den här artikeln implementerar du bekräftelser för ett kommando i anpassade kommandon.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456502"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Så här lägger du till en bekräftelse i ett anpassat kommando (förhandsgranskning)

I den här artikeln får du lära dig hur du lägger till en bekräftelse i ett kommando.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:

- [Snabbstart: Skapa ett anpassat kommando (förhandsgranskning)](./quickstart-custom-speech-commands-create-new.md)
- [Snabbstart: Skapa ett anpassat kommando med parametrar (förhandsgranskning)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Skapa ett SetAlarm-kommando

Om du vill demonstrera valideringar ska vi skapa ett nytt kommando som gör det möjligt för användaren att ställa in ett alarm.

1. Öppna ditt tidigare skapade program för anpassade kommandon i [Speech Studio](https://speech.microsoft.com/)
1. Skapa ett nytt **kommandouppsättninglarm**
1. Lägga till en parameter som heter DateTime

   | Inställning           | Föreslaget värde                                          | Beskrivning                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Namn              | DateTime                                                 | Ett beskrivande namn för kommandoparametern                                                    |
   | Krävs          | true                                                     | Kryssruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
   | Mall för svar | "- Vilken tid?"                                           | En uppmaning att be om värdet av den här parametern när den inte är känd                              |
   | Typ              | DateTime                                                 | Typ av parameter, till exempel Tal, Sträng eller Datumtid                                      |
   | Standardvärden för datum     | Om datum saknas idag                             |                                                                                                  |
   | Standardvärden för tid     | Om tid saknas användning start av dagen                      |                                                                                                  | 

1. Lägga till några exempelmeningar
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Lägga till en slutföranderegel för att bekräfta resultatet

   | Inställning    | Föreslaget värde                                         | Beskrivning                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn  | Ställ in larm                                               | Ett namn som beskriver syftet med regeln          |
   | Åtgärder    | SpeechResponse - "- Ok, alarm inställt för {DateTime}"       | Åtgärden som ska vidtas när regelvillkoret är sant |

## <a name="try-it-out"></a>Prova det

Välj testpanelen och prova några interaktioner.

- Ingång: Ställ in larm för i morgon vid lunchtid
- Utgång: "Ok, larm inställt på 12/06/2019 12:00:00"

- Ingång: Ställ in ett larm
- Utdata: "Vilken tid?"
- Ingång: 17:00
- Utgång: "Ok, larm inställt på 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Lägg till de avancerade reglerna för bekräftelse

1. Lägg till en avancerad regel för bekräftelse. 

    Denna regel kommer att be användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (ja / nej) för nästa tur.

   | Inställning               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Bekräfta datumtid                                                                | Ett namn som beskriver syftet med regeln          |
   | Villkor            | Obligatorisk parameter - DateTime                                                    | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | SpeechResponse - "- Vill du ställa in ett alarm för {DateTime}?"       | Åtgärden som ska vidtas när regelvillkoret är sant |
   | Tillstånd efter körning | Vänta på ingång                                                                   | Tillstånd för användaren efter svängen                  |
   | Förväntningar          | Bekräftelse                                                                     | Förväntan inför nästa sväng                      |

1. Lägg till en avancerad regel för att hantera en lyckad bekräftelse (användaren sa ja)

   | Inställning               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Accepterad bekräftelse                                                            | Ett namn som beskriver syftet med regeln          |
   | Villkor            | Parametern SuccessfulConfirmation & obligatoriskt - DateTime                           | Villkor som avgör när regeln kan köras    |   
   | Tillstånd efter körning | Redo för slutförande                                                             | Användarens tillstånd efter svängen                   |

1. Lägg till en avancerad regel för att hantera en nekad bekräftelse (användaren sa nej)

   | Inställning               | Föreslaget värde                                                                  | Beskrivning                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Nekad bekräfta                                                                   | Ett namn som beskriver syftet med regeln          |
   | Villkor            | Parametern Nekadkonkfirmation & obligatoriskt - DateTime                               | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | ClearParameter - DateTime & SpeechResponse - "- Inga problem, vilken tid då?"     | Åtgärden som ska vidtas när regelvillkoret är sant |
   | Tillstånd efter körning | Vänta på ingång                                                                   | Användarens tillstånd efter svängen                   |
   | Förväntningar          | ElicitParameters - DateTime                                                      | Förväntan inför nästa sväng                      |

## <a name="try-it-out"></a>Prova det

Välj testpanelen och prova några interaktioner.

- Ingång: Ställ in larm för i morgon vid lunchtid
- Utdata: "Vill du ställa in ett larm för 12/07/2019 12:00:00?"
- Ingång: Nej
- Output: "Inga problem, vilken tid då?"
- Ingång: 17:00
- Utdata: "Vill du ställa in ett larm för 12/06/2019 17:00:00?"
- Ingång: Ja
- Utgång: "Ok, larm inställt på 12/06/2019 17:00:00"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här lägger du till en korrigering i ett steg i ett anpassat kommando (förhandsgranskning)](./how-to-custom-speech-commands-one-step-correction.md)