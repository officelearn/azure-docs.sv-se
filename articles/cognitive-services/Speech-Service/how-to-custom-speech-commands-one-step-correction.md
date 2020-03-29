---
title: Så här lägger du till en korrigering i ett steg i ett anpassat kommando (förhandsversion) – taltjänst
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklarar vi hur du implementerar korrigeringar i ett steg för ett kommando i anpassade kommandon.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456458"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Så här lägger du till en korrigering i ett steg i ett anpassat kommando (förhandsgranskning)

I den här artikeln får du lära dig hur du lägger till en stegbekräftelse i ett kommando.

Enstegskorrigering används för att uppdatera ett kommando som just har slutförts.

Dvs om du bara ställa in ett larm, kan du ändra dig och uppdatera tiden för larmet.

- Ingång: Ställ in larm för i morgon vid lunchtid
- Utgång: "Ok, larm inställt på 12/06/2019 12:00:00"
- Ingång: Nej, i morgon kl 13:00
- Utgång: "Ok

Tänk på att detta innebär att du som utvecklare har en mekanism för att uppdatera alarmet i backend-programmet.

## <a name="prerequisites"></a>Krav

Du måste ha slutfört stegen i följande artiklar:

- [Snabbstart: Skapa ett anpassat kommando (förhandsgranskning)](./quickstart-custom-speech-commands-create-new.md)
- [Snabbstart: Skapa ett anpassat kommando med parametrar (förhandsgranskning)](./quickstart-custom-speech-commands-create-parameters.md)
- [Så här lägger du till en bekräftelse i ett anpassat kommando (förhandsgranskning)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Lägga till avancerade regler för korrigering i ett steg 

Om du vill visa enstegskorrigering ska vi utöka kommandot **SetAlarm** som skapats i [bekräftelserna hur du gör](./how-to-custom-speech-commands-confirmations.md).
 
1. Lägg till en avancerad regel för att uppdatera föregående larm. 

    Denna regel kommer att be användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (ja / nej) för nästa tur.

   | Inställning               | Föreslaget värde                                                  | Beskrivning                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Regelnamn             | Uppdatera tidigare larm                                            | Ett namn som beskriver syftet med regeln          |
   | Villkor            | UpdateLastCommand & obligatorisk parameter - DateTime                | Villkor som avgör när regeln kan köras    |   
   | Åtgärder               | SpeechResponse - "- Uppdatera föregående larm till {DateTime}"       | Åtgärden som ska vidtas när regelvillkoret är sant |
   | Tillstånd efter körning | Kommandot Slutför                                                 | Användarens tillstånd efter svängen                   |

1. Flytta regeln som du just skapade högst upp i avancerade regler (rulla över regeln på panelen och klicka på UPP-pilen).
   > [!div class="mx-imgBorder"]
   > ![Lägga till en områdesverifiering](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> I ett verkligt program skickar du i avsnittet Åtgärder i den här regeln också tillbaka en aktivitet till klienten eller anropar en HTTP-slutpunkt för att uppdatera alarmet i systemet.

## <a name="try-it-out"></a>Prova det

Välj testpanelen och prova några interaktioner.

- Ingång: Ställ in larm för i morgon vid lunchtid
- Utdata: "Vill du ställa in ett larm för 12/07/2019 12:00:00?"
- Ingång: Ja
- Utgång: "Ok, larm inställt på 12/07/2019 12:00:00"
- Ingång: Nej, i morgon kl 13:00
- Utgång: "Uppdatera tidigare larm till 2019-12-07 13:00:00"
