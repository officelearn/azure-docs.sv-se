---
title: Hämta prenumerationsnycklar för Custom Speech Service på Azure | Microsoft Docs
description: Lär dig mer om att hämta prenumerationsnycklar för anrop till Custom Speech Service i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 84ef657af2cc3dc4a7168a815b5e51d6f4f33fd7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338377"
---
# <a name="obtain-subscription-keys"></a>Hämta prenumerationsnycklar

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Kom igång med Azure Custom Speech Service, måste du först länka ditt konto till en Azure-prenumeration. Prenumerationer på kostnadsfria och betalda nivåer är tillgängliga. Information om nivåerna finns i den [prissättningssidan](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Hämta en prenumerationsnyckel
1. Du kan få en prenumerationsnyckel från Azure-portalen i ett av två sätt:

    * Gå till den [Azure-portalen](https://ms.portal.azure.com), och Lägg till ett nytt Cognitive Services-API genom att söka efter _Cognitive Services_ och sedan välja **Cognitive Services API: er**.

      ![Cognitive Services search](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Eller gå direkt till den [Cognitive Services API: er](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![API:er för Cognitive Services](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. Fyll i följande obligatoriska fält:

      a. **Kontonamn**. Använd ett namn som passar dig. Kom ihåg det här namnet så att du kan hitta din Cognitive Services-prenumeration i resurslistan över.

      b. **Prenumeration**. Välj en från dina Azure-prenumerationer.

      c. **API-typ**. Välj **Custom Speech Service (förhandsversion)**.

      d. **Plats**. Det är för närvarande **västra USA**.

      e. **Prisnivå**. Välj den nivå som passar dig. **F0** är den kostnadsfria nivån. Kvoter som tillåts beskrivs på den [prissättningssidan](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Cognitive Services har skapats](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. Du bör hitta antingen en vy på instrumentpanelen eller en tjänst med det angivna kontonamnet i resurslistan med. När du väljer det, visas en översikt över din tjänst. I listan till vänster under **resurshantering**väljer **nycklar**. Kopiera **nyckel 1**.

      Den här prenumerationsnyckel krävs i nästa steg.

      ![NYCKEL 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Kopiera inte de **prenumerations-ID** från översiktssidan. Du behöver prenumerationsnyckeln i nästa steg.
      >

      ![Översikt över prenumerations-ID](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. Välj ditt konto om du vill ange din prenumerationsnyckel på menyfliken i det övre högra hörnet. På den nedrullningsbara menyn väljer **prenumerationer**.

      ![Menyalternativet för prenumerationer](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    En tabell med prenumerationer visas, som är tom öppnas första gången.

    ![Prenumerationer tabell](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. Välj **Lägg till ny**. Ange ett namn för prenumerationen och prenumerationsnyckeln. Det kan vara antingen **nyckel 1** (primärnyckel) eller **nyckel 2** (sekundärnyckel) från prenumerationen.

      ![Nyckelnamn för prenumeration](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

För att ladda upp data, tränar en modell eller en distribution kan du behöva länka dina Custom Speech Service-aktiviteter till en Azure-prenumeration. Det kan vara antingen en kostnadsfri nivå eller en betald nivå-prenumeration. Mer information finns på sidan med [priser](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Hämta ett prenumerations-ID
För att få ett prenumerations-ID kan du gå till Azure-portalen. Sök efter *Cognitive Services* och *Custom Speech Service*, och följ instruktionerna.

> [!NOTE]
> Prenumerationsnyckeln krävs senare i den här processen.
>

## <a name="next-steps"></a>Nästa steg
* Börja skapa din [anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md).
* Börja skapa din [anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md).
