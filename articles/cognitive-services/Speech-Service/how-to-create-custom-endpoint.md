---
title: Skapa en anpassad slutpunkt med Speech Service på Azure | Microsoft Docs
description: Lär dig hur du skapar en anpassad tal till text-slutpunkt med Speech-tjänsten i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: d06dbc399a3610982c99a548373482fc5ec21f4d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852887"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Skapa en anpassad slutpunkt för till tal till text

När du har skapat anpassade akustiska modeller eller språkmodeller, kan du distribuera dem i en anpassad tal till text-slutpunkt. 

## <a name="create-an-endpoint"></a>Skapa en slutpunkt
Om du vill skapa en ny anpassad slutpunkt, Välj **slutpunkter** på den **Custom Speech** menyn längst upp på sidan. Den här åtgärden tar dig till den **slutpunkter** sidan, som innehåller en tabell med aktuella anpassade slutpunkter. Om du inte har skapat några slutpunkter är tabellen tom. Den aktuella nationella inställningen visas i tabellrubriken. 

Om du vill skapa en distribution för ett annat språk, Välj **ändra språket**. Mer information om språk som stöds.

Om du vill skapa en ny slutpunkt, Välj **Skapa ny**. I den **Create Endpoint** fönstret anger du informationen i den **namn** och **beskrivning** rutorna för anpassade distributionen.

I den **prenumeration** kombinationsrutan väljer du den prenumeration som du vill använda. Om det är en S0 (betalande)-prenumeration kan du välja samtidiga begäranden.

Du kan också välja om innehållsloggning växlas eller inaktivera. Det vill säga markerar du om slutpunkten trafiken lagras. Om den inte är markerad ska lagra trafiken ignoreras. För alla loggat innehåll hittar du nedladdningslänkarna under Endpoint -> detaljer

I den **akustisk modell** väljer du den akustiska modellen som du vill, och i den **språkmodell** väljer språkmodellen som du vill. Alternativen för- och språkdata modeller är alltid de grundläggande Microsoft-modellerna. Valet av basmodellen begränsar kombinationerna. Du kan inte blanda konversationsanpassade grundläggande modeller med sökning och bestämmer grundläggande modeller.

> [!NOTE]
> Glöm inte att acceptera villkoren för användning och information om priser genom att välja kryssrutan.
>

När du har valt dina modeller för- och språkdata, Välj **skapa**. Den här åtgärden tillbaka till den **distributioner** sidan. Tabellen innehåller nu en post som motsvarar din nya slutpunkt. Status för den slutpunkt visar det aktuella tillståndet, medan det skapas. Det kan ta upp till 30 minuter att skapa en instans av en ny slutpunkt med anpassade modeller. När statusen för distributionen är *Slutför*, slutpunkten är redo att användas.

När distributionen är klar, blir en länk i namnet på slutpunkten. Att välja länken visar den **slutpunktsinformation** sidan som visar URL: er för din anpassade slutpunkt som ska användas med antingen en HTTP-begäran eller Microsoft Cognitive Services Talklientbiblioteket, som använder webbsockets.

## <a name="next-steps"></a>Nästa steg

Fler självstudier finns i:
- [Hämta en kostnadsfri utvärderingsprenumeration på Speech-tjänsten](https://azure.microsoft.com/try/cognitive-services/)
- [Skapa anpassad akustisk modell](how-to-customize-acoustic-models.md)
- [Skapa anpassat språkmodell](how-to-customize-language-model.md)
