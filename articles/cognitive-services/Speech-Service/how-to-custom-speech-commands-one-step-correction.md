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
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456458"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Gör så här: Lägg till en steg korrigering i ett anpassat kommando (förhands granskning)

I den här artikeln får du lära dig hur du lägger till ett-steg-bekräftelse till ett kommando.

En steg korrigering används för att uppdatera ett kommando som precis har slutförts.

Dvs. om du precis har skapat ett larm kan du ändra ditt sinne och uppdatera tiden för larmet.

- Inmatade: Ställ in alarm i morgon kl. 12.00
- Utdata: "OK, alarm set för 12/06/2019 12:00:00"
- Inmatade: Nej, imorgon vid 1pm
- Utdata: "OK

Tänk på att det innebär att du som utvecklare har en mekanism för att uppdatera alarmet i Server dels programmet.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:

- [Snabb start: skapa ett anpassat kommando (förhands granskning)](./quickstart-custom-speech-commands-create-new.md)
- [Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)
- [Gör så här: Lägg till en bekräftelse till ett anpassat kommando (förhands granskning)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Lägg till de avancerade reglerna för en steg korrigering 

För att demonstrera en steg korrigering ska vi utöka **SetAlarm** -kommandot som skapats i [bekräftelser](./how-to-custom-speech-commands-confirmations.md).
 
1. Lägg till en avancerad regel för att uppdatera föregående larm. 

    Den här regeln uppmanar användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (Ja/Nej) för nästa turn.

   | Inställning               | Föreslaget värde                                                  | Beskrivning                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Uppdatera tidigare larm                                            | Ett namn som beskriver syftet med regeln          |
   | Villkor            | UpdateLastCommand & obligatorisk parameter-DateTime                | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | SpeechResponse-"-uppdaterar tidigare alarm till {DateTime}"       | Den åtgärd som ska vidtas när regel villkoret är sant |
   | Tillstånd efter körning | Slutför kommando                                                 | Användarens tillstånd efter aktivering                   |

1. Flytta regeln som du nyss skapade överst i avancerade regler (rulla över regeln i panelen och klicka på uppåtpilen).
   > [!div class="mx-imgBorder"]
   > ![lägga till ett intervall verifierings](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> I ett verkligt program, i avsnittet åtgärder i den här regeln, skickar du även en aktivitet till klienten eller anropar en HTTP-slutpunkt för att uppdatera larmet i systemet.

## <a name="try-it-out"></a>Prova

Välj panelen test och prova några interaktioner.

- Inmatade: Ställ in alarm i morgon kl. 12.00
- Utdata: "är du säker på att du vill ställa in ett larm för 12/07/2019 12:00:00?"
- Inmatade: Ja
- Utdata: "OK, alarm set för 12/07/2019 12:00:00"
- Inmatade: Nej, imorgon vid 1pm
- Utdata: "uppdaterar föregående alarm till 12/07/2019 13:00:00"
