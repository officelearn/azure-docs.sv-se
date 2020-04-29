---
title: Distribuera en modell för tjänsten Custom Speech tal
titleSuffix: Azure Cognitive Services
description: I det här dokumentet får du lära dig hur du skapar och distribuerar en slut punkt med hjälp av Custom Speech portalen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 9d48f09fe5d5c736f65b6a76211dd3fec85479ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402183"
---
# <a name="deploy-a-custom-model"></a>Distribuera en anpassad modell

När du har laddat upp och inspekterat data, utvärderat noggrannhet och tränat en anpassad modell, kan du distribuera en anpassad slut punkt för användning med dina appar, verktyg och produkter. I det här dokumentet får du lära dig hur du skapar och distribuerar en slut punkt med hjälp av [Custom Speech portalen](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Skapa en anpassad slut punkt

Om du vill skapa en ny anpassad slut punkt loggar du in på [Custom Speech Portal](https://speech.microsoft.com/customspeech) och väljer **distribution** på Custom Speech menyn överst på sidan. Om det här är din första körning ser du att det inte finns några slut punkter som visas i tabellen. När du har skapat en slut punkt använder du den här sidan för att spåra varje distribuerad slut punkt.

Välj sedan **Lägg till slut punkt** och ange ett **namn** och en **Beskrivning** för din anpassade slut punkt. Välj sedan den anpassade modell som du vill koppla till den här slut punkten. På den här sidan kan du också aktivera loggning. Med loggning kan du övervaka slut punkts trafik. Om den är inaktive rad lagras inte trafiken.

![Så här distribuerar du en modell](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Glöm inte att acceptera villkoren för användning och pris information.

Välj sedan **skapa**. Den här åtgärden återgår till **distributions** sidan. Tabellen innehåller nu en post som motsvarar din anpassade slut punkt. Slut punktens status visar dess aktuella tillstånd. Det kan ta upp till 30 minuter att instansiera en ny slut punkt med hjälp av dina anpassade modeller. När distributions statusen ändras till **slutförd**, är slut punkten redo att användas.

När slut punkten har distribuerats visas slut punktens namn som en länk. Klicka på länken om du vill visa information som är speciell för din slut punkt, till exempel slut punkts nyckel, slut punkts-URL och exempel kod.

## <a name="view-logging-data"></a>Visa loggnings data

Loggnings data är tillgängliga för hämtning under **slut punkt > information**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du använder din anpassade modell [här](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbered och testa dina data](how-to-custom-speech-test-data.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
