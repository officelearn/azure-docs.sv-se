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
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307964"
---
# <a name="add-interaction-rules"></a>Lägg till interaktions regler

I den här artikeln får du lära dig om **interaktions regler**. Detta är ytterligare regler för att hantera mer detaljerade eller komplexa situationer. Även om du är kostnads fri att skapa egna anpassade interaktions regler, i den här artikeln, använder du interaktions regler för följande mål scenarier:

* Bekräfta kommandon
* Lägga till en steg korrigering i kommandon

Gå till avsnittet [referenser](./custom-commands-references.md) om du vill lära dig mer om interaktions regler.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:
> [!div class="checklist"]
> * [Så här: skapa program med enkla kommandon](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Gör så här: lägga till parametrar till kommandon](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Lägg till bekräftelser i ett kommando

Om du vill lägga till en bekräftelse använder du kommandot **SetTemperature** . För att få en bekräftelse skapar du interaktions regler med hjälp av följande steg.

1. Välj kommandot **SetTemperature** i det vänstra fönstret.
2. Lägg till interaktions regler genom att välja **Lägg till** i den mellersta rutan och sedan välja **interaktions regler**  >  **Bekräfta kommando**.

    Detta lägger till tre interaktions regler den här regeln uppmanar användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (Ja/Nej) för nästa turn.

    1. Ändra den **bekräftade kommando** interaktions regeln enligt följande konfiguration
        1. Byt **namn** till **`Confirm Temperature`** .
        1. Lägg till en ny villkors **parameter som krävs > temperatur**
        1. Lägg till en ny åtgärd som- **typ > skicka tal svar `Are you sure you want to set the temperature as {Temperature} degrees?` >**
        1. Lämna standardvärdet för att **Bekräfta bekräftelse från användaren** i förväntningar-avsnittet.
      
         > [!div class="mx-imgBorder"]
         > ![Skapa nödvändigt parameter svar](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Ändra **bekräftelse regeln lyckades** för att hantera en lyckad bekräftelse (användaren säger ja).
      
          1. Ändra **namn** till **`Confirmation temperature succeeded`** .
          1. Lämna den redan befintliga **bekräftelsen lyckades** .
          1. Lägg till en ny villkors **typ > nödvändiga parametrar > temperatur**
          1. Lämna standardvärdet för **efter körnings tillstånd** som **regler för att köra slut för ande**.

    1. Ändra **bekräftelsen nekad** (användaren sade nej) för att hantera scenarier när bekräftelse nekas.

          1. Ändra **namn** till **`Confirmation temperature denied`** .
          1. Lämna den redan befintliga **bekräftelsen nekades** .
          1. Lägg till en ny villkors **typ > nödvändiga parametrar > temperatur**
          1. Lägg till en ny åtgärd som- **typ > skicka tal svar `No problem. What temperature then?` >**
          1. Lämna standardvärdet för **post-Execution-tillstånd** som **väntar på användarens indata**.

> [!IMPORTANT]
> I den här artikeln har du använt den inbyggda bekräftelse funktionen. Du kan också få samma genom att lägga till interaktions reglerna en i taget, manuellt.
   

### <a name="try-out-the-changes"></a>Prova ändringarna

Välj **träna**, vänta tills träningen är klar och välj **testa**.

- Inmatade: Ange temperatur till 80 grader
- Utdata: OK 80?
- Inmatade: Nej
- Utdata: inga problem. vilken temperatur sedan?
- Inmatade: 83 grader
- Utdata: OK 83?
- Inmatade: Ja
- Utdata: OK, ställa in temperatur på 83 grader


## <a name="implementing-corrections-in-a-command"></a>Implementera korrigeringar i ett kommando

I det här avsnittet konfigurerar du en-steg-korrigering, som används när åtgärden redan har körts. Du ser också ett exempel på hur korrigeringen är aktive rad som standard om kommandot inte är uppfyllt ännu. Lägg till en ny parameter **AlarmTone**om du vill lägga till en korrigering när kommandot inte har slutförts.

Välj kommandot **SetAlarm** i det vänstra fönstret och Lägg till en ny parameter **AlarmTone**.
        
- **Namn** > `AlarmTone`
- **Ange** > sträng
- **Standardvärde** > `Chimes`
- **Konfigurations** > acceptera fördefinierade indatavärden från intern katalog
- **Fördefinierade indatavärden**  >  `Chimes` , `Jingle` och `Echo` . Var och en som enskilda fördefinierade indata.


Uppdatera sedan svaret för DateTime-parametern till `Ready to set alarm with tone as {AlarmTone}. For what time?` . Ändra sedan regeln för slut för Ande enligt följande.

1. Välj den befintliga regeln för slut för ande **ConfirmationResponse**.
1. Hovra över den befintliga åtgärden i den högra panelen och välj knappen **Redigera** .
1. Uppdatera tal svar till`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>Prova ändringarna

Välj **träna**, vänta tills träningen är klar och välj **testa**.
Prova följande yttranden:

- Inmatade: Ställ in ett larm
- Utdata: redo att ställa in alarm med tonen som CHIMES. För vilken tid?
- Inmatade: Ställ in ett larm med tonen som Jingle för 9 am imorgon
- Output: OK, alarm anges för 2020-05-30 09:00:00. Alarm tonen är Jingle.

> [!IMPORTANT]
> Observera hur alarm tonen kan ändras utan någon explicit konfiguration i ett pågående kommando, d.v.s. När kommandot inte slutförts ännu. **Korrigering är aktive rad som standard för alla kommando parametrar, oavsett vilket antal som finns om kommandot ännu är uppfyllt.**

### <a name="correction-when-command-is-completed"></a>Korrigering när kommandot har slutförts

Plattform för anpassade kommandon ger också möjlighet att utföra en stegs korrigering även när kommandot har slutförts. Men den här funktionen är inte aktive rad som standard och måste konfigureras uttryckligen. Använd följande steg för att konfigurera en-steg korrigering.

1. I kommandot **SetAlarm** lägger du till en interaktions regel av typen **Uppdatera föregående** för att uppdatera det tidigare angivna larmet. Byt namn på standard **namnet** för interaktions regeln för att **uppdatera tidigare larm**.
1. Lämna standard villkoret **föregående-kommandot måste uppdateras** i befintligt skick.
1.  Lägg till ett nytt villkor som **> obligatorisk Parameter > datetime**.
1. Lägg till en ny åtgärd som **typ > skicka tal svar > enkelt redigerings `Updating previous alarm time to {DateTime}.` program >**
1. Lämna standardvärdet för post-körningen som **kommandot har slutförts**.

### <a name="try-out-the-changes"></a>Prova ändringarna

Välj **träna**, vänta tills träningen är klar och välj **testa**.

- Inmatade: Ställ in ett larm
- Utdata: redo att ställa in alarm med tonen som CHIMES. Vilken tid?
- Inmatade: Ställ in ett larm med tonen som Jingle för 9 am imorgon
- Output: OK, alarm anges för 2020-05-21 09:00:00. Alarm tonen är Jingle.
- Inmatade: Nej, 8 am
- Utdata: uppdaterar föregående larm tid till 2020-05-29 08:00.

> [!NOTE]
> I ett verkligt program, i avsnittet åtgärder i den här korrigerings regeln, måste du också skicka tillbaka en aktivitet till klienten eller anropa en HTTP-slutpunkt för att uppdatera larm tiden i systemet. Den här åtgärden bör vara helt ansvarig för att bara uppdatera alarm tiden och inte andra attribut för kommandot, i det här fallet alarm tonen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: Lägg till mallar för språk skapande för tal svar](./how-to-custom-commands-add-language-generation-templates.md)