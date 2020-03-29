---
title: 'Snabbstart: Skapa ett anpassat kommando med parametrar (förhandsversion) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln ska du lägga till parametrar i ett program för anpassade kommandon.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348537"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Snabbstart: Skapa ett anpassat kommando med parametrar (förhandsgranskning)

I [föregående artikel](./quickstart-custom-speech-commands-create-new.md)skapade vi ett nytt anpassat kommandon projekt för att svara på kommandon utan parametrar.

I den här artikeln kommer vi att utöka det här programmet med parametrar så att det kan hantera att slå på och stänga av flera enheter.

## <a name="create-parameters"></a>Skapa parametrar

1. Öppna projektet [vi skapade tidigare](./quickstart-custom-speech-commands-create-new.md)
1. Eftersom kommandot nu hanterar på och av byter du namn på kommandot till "TurnOnOff"
   - Hovra över namnet på kommandot och välj redigeringsikonen för att ändra namnet
1. Skapa en ny parameter som representerar om användaren vill slå på eller inaktivera enheten
   - Markera `+` ikonen bredvid avsnittet Parametrar

   > [!div class="mx-imgBorder"]
   > ![Skapa parameter](media/custom-speech-commands/create-on-off-parameter.png)

   | Inställning            | Föreslaget värde     | Beskrivning                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Namn               | Onoff               | Ett beskrivande namn för parametern                                                                     |
   | Är global          | Okontrollerat           | Kryssruta som anger om ett värde för den här parametern tillämpas globalt på alla kommandon i projektet |
   | Krävs           | Kontrolleras             | Kryssruta som anger om ett värde för den här parametern krävs innan kommandot slutförs          |
   | Mall för svar  | "- På eller av?"      | En uppmaning att be om värdet av den här parametern när den inte är känd                                       |
   | Typ               | String              | Typ av parameter, till exempel Tal, Sträng eller Datumtid                                               |
   | Konfiguration      | Stränglista         | För strängar begränsar en stränglista indata till en uppsättning möjliga värden                                      |
   | Värden för stränglista | på, av             | För en stränglisteparameter visas en uppsättning möjliga värden och deras synonymer                                |

   - Välj sedan `+` ikonen igen om du vill lägga till en andra parameter som representerar namnet på enheterna. I det här exemplet kan en TV och ett

   | Inställning            | Föreslaget värde       | Beskrivning                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Namn               | SubjectDevice         | Ett beskrivande namn för parametern                                                                     |
   | Är global          | Okontrollerat             | Kryssruta som anger om ett värde för den här parametern tillämpas globalt på alla kommandon i projektet |
   | Krävs           | Kontrolleras               | Kryssruta som anger om ett värde för den här parametern krävs innan kommandot slutförs          |
   | Mall för svar  | "- Vilken enhet?"     | En uppmaning att be om värdet av den här parametern när den inte är känd                                       |
   | Typ               | String                | Typ av parameter, till exempel Tal, Sträng eller Datumtid                                               |
   | Konfiguration      | Stränglista           | För strängar begränsar en stränglista indata till en uppsättning möjliga värden                                      |
   | Värden för stränglista | tv, fläkt               | För en stränglisteparameter visas en uppsättning möjliga värden och deras synonymer                                |
   | Synonymer (tv)      | tv, tv     | Valfria synonymer för varje möjligt värde för en stränglisteparameter                                      |

## <a name="add-sample-sentences"></a>Lägga till exempelmeningar

Med parametrar är det bra att lägga till exempelmeningar som täcker alla möjliga kombinationer. Ett exempel:

1. Fullständig parameterinformation -`"turn {OnOff} the {SubjectDevice}"`
1. Information om partiell parameter -`"turn it {OnOff}"`
1. Ingen parameterinformation -`"turn something"`

Exempelmeningar med olika mängder information gör att programmet Anpassade kommandon kan lösa både lösningar med ett slag och lösningar med flera varv med partiell information.

Med detta i åtanke redigerar du exempelmeningar för att använda parametrarna enligt nedan.

> [!TIP]
> I redigeraren Exempelmeningar använder du klammerparenteser för att referera till dina parametrar. - `turn {OnOff} the {SubjectDevice}`Använd flikkomplettering för att referera till tidigare skapade parametrar.

> [!div class="mx-imgBorder"]
> ![Exempel på meningar med parametrar](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Lägga till parametrar i slutföranderegeln

Ändra slutföranderegeln som du skapade i [föregående snabbstart:](./quickstart-custom-speech-commands-create-new.md)

1. Lägg till ett nytt villkor och välj Parametern Obligatoriskt. Välj `OnOff` både och`SubjectDevice`
1. Redigera åtgärden Talsvar `OnOff` som `SubjectDevice`ska användas och:

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Prova det

Öppna panelen Testa chatt och prova några interaktioner.

- Ingång: stäng av tv:n
- Utgång: Ok, stänga av tv:n

- Ingång: stäng av TV:n
- Utgång: Ok, stänga av tv:n

- Ingång: stäng av den
- Utdata: Vilken enhet?
- Ingång: tv:n
- Utgång: Ok, stänga av tv:n

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Använda anpassade kommandon med anpassad röst (förhandsgranskning)](./quickstart-custom-speech-commands-select-custom-voice.md)
