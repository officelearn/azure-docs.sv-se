---
title: 'Gör så här: Lägg till en steg korrigering i ett anpassat kommando (för hands version) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklarar vi hur du implementerar en-steg-korrigering för ett kommando i anpassade kommandon.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858265"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Gör så här: Lägg till en steg korrigering i ett anpassat kommando (förhands granskning)

I den här artikeln får du lära dig hur du lägger till ett-steg-bekräftelse till ett kommando.

En steg korrigering används för att uppdatera ett kommando som precis har slutförts. Det innebär att om du precis har skapat ett larm kan du ändra ditt sinne och uppdatera tiden för larmet. Ett exempel på sådana fall är följande:

- Inmatade: Ställ in alarm i morgon kl. 12.00
- Utdata: OK, alarm set för 2020-05-02 12:00:00
- Inmatade: Nej, imorgon vid 1pm
- Utdata: OK

Ett verkligt världs scenario, som vanligt vis åtföljs av en klient som kör en åtgärd som ett resultat av kommando kompletteringen – i den här artikeln förutsätter vi att du som utvecklare har en mekanism för att uppdatera larmet i Server delen.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:
> [!div class="checklist"]

> * [Snabb start: skapa ett anpassat kommando (förhands granskning)](./quickstart-custom-speech-commands-create-new.md)
> * [Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Gör så här: Lägg till en bekräftelse till ett anpassat kommando (förhands granskning)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Lägg till interaktions regler för en steg korrigering 

För att demonstrera en steg korrigering ska vi utöka **SetAlarm** -kommandot som skapats i [How to: Add a Confirm to Custom Command (för hands version)](./how-to-custom-speech-commands-confirmations.md).
1. Lägg till en interaktions regel för att uppdatera det tidigare inställda larmet. 

    Den här regeln uppmanar användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (Ja/Nej) för nästa turn.

   | Inställningen               | Föreslaget värde                                                  | Beskrivning                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Uppdatera tidigare larm                                            | Ett namn som beskriver syftet med regeln          |
   | Villkor            | Föregående kommando måste uppdateras & obligatorisk parameter-> DateTime                | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | Skicka Speech Response-> Simple Editor-> uppdatera föregående alarm till {DateTime}      | Den åtgärd som ska vidtas när regel villkoren är sanna |
   | Efter körnings tillstånd | Kommandot har slutförts        | Användarens tillstånd efter aktivering                   |

1. Flytta regeln som du nyss skapade till toppen av interaktions reglerna (Välj regeln i panelen och klicka på uppåtpilen som finns under `...` ikonen överst i den mellersta rutan).
   > [!div class="mx-imgBorder"]
   > ![Lägg till en intervall validering](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > I ett verkligt program, i avsnittet åtgärder i den här regeln, skickar du även en aktivitet till klienten eller anropar en HTTP-slutpunkt för att uppdatera larmet i systemet.

## <a name="try-it-out"></a>Prova nu

Välj `Train`, vänta på att utbildningen är klart `Test`och välj.

- Inmatade: Ställ in alarm i morgon kl. 12.00
- Utdata: är du säker på att du vill ställa in ett larm för 2020-05-02 12:00:00
- Inmatade: Ja
- Utdata: OK, alarm set för 2020-05-02 12:00:00
- Inmatade: Nej, imorgon vid 2pm
- Utdata: uppdaterar föregående alarm till 2020-05-02 14:00:00
