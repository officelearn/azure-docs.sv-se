---
title: Hur du loggar in dialogrutor i en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om att logga dialogrutor i en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 74ee04fb3d4044573a98535a9bdc26d5c593a222
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796908"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Hur du loggar in dialogrutor i en Konversationsdeltagare-modell

Den här kursen visar hur Log-dialogrutor utnyttjas för att skapa bättre Konversationsdeltagare modeller från registrerade interaktioner med verkliga användare.

## <a name="video"></a>Video

[![Självstudiekursen förhandsversionen av log dialogrutor](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

och väder-modellen som skapats i tidigare självstudier.

## <a name="details"></a>Information
Log dialoger är inspelade loggarna för din robot interaktion med slutanvändarna. Du kan åtgärda entitet etiketter och åtgärden val för att förbättra modellens prestanda och hela systemets prestanda med hjälp av dessa Log-dialogrutor.

## <a name="steps"></a>Steg

Klicka på ”Importera Tutorials” och välja modellen med namnet ”självstudien-11-LogDialogs” i webbgränssnittet.

Den här modellen innehåller en entitet med namnet ”stad” och åtgärder som utformats för att svara på förfrågningar om väder i den staden. Två träna-dialogrutor har använts för att träna modellen så att prestandaförväntningarna är något låga. Modellen skulle förbättra med ytterligare utbildning och få tillgång till verkliga användarinteraktioner.

### <a name="create-a-new-conversation"></a>Skapa en ny konversation

1. Klicka på ”Log-dialogrutor” och knappen ”Ny Log dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ange ”Austin väderprognos”
3. Klicka på knappen ”klar testning”.
4. Klicka på ”Austin väderprognos” log-dialogrutan i listan.
5. Klicka på ”Austin väderprognos”-uttryck på panelen chatt.
6. Klicka på ”Austin” och sedan på ”stad” från entitetslistan.
7. Klicka på knappen ”Skicka ändringar”.
    - Den här ändringen i värdet för entiteten ändras underordnade konversationen eftersom vi har nya entitetsvärden i minnet. Senare åtgärder har blivit ogiltig särskilt sådana som rör ”stad”-entitet.
8. Klicka på ”vilken stad”? uttryck i panelen chatt.
9. Välj svar ”vädret i Austin är förmodligen vackert”.
10. Klicka på knappen ”Spara som träna dialogrutan”.
    - Träning har startats omedelbart

Senaste en anteckning. Beroende på företagets behov, kan konversationsfunktion för loggning stängas av genom att gå till inställningar och avmarkera ”Log konversationer”.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Entiteten identifiering av återanrop](./12-entity-detection-callback.md)
