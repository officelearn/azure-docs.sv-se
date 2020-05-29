---
title: 'Snabb start: skapa en anpassad kommando förhands gransknings app med parametrar-Speech service'
titleSuffix: Azure Cognitive Services
description: I den här artikeln lägger du till parametrar i ett program med anpassade kommandon så att du kan aktivera och inaktivera flera enheter.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 5461ef9680ab89c8cc9cc2e1166366abb04a6eab
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142290"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Snabb start: skapa ett anpassat kommando för hands versions program med parametrar

I [föregående artikel](./quickstart-custom-speech-commands-create-new.md)skapade du ett enkelt anpassat kommando program utan parametrar.

I den här artikeln ska du utöka programmet med parametrar så att det kan aktivera och inaktivera flera enheter.

## <a name="create-parameters"></a>Skapa parametrar

1. Öppna det projekt som du skapade i [föregående artikel](./quickstart-custom-speech-commands-create-new.md).

   Vi redigerar det befintliga kommandot så att det kan användas för att aktivera och inaktivera flera enheter.
1. Eftersom kommandot nu kommer att hantera både på och av byter du namn på det till **TurnOnOff**.
   1. I det vänstra fönstret väljer du kommandot **TurnON** och väljer sedan knappen med tre punkter (**...**) bredvid **nytt kommando** överst i fönstret.
   
   1. Välj **Byt namn**. Ändra **namnet** till **TurOnOff**i **kommando fönstret Byt** namn. Välj **Spara**.

1. Skapa en parameter som visar om användaren vill aktivera eller inaktivera enheten.
   1. Välj **Lägg till** överst i den mellersta rutan. Välj **parameter**i list rutan.
   1. I den högra rutan, i avsnittet **parametrar** , lägger du till ett värde i rutan **namn** .
   1. Välj **obligatoriskt**. I fönstret **Lägg till svar för en obligatorisk parameter** väljer du **enkel redigerare**. I rutan **första variationen** anger du den här texten:
        ```
        On or Off?
        ```
   1. Välj **Uppdatera**.

       > [!div class="mx-imgBorder"]
       > ![Skapa nödvändigt parameter svar](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Konfigurera resten av egenskaperna för parametern enligt följande:
       

    | Konfiguration      | Föreslaget värde     | Description                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Namn**               | **Mikrofonen**           | Ett beskrivande namn för parametern.                                                                  |
    | **Är global**          | Rensas       | En kryss ruta som anger om ett värde för parametern globalt tillämpas på alla kommandon i programmet.|
    | **Obligatoriskt**           | Vald         | En kryss ruta som anger om ett värde för parametern måste anges.  |
    | **Svar för obligatorisk parameter**      |**Enkelt redigerings program – > på eller av?**      | En uppmaning om att fråga efter värdet på parametern när den inte är känd. |
    | **Typ**               | **Sträng**          | Typ av parameter. Till exempel Number, String, datum tid, geografi.   |
    | **Konfiguration**      | **Acceptera fördefinierade indatavärden från intern katalog** | För strängar begränsar den här inställningen indata till en uppsättning möjliga värden. |
    | **Fördefinierade indatavärden**     | **på**, **av**             | En uppsättning möjliga värden och deras alias.         |
       


    > [!div class="mx-imgBorder"]
    > ![Skapa parameter](media/custom-speech-commands/create-on-off-parameter.png)

1. Spara inställningarna genom att klicka på **Spara**.

 1. Välj **Lägg till** igen för att lägga till en andra parameter. Den här parametern representerar namnet på enheten. Använd de här inställningarna:
   

       | Inställningen            | Föreslaget värde       | Description                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Namn**               | **SubjectDevice**         | Ett beskrivande namn för parametern.                                                                     |
       | **Är global**          | Rensas             | En kryss ruta som anger om ett värde för parametern globalt tillämpas på alla kommandon i programmet. |
       | **Obligatoriskt**           | Vald               | En kryss ruta som anger om ett värde för parametern måste anges.          |
       | **Enkelt redigerings program**      | **Vilken enhet?**    | En uppmaning om att fråga efter värdet på parametern när den inte är känd.                                       |
       | **Typ**               | **Sträng**                | Typ av parameter. Till exempel Number, String, datum tid, geografi.                                                |
       | **Konfiguration**      | **Acceptera fördefinierade indatavärden från intern katalog** | För strängar begränsar den här inställningen indata till en uppsättning möjliga värden.       |
       | **Fördefinierade indatavärden** | **TV**, **fläkt**               | En uppsättning möjliga värden och deras alias.                               |
       | **Alias** (TV)      | **TV**, **talad**     | Valfria alias för var och en av de fördefinierade indatavärdena.                                 |

## <a name="add-example-sentences"></a>Lägg till exempel meningar

För kommandon som har parametrar är det bra att lägga till exempel meningar som beskriver alla möjliga kombinationer. Ett exempel:

- Fullständig parameter information:`turn {OnOff} the {SubjectDevice}`
- Ofullständig parameter information:`turn it {OnOff}`
- Ingen parameter information:`turn something`

Exempel på meningar som har olika mängder information gör att de anpassade kommandona kan matcha både ensidiga lösningar och flera lösningar med partiell information.

Med det i åtanke redigerar du exempel meningarna så att de använder de parametrar som föreslås här:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> I exempel menings redigeraren använder du klamrar för att referera till parametrarna: `turn {OnOff} the {SubjectDevice}` .
>
> Använd fliken för automatisk komplettering som definierats av parametrarna som skapats tidigare.

## <a name="add-parameters-to-completion-rules"></a>Lägg till parametrar till regler för slut för ande

Ändra regeln för slut för ande som du skapade i [föregående snabb start](./quickstart-custom-speech-commands-create-new.md).

1. I avsnittet **villkor** väljer du **Lägg till ett villkor**.
1. I fönstret **nytt villkor** väljer du **nödvändiga parametrar**i listan **typ** . I listan väljer du både **mikrofonen** och **SubjectDevice**.
1. Välj **Skapa**.
1. I avsnittet **åtgärder** redigerar du den befintliga **svars åtgärden skicka tal** genom att hovra över åtgärden och välja knappen Redigera. Den här gången använder du de `OnOff` nya `SubjectDevice` parametrarna och:

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Prova nu
1. Välj **träna** överst i den högra rutan.

1. När träningen är färdig väljer du **test**.
    
    Ett **test av** programfönstret visas.

1. Prova några interaktioner.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabb start: Använd anpassade kommandon med anpassad röst (förhands granskning)](./quickstart-custom-speech-commands-select-custom-voice.md)
