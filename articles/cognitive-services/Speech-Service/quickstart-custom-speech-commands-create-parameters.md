---
title: 'Snabb start: skapa ett anpassat kommando med parametrar (för hands version) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln lägger du till parametrar i ett program med anpassade kommandon.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: caa1959ec64bb3a123c60763750267b107685b82
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111249"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)

I [föregående artikel](./quickstart-custom-speech-commands-create-new.md)skapade vi ett nytt anpassat kommando-projekt för att svara på kommandon utan parametrar.

I den här artikeln kommer vi att utöka det här programmet med parametrar så att det kan hantera aktivering och inaktive ring av flera enheter.

## <a name="create-parameters"></a>Skapa parametrar

1. Öppna projektet som [vi skapade tidigare](./quickstart-custom-speech-commands-create-new.md)
1. Eftersom kommandot nu kommer att hantera på och av, byter du namn på kommandot till "TurnOnOff"
   - Hovra över namnet på kommandot och välj redigerings ikonen för att ändra namnet
1. Skapa en ny parameter som visar om användaren vill aktivera eller inaktivera enheten
   - Välj `+` ikonen bredvid parameter avsnittet

   > [!div class="mx-imgBorder"]
   > ![skapa parameter](media/custom-speech-commands/create-on-off-parameter.png)

   | Inställning            | Föreslaget värde | Beskrivning                                                                                               |
   | ------------------ | --------------- | --------------------------------------------------------------------------------------------------------- |
   | Namn               | Mikrofonen           | Ett beskrivande namn för din parameter                                                                     |
   | Är global          | avmarkerat       | Kryss ruta som anger om ett värde för den här parametern används globalt för alla kommandon i projektet |
   | Krävs           | analysera         | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs          |
   | Svarsmall  | På eller av?      | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd                                       |
   | Typ               | Sträng          | Parameter typ, t. ex. tal, sträng eller datum/tid                                               |
   | Konfiguration      | Sträng lista     | För strängar begränsar en sträng lista indata till en uppsättning möjliga värden                                      |
   | Sträng List värden | på, av         | För en sträng List parameter, en uppsättning möjliga värden och deras synonymer                                |

   - Välj sedan `+` ikonen igen för att lägga till en andra parameter som representerar namnet på enheterna. I det här exemplet är en TV och en fläkt

   | Inställning            | Föreslaget värde   | Beskrivning                                                                                               |
   | ------------------ | ----------------- | --------------------------------------------------------------------------------------------------------- |
   | Namn               | SubjectDevice     | Ett beskrivande namn för din parameter                                                                     |
   | Är global          | avmarkerat         | Kryss ruta som anger om ett värde för den här parametern används globalt för alla kommandon i projektet |
   | Krävs           | analysera           | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs          |
   | Svarsmall  | Vilken enhet?     | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd                                       |
   | Typ               | Sträng            | Parameter typ, t. ex. tal, sträng eller datum/tid                                               |
   | Konfiguration      | Sträng lista       | För strängar begränsar en sträng lista indata till en uppsättning möjliga värden                                      |
   | Sträng List värden | TV, fläkt           | För en sträng List parameter, en uppsättning möjliga värden och deras synonymer                                |
   | Synonymer (TV)      | TV, Talad | Valfria synonymer för varje möjligt värde för en sträng List parameter                                      |

## <a name="add-sample-sentences"></a>Lägg till exempel meningar

Med parametrar är det bra att lägga till exempel meningar som beskriver alla möjliga kombinationer. Exempel:

1. Fullständig parameter information – `"turn {OnOff} the {SubjectDevice}"`
1. Del parameter information – `"turn it {OnOff}"`
1. Ingen parameter information – `"turn something"`

Exempel på meningar med olika mängder information gör att de anpassade kommandona kan matcha både ensidiga lösningar och flera lösningar med delvis information.

Med det i åtanke redigerar du exempel meningarna så att de använder parametrarna enligt rekommendationerna nedan.

> [!TIP]
> I exempel menings redigeraren använder du klammerparenteser för att referera till dina parametrar. - `turn {OnOff} the {SubjectDevice}` att använda TABB-slutförande för att referera till tidigare skapade parametrar.

> [!div class="mx-imgBorder"]
> ![exempel meningar med parametrar](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Lägg till parametrar till regel för slut för ande

Ändra regeln för slut för ande som du skapade i [föregående snabb start](./quickstart-custom-speech-commands-create-new.md):

1. Lägg till ett nytt villkor och välj obligatorisk parameter. Välj både `OnOff` och `SubjectDevice`
1. Redigera tal svars åtgärden för att använda `OnOff` och `SubjectDevice`:

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Prova det

Öppna panelen testa Chat och prova några interaktioner.

- Inmatade: Stäng av TV
- Utdata: OK, stänga av TV

- Inmatade: Stäng av TV: n
- Utdata: OK, stänga av TV

- Inmatade: Stäng av
- Utdata: vilken enhet?
- Inmatade: TV
- Utdata: OK, stänga av TV

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabb start: ansluta till ett anpassat kommando program med talet SDK (för hands version)](./quickstart-custom-speech-commands-speech-sdk.md)

