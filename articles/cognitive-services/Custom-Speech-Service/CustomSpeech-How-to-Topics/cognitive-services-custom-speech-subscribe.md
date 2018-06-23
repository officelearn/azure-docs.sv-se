---
title: Hämta prenumeration nycklar för anpassad tal tjänsten i Azure | Microsoft Docs
description: Lär dig mer om att hämta prenumerationen nycklar för anrop till anpassad tal tjänsten i kognitiva tjänster.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fcef86a19a77581ff82b64173e2ac68b26ae708a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351654"
---
# <a name="obtain-subscription-keys"></a>Hämta prenumerationsnycklar
Om du vill komma igång med tjänsten Azure anpassad tal, måste du först att länka ditt konto till en Azure-prenumeration. Det finns prenumerationer kostnadsfria nivåer och betalnivåer. Information om nivåerna finns i [sida med priser](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Hämta en nyckel för prenumeration
1. Du kan få en nyckel för prenumeration från Azure-portalen på något av två sätt:

    * Gå till den [Azure-portalen](https://ms.portal.azure.com), och Lägg till ett nytt kognitiva Services API genom att söka efter _kognitiva Services_ och sedan välja **kognitiva Services API: er**.

      ![Kognitiva Services search](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Eller gå direkt till den [kognitiva Services API: er](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![API:er för Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
2. Fyll i följande obligatoriska fält:

      a. **Kontonamn**. Använd ett namn som passar dig. Kom ihåg detta namn så att du kan hitta prenumerationen kognitiva tjänster i resurslistan över.

      b. **Prenumeration**. Välj ett av dina Azure-prenumerationer.

      c. **API-typen**. Välj **anpassade tal Service (förhandsgranskning)**.

      d. **Plats**. Det är för närvarande **västra USA**.

      e. **Prisnivån**. Välj den nivå som passar dig. **F0** är den kostnadsfria nivån. Kvoterna som tillåts beskrivs på den [sida med priser](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Skapa ett kognitiva Services-konto](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

3. I resurslistan över bör du hitta antingen en vy på instrumentpanelen eller en tjänst med det angivna kontonamnet. När du väljer det, kan du se en översikt över din tjänst. I listan till vänster under **resurshantering**väljer **nycklar**. Kopiera **nyckel 1**.

      Den här prenumerationen nyckeln krävs i nästa steg.

      ![NYCKEL 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Kopiera inte den **prenumerations-ID** från översiktssidan. Du måste nyckeln prenumeration i nästa steg.
      >

      ![Översikt över prenumerations-ID](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

4. Nyckeln i menyfliksområdet i det övre högra Välj ditt konto för att ange din prenumeration. På den nedrullningsbara menyn väljer **prenumerationer**.

      ![Menyalternativet för prenumerationer](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    En tabell med prenumerationer visas som är tom första gången den öppnas.

    ![Prenumerationer tabell](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

5. Välj **Lägg till ny**. Ange ett namn för prenumerationen och nyckeln för prenumerationen. Det kan vara antingen **nyckel 1** (primärnyckel) eller **nyckel 2** (sekundärnyckeln) från prenumerationen.

      ![Prenumerationen nyckelnamn](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Om du vill ladda upp data, tränar en modell eller göra en distribution måste du länka dina anpassade tal Service aktiviteter till en Azure-prenumeration. Det kan vara antingen en kostnadsfria nivån eller en nivå betald prenumeration. Mer information finns på sidan med [priser](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Hämta ett prenumerations-ID
För att få ett prenumerations-ID, gå till Azure-portalen. Sök efter *kognitiva Services* och *anpassade tal Service*, och följ instruktionerna.

> [!NOTE]
> Nyckeln prenumeration krävs senare i den här processen.
>

## <a name="next-steps"></a>Nästa steg
* Börjar skapa din [anpassade ljud modellen](cognitive-services-custom-speech-create-acoustic-model.md).
* Börjar skapa din [anpassade språk modellen](cognitive-services-custom-speech-create-language-model.md).
