---
title: 'Gör så här: Lägg till en bekräftelse till ett anpassat kommando'
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du implementerar bekräftelser för ett kommando i anpassade kommandon.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: f37109cc2677ad5ef18c5677bda9308a78cebccf
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851311"
---
# <a name="add-interaction-rules"></a>Lägga till interaktionsregler

I den här artikeln får du lära dig om *interaktions regler*. Dessa ytterligare regler hanterar mer detaljerade eller komplexa situationer. Du kan skapa egna anpassade interaktions regler, men i den här artikeln använder du interaktions regler för följande mål scenarier:

* Bekräfta kommandon
* Lägga till en enstaka stegs korrigering i kommandon

Om du vill veta mer om interaktions regler går du till avsnittet [referenser](./custom-commands-references.md) .

## <a name="prerequisites"></a>Krav

Du måste ha utfört stegen i följande artiklar:
> [!div class="checklist"]
> * [Skapa program med enkla kommandon](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Lägga till parametrar till kommandon](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Lägg till bekräftelser i ett kommando

Om du vill lägga till en bekräftelse använder du kommandot **SetTemperature** . För att få en bekräftelse skapar du interaktions regler med hjälp av följande steg.

1. Välj kommandot **SetTemperature** i det vänstra fönstret.
1. Lägg till interaktions regler genom att välja **Lägg till** i fönstret i mitten. Välj sedan **interaktions regler**  >  **Bekräfta kommando**.

    Den här åtgärden lägger till tre interaktions regler. Den här regeln uppmanar användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (Ja/Nej) för nästa turn.

    1. Ändra den **bekräftade kommando** interaktions regeln enligt följande konfiguration:
        1. Byt namn på **namn** för att **Bekräfta temperatur**.
        1. Lägg till ett nytt villkor som temperatur för **obligatoriska parametrar**  >  **Temperature**.
        1. Lägg till en ny åtgärd som **typ**  >  **Skicka tal svar**  >  **är du säker på att du vill ställa in temperaturen som {temperatur} grader?**
        1. Lämna standardvärdet för att **Bekräfta bekräftelse från användaren** i **förväntningar** -avsnittet.
      
         > [!div class="mx-imgBorder"]
         > ![Skapa nödvändigt parameter svar](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Ändra **bekräftelse regeln lyckades** för att hantera en lyckad bekräftelse (användaren säger ja).
      
          1. Ändring av **namn** till **bekräftelse temperatur lyckades**.
          1. Lämna den redan befintliga **bekräftelsen lyckades** .
          1. Lägg till ett nytt villkor som **typ**  >  **obligatoriska parametrar**  >  **temperatur**.
          1. Lämna standardvärdet för **efter körnings tillstånd** som **regler för att köra slut för ande**.

    1. Ändra bekräftelse regeln för **nekad bekräftelse** för att hantera scenarier när en bekräftelse nekas (användaren har nej).

          1. Ändra **namn** till **bekräftelse temperatur nekad**.
          1. Lämna den redan befintliga **bekräftelsen nekades** .
          1. Lägg till ett nytt villkor som **typ**  >  **obligatoriska parametrar**  >  **temperatur**.
          1. Lägg till en ny åtgärd som **typ**  >  **Skicka tal svar**  >  **inget problem. Vilken temperatur sedan?**
          1. Lämna standardvärdet för **post-Execution-tillstånd** som **väntar på användarens indata**.

> [!IMPORTANT]
> I den här artikeln har du använt den inbyggda bekräftelse funktionen. Du kan också lägga till interaktions regler manuellt en i taget.
   

### <a name="try-out-the-changes"></a>Prova ändringarna

Välj **träna**, vänta tills utbildningen är klar och välj **test**.

- **Inmatade**: Ange temperatur till 80 grader.
- **Utdata**: OK 80?
- **Inmatade**: Nej.
- **Utdata**: inga problem. Vilken temperatur sedan?
- **Inmatade**: 83 grader.
- **Utdata**: OK 83?
- **Inmatade**: Ja.
- **Utdata**: OK, ställer in temperatur på 83 grader.


## <a name="implement-corrections-in-a-command"></a>Implementera korrigeringar i ett kommando

I det här avsnittet konfigurerar du en steg korrigering som används när åtgärden redan har körts. Du ser också ett exempel på hur en korrigering aktive ras som standard om kommandot inte är uppfyllt ännu. Lägg till en korrigering när kommandot inte är slutfört genom att lägga till den nya parametern **AlarmTone**.

Välj kommandot **SetAlarm** i det vänstra fönstret och Lägg till den nya parametern **AlarmTone**.
        
- **Namn**  >  **AlarmTone**
- **Typ**  >  **Sträng**
- **Standardvärde**  >  **CHIMES**
- **Konfiguration**  >  **Acceptera fördefinierade indatavärden från den interna katalogen**
- **Fördefinierade indatavärden**  >  **CHIMES**, **Jingle**och **ECHO** som enskilda fördefinierade indata


Uppdatera sedan svaret för **datetime** -parametern till **redo att ställa in alarm med tonen som {AlarmTone}. För vilken tid?**. Ändra sedan regeln för slut för Ande enligt följande:

1. Välj den befintliga regeln för slut för ande **ConfirmationResponse**.
1. Hovra över den befintliga åtgärden i den högra rutan och välj **Redigera**.
1. Uppdatera tal svaret till **OK, alarm inställt för {DateTime}. Alarm tonen är {AlarmTone}.**

### <a name="try-out-the-changes"></a>Prova ändringarna

Välj **träna**, vänta tills utbildningen är klar och välj **test**.
Prova följande yttranden:

- **Inmatade**: Ange ett larm.
- **Utdata**: redo att ställa in alarm med tonen som CHIMES. För vilken tid?
- **Inmatade**: Ställ in ett larm med tonen som Jingle för 9 am imorgon.
- **Output**: OK, alarm anges för 2020-05-30 09:00:00. Alarm tonen är Jingle.

> [!IMPORTANT]
> Alarm tonen kan ändras utan någon explicit konfiguration i ett pågående kommando, till exempel när kommandot inte har slutförts än. *En korrigering är aktive rad som standard för alla kommando parametrar, oavsett vilket antal som finns om kommandot ännu är uppfyllt.*

### <a name="correction-when-command-is-completed"></a>Korrigering när kommandot har slutförts

Plattformen för anpassade kommandon ger också möjlighet att utföra en enstaka stegs korrigering även när kommandot har slutförts. Den här funktionen är inte aktive rad som standard. Det måste konfigureras explicit. Använd följande steg för att konfigurera en korrigering i ett steg.

1. I kommandot **SetAlarm** lägger du till en interaktions regel av typen **Uppdatera föregående-kommando** för att uppdatera det tidigare angivna larmet. Byt namn på standard **namnet** för interaktions regeln för att **uppdatera tidigare larm**.
1. Lämna standard villkoret **föregående-kommandot måste uppdateras** i befintligt skick.
1. Lägg till ett nytt villkor **som**  >  **obligatorisk parameter för parametern**  >  **datetime**.
1. Lägg till en ny åtgärd som **typ**  >  **Skicka Speech Response**  >  **Simple Editor**  >  **uppdaterar föregående alarm tid till {DateTime}.**
1. Lämna standardvärdet för **post-Execution-tillstånd** när **kommandot har slutförts**.

### <a name="try-out-the-changes"></a>Prova ändringarna

Välj **träna**, vänta tills utbildningen är klar och välj **test**.

- **Inmatade**: Ange ett larm.
- **Utdata**: redo att ställa in alarm med tonen som CHIMES. För vilken tid?
- **Inmatade**: Ställ in ett larm med tonen som Jingle för 9 am imorgon.
- **Output**: OK, alarm anges för 2020-05-21 09:00:00. Alarm tonen är Jingle.
- **Inmatade**: Nej, 8.
- **Utdata**: uppdaterar föregående larm tid till 2020-05-29 08:00.

> [!NOTE]
> I ett verkligt program, i avsnittet **åtgärder** i den här korrigerings regeln, måste du också skicka tillbaka en aktivitet till klienten eller anropa en http-slutpunkt för att uppdatera larm tiden i systemet. Den här åtgärden bör enbart vara ansvarig för att uppdatera alarm tiden och inte andra attribut för kommandot. I det här fallet är det en larm signal.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: Lägg till mallar för språk skapande för tal svar](./how-to-custom-commands-add-language-generation-templates.md)
