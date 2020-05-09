---
title: 'Snabb start: skapa ett anpassat kommando med parametrar (för hands version) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln lägger du till parametrar i ett program med anpassade kommandon.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853604"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Snabb start: skapa ett anpassat kommandon program med parametrar (förhands granskning)

I [föregående artikel](./quickstart-custom-speech-commands-create-new.md)skapade du ett enkelt anpassat kommando program utan parametrar.

I den här artikeln kommer du att utöka det här programmet så att det använder parametrar så att det kan hantera aktivering och inaktive ring av flera enheter.

## <a name="create-parameters"></a>Skapa parametrar

1. Öppna projektet [som du skapade tidigare](./quickstart-custom-speech-commands-create-new.md)
1. Nu ska vi redigera det befintliga kommandot för att aktivera och inaktivera flera enheter.
1. Eftersom kommandot nu kommer att hantera på och av byter du namn på kommandot `TurnOnOff`till.
   - I det vänstra fönstret väljer du `TurnOn` kommandot och klickar sedan på `...` ikonen bredvid `+ New command` överst i fönstret.
   
   - Välj `Rename` ikon. Byt **namn** till `TurOnOff`i popup- **kommandot Byt** namn. Välj sedan **Spara**.

1. Därefter skapar du en ny parameter som visar om användaren vill aktivera eller inaktivera enheten.
   - Välj `+ Add` ikonen som finns överst i mitten av fönstret. Välj **parameter**i list rutan.
   - I den högra rutan kan du se avsnittet **parametrar** -konfiguration.
   - Lägg till värde för **namn**.
   - Markera kryss rutan **nödvändig** . I fönstret **Lägg till svar för en obligatorisk parameter** väljer du **enkel redigerare** och till den **första varianten**, Lägg till
        ```
        On or Off?
        ```
   - Välj **Uppdatera**.

       > [!div class="mx-imgBorder"]
       > ![Skapa nödvändigt parameter svar](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Nu ska vi konfigurera resten av egenskaperna för parametern enligt följande och välja `Save` att spara konfigurationen alla konfigurationer till parametern.
       

       | Konfiguration      | Föreslaget värde     | Beskrivning                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Name               | Mikrofonen           | Ett beskrivande namn för parametern                                                                           |
       | Är global          | avmarkerat       | Kryss ruta som anger om ett värde för den här parametern används globalt för alla kommandon i programmet|
       | Krävs           | analysera         | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
       | Svar för obligatorisk parameter      |Enkelt redigerings program – > på eller av?      | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd |
       | Typ               | Sträng          | Typ av parameter, till exempel Number, String, datum tid eller geografi   |
       | Konfiguration      | Acceptera fördefinierade indatavärden från intern katalog | För strängar begränsar detta indata till en uppsättning möjliga värden |
       | Fördefinierade indatavärden     | på, av             | Uppsättning möjliga värden och deras alias         |
       
        > [!div class="mx-imgBorder"]
        > ![Skapa parameter](media/custom-speech-commands/create-on-off-parameter.png)

   - Välj sedan `+ Add` ikonen igen för att lägga till en andra parameter för att representera namnet på enheterna med följande konfiguration.
   

       | Inställningen            | Föreslaget värde       | Beskrivning                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Name               | SubjectDevice         | Ett beskrivande namn för parametern                                                                     |
       | Är global          | avmarkerat             | Kryss ruta som anger om ett värde för den här parametern används globalt för alla kommandon i programmet |
       | Krävs           | analysera               | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs          |
       | Enkelt redigerings program      | Vilken enhet?    | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd                                       |
       | Typ               | Sträng                | Typ av parameter, till exempel Number, String, datum tid eller geografi                                                |
       | Konfiguration      | Acceptera fördefinierade indatavärden från intern katalog | För strängar begränsar en sträng lista indata till en uppsättning möjliga värden       |
       | Fördefinierade indatavärden | TV, fläkt               | Uppsättning möjliga värden och deras alias                               |
       | Alias (TV)      | TV, Talad     | Valfria alias för varje möjligt värde med fördefinierade indatavärden                                 |

## <a name="add-example-sentences"></a>Lägg till exempel meningar

Med kommandon med parametrar är det bra att lägga till exempel meningar som beskriver alla möjliga kombinationer. Exempel:

1. Fullständig parameter information –`turn {OnOff} the {SubjectDevice}`
1. Partiell parameter information –`turn it {OnOff}`
1. Ingen parameter information –`turn something`

Exempel på meningar med olika typer av information gör att de anpassade kommandona kan matcha både ensidiga lösningar och flera lösningar med delvis information.

Med det i åtanke redigerar du exempel meningarna så att de använder parametrarna enligt rekommendationerna nedan.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> I exempel menings redigeraren använder du klammerparenteser för att referera till dina parametrar. - `turn {OnOff} the {SubjectDevice}`Använd fliken för automatisk komplettering som backas upp av parametrarna som skapats tidigare.

## <a name="add-parameters-to-completion-rules"></a>Lägg till parametrar till regler för slut för ande

Ändra regeln för slut för ande som vi skapade i [den tidigare snabb](./quickstart-custom-speech-commands-create-new.md)starten.

1. I avsnittet **villkor** lägger du till ett nytt villkor genom att välja **+ Lägg till ett villkor**
1. I det nya pop-up- **villkoret**väljer `Required parameters` du från List rutan **typ** . I check listan nedan markerar du både `OnOff` och. `SubjectDevice`
1. Klicka på **Skapa**.
1. I avsnittet **åtgärder** redigerar du den befintliga svars åtgärden skicka tal genom att hovra över åtgärden och klicka på ikonen Redigera. Den här gången använder vi nyligen skapade `OnOff` och parametrar `SubjectDevice`

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Prova nu
1. Välj `Train` ikonen som finns överst i den högra rutan.

1. När inlärningen är klar väljer `Test`du.
    - Ett nytt **test av ditt program** -fönster visas.
    - Prova några interaktioner.

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
> [Snabb start: Använd anpassade kommandon med anpassad röst (förhands granskning)](./quickstart-custom-speech-commands-select-custom-voice.md)
