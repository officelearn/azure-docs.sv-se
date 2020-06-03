---
title: Lägg till en steg korrigering i för hands versionen av anpassade kommandon – tal service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du lägger till en enstaka stegs korrigering för ett kommando i en för hands version av anpassade kommandon.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310435"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Lägga till en steg korrigering i ett anpassat kommando i ett för hands versions program för anpassade kommandon

I den här artikeln får du lära dig hur du lägger till en-steg-bekräftelse till ett kommando i en för hands version av anpassade kommandon.

En steg korrigering används för att uppdatera ett kommando som precis har slutförts. När du lägger till en steg korrigering i ett alarm kommando kan du ändra vad du vill och uppdatera tiden för larmet. Till exempel:

- Inmatade: Ställ in alarm i morgon kl. 12.00
- Utdata: OK, alarm set för 2020-05-02 12:00:00
- Inmatade: Nej, imorgon vid 1pm
- Utdata: OK

> [!NOTE]
> I ett verkligt scenario kör klienten en åtgärd som ett resultat av slut för ande av kommando. I den här artikeln förutsätter vi att du har en mekanism för att uppdatera alarmet i Server dels programmet.

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i följande artiklar:
> [!div class="checklist"]

> * [Snabb start: skapa en anpassad kommando förhands gransknings app](./quickstart-custom-speech-commands-create-new.md)
> * [Snabb start: skapa en anpassad kommando förhands gransknings app med parametrar](./quickstart-custom-speech-commands-create-parameters.md)
> * [Gör så här: Lägg till bekräftelser i ett kommando i en för hands version av anpassade kommandon](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Lägg till interaktions regler för en steg korrigering

Om du vill visa en steg korrigering utökar du kommandot **SetAlarm** som du skapade i [How to: Lägg till en bekräftelse till ett kommando i för hands versionen av anpassade kommandon](./how-to-custom-speech-commands-confirmations.md).

1. Lägg till en interaktions regel för att uppdatera **SetAlarm** -kommandot.

    Den här regeln uppmanar användaren att bekräfta datum och tid för larmet och förväntar sig en bekräftelse (Ja/Nej) för nästa turn.

   | Inställning               | Föreslaget värde                                                  | Description                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Regel namn**             | **Uppdatera tidigare larm**                                            | Ett namn som beskriver syftet med regeln          |
   | **Villkor**            | **Föregående kommando måste uppdateras & obligatorisk parameter-> DateTime**                | Villkor som avgör när regeln kan köras    |   
   | **Åtgärder**               | **Skicka Speech Response-> Simple Editor-> uppdatera föregående alarm till {DateTime}**      | Den åtgärd som ska vidtas när regel villkoren är sanna |
   | **Efter körnings tillstånd** | **Kommandot har slutförts**        | Användarens tillstånd efter aktivering                   |

1. I fönstret väljer du den interaktions regel som du nyss skapade. Välj knappen med tre punkter (**...**) i det övre vänstra hörnet i fönstret. Använd sedan pilen **Flytta uppåt** för att flytta regeln överst i listan **interaktions regler** .
   > [!div class="mx-imgBorder"]
   > ![Lägg till en intervall validering](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > I ett verkligt program använder du avsnittet **åtgärder** för att skicka tillbaka en aktivitet till klienten eller anropa en http-slutpunkt för att uppdatera larmet i systemet.

## <a name="try-it-out"></a>Prova nu

1. Välj **träna**.

1. När utbildningen är färdig väljer du **test**och försöker sedan utföra följande interaktioner:

   - Inmatade: Ställ in alarm i morgon kl. 12.00
   - Utdata: är du säker på att du vill ställa in ett larm för 2020-05-02 12:00:00
   - Inmatade: Ja
   - Utdata: OK, alarm set för 2020-05-02 12:00:00
   - Inmatade: Nej, imorgon vid 2pm
   - Utdata: uppdaterar föregående alarm till 2020-05-02 14:00:00
