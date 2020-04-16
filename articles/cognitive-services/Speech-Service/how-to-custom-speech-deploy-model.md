---
title: Distribuera en modell för anpassad tal - taltjänst
titleSuffix: Azure Cognitive Services
description: I det här dokumentet får du lära dig hur du skapar och distribuerar en slutpunkt med hjälp av portalen Anpassat tal.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 9d48f09fe5d5c736f65b6a76211dd3fec85479ea
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402183"
---
# <a name="deploy-a-custom-model"></a>Distribuera en anpassad modell

När du har laddat upp och inspekterat data, utvärderat noggrannhet och tränat en anpassad modell kan du distribuera en anpassad slutpunkt som ska användas med dina appar, verktyg och produkter. I det här dokumentet får du lära dig hur du skapar och distribuerar en slutpunkt med hjälp av [portalen Anpassat tal](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Skapa en anpassad slutpunkt

Om du vill skapa en ny anpassad slutpunkt loggar du in på [portalen Anpassat tal](https://speech.microsoft.com/customspeech) och väljer **Distribution** på menyn Anpassat tal högst upp på sidan. Om det här är ditt första åk kommer du att märka att det inte finns några slutpunkter i tabellen. När du har skapat en slutpunkt använder du den här sidan för att spåra varje distribuerad slutpunkt.

Välj sedan **Lägg till slutpunkt** och ange ett **namn** och **en beskrivning** för den anpassade slutpunkten. Välj sedan den anpassade modell som du vill associera med den här slutpunkten. Från den här sidan kan du också aktivera loggning. Genom att logga kan du övervaka slutpunktstrafiken. Om trafiken är inaktiverad lagras den inte.

![Så här distribuerar du en modell](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Glöm inte att acceptera användarvillkoren och prisinformation.

Välj sedan **Skapa**. Den här åtgärden returnerar dig till **distributionssidan.** Tabellen innehåller nu en post som motsvarar din anpassade slutpunkt. Slutpunktens status visar dess aktuella tillstånd. Det kan ta upp till 30 minuter att instansiera en ny slutpunkt med dina anpassade modeller. När distributionens status ändras till **Slutför**är slutpunkten klar att användas.

När slutpunkten har distribuerats visas slutpunktsnamnet som en länk. Klicka på länken om du vill visa information som är specifik för slutpunkten, till exempel slutpunktsnyckeln, slutpunkts-URL:en och exempelkoden.

## <a name="view-logging-data"></a>Visa loggningsdata

Loggningsdata är tillgängliga för nedladdning under **Endpoint > Details**.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du använder din anpassade modell [här](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
