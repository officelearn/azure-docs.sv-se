---
title: Distribuera en modell för anpassat tal – Speech Services
titlesuffix: Azure Cognitive Services
description: I det här dokumentet lär du dig att skapa och distribuera en slutpunkt med hjälp av portalen för anpassat tal.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025876"
---
# <a name="deploy-a-custom-model"></a>Distribuera en anpassad modell

När du har laddat upp och granskas data, utvärderas Precision och tränas en anpassad modell, kan du distribuera en anpassad slutpunkt som ska användas med dina appar, verktyg och produkter. I det här dokumentet lär du dig att skapa och distribuera en slutpunkt med hjälp av portalen för anpassat tal.

## <a name="create-a-custom-endpoint"></a>Skapa en anpassad slutpunkt

Om du vill skapa en ny anpassad slutpunkt, Välj **distribution** Custom Speech-menyn längst upp på sidan. Om det här är din första körning, ser du att det inte finns några slutpunkter som anges i tabellen. När du har skapat en slutpunkt, ska du använda den här sidan för att spåra varje distribuerade slutpunkten.

Välj sedan **Lägg till slutpunkt** och ange en **namn** och **beskrivning** för din anpassade slutpunkt. Välj sedan den anpassa modell som du vill associera med den här slutpunkten. Du kan också aktivera loggning för från den här sidan. Loggning kan du övervaka trafiken för slutpunkten. Om du inaktiverar lagras inte trafik.

![Så här distribuerar du en modell](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Glöm inte att godkänna villkoren för användning och prisinformation.

Välj sedan **skapa**. Den här åtgärden tillbaka till den **distribution** sidan. Tabellen innehåller nu en post som motsvarar din anpassade slutpunkt. Status för den slutpunkt visar det aktuella tillståndet. Det kan ta upp till 30 minuter att skapa en instans av en ny slutpunkt med hjälp av anpassade modeller. När status för distributionen ändras till **Slutför**, slutpunkten är klart att användas.

När slutpunkten har distribuerats visas namnet på slutpunkten som en länk. Klicka på länken om du vill visa information som är specifik för din slutpunkt, som slutpunktsnyckeln, slutpunkts-URL och exempelkod.

## <a name="view-logging-data"></a>Visa data för loggning

Loggningsdata finns att hämta under **Endpoint > information**.

## <a name="next-steps"></a>Nästa steg

* Använd din anpassade slutpunkt med den [tal SDK](speech-sdk.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
