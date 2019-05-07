---
title: Vad är anpassade röst? – Speech Services
titlesuffix: Azure Cognitive Services
description: Anpassade röst är en uppsättning online-verktyg som gör det möjligt att skapa en identifierbara, en av en typ ton för ditt varumärke. Allt som behövs för att komma igång är ett fåtal ljudfiler och associerade avskrifter. Följ länkarna nedan för att börja skapa en anpassad tal till text-upplevelse.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 8cf9dc6cbfc96448462aac3a64807f8beb6036ad
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156917"
---
# <a name="get-started-with-custom-voice"></a>Kom igång med anpassade röst

Anpassade röst är en uppsättning online-verktyg som gör det möjligt att skapa en identifierbara, en av en typ ton för ditt varumärke. Allt som behövs för att komma igång är ett fåtal ljudfiler och associerade avskrifter. Följ länkarna nedan för att börja skapa en anpassad text till tal-upplevelse.

## <a name="whats-in-custom-voice"></a>Vad är anpassade röst?

Innan du börjar med anpassade röst, behöver du ett Azure-konto och en Speech Services-prenumeration. När du har skapat ett konto kan du förbereda dina data, träna och testa dina modeller, utvärdera röst kvalitet och slutligen distribuera anpassade voice-modellen.

Diagrammet nedan visar hur du skapar en anpassad voice-modell med hjälp av anpassade Voice-portalen. Använd länkarna om du vill veta mer.

![Arkitekturdiagram för anpassade röst](media/custom-voice/custom-voice-diagram.png)

1.  [Prenumerera på och skapa ett projekt](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#set-up-your-azure-account) – skapa ett Azure-konto och skapa en Speech Services-prenumeration. Den här enhetlig prenumerationen ger dig åtkomst till tal till text, tal, talöversättning och anpassade Voice-portalen. Sedan använder prenumerationen Speech Services, skapa ditt första anpassade Voice-projekt.

2.  [Ladda upp data](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#upload-your-datasets) – ladda upp data (ljud och text) med anpassade Voice-portalen eller API för anpassat röst. Från portalen kan du undersöka och utvärdera uttal poängen och signal brus förhållanden. Mer information finns i [hur du förbereder data för anpassad röst](how-to-custom-voice-prepare-data.md).

3.  [Träna din modell](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#build-your-voice-font) – använda dina data för att skapa en anpassad text till tal röst-modell. Du kan träna en modell i olika språk. Testa din modell efter utbildning, och om du är nöjd med resultatet kan du distribuera modellen.

4.  [Distribuera din modell](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#create-and-use-a-custom-endpoint) – skapa en anpassad slutpunkt för din rösten modell och använda den för talsyntes produkter, verktyg och program.

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

Det krävs en Speech Services-prenumeration innan du kan använda portal för anpassat tal för att skapa en anpassad modell. Följ dessa instruktioner för att skapa en Speech Services-prenumeration i Azure. Om du inte har ett Azure-konto, kan du registrera dig för en ny.  

När du har skapat ett Azure-konto och en Speech Services-prenumeration, måste du logga in på portalen för anpassad röst och ansluter din prenumeration.

1. Hämta prenumerationsnyckeln Speech Services från Azure-portalen.
2. Logga in på den [anpassad röst portal](https://aka.ms/custom-voice).
3. Välj din prenumeration och skapa ett projekt med tal.
4. Om du vill växla till ett annat tal-prenumeration kan du använda kugghjulsikonen i det övre navigeringsfältet.

> [!NOTE]
> Anpassad Voice-tjänsten stöder inte nyckeln 30-dagars kostnadsfri utvärderingsversion. Du måste ha en F0 eller en S0-nyckel som skapats i Azure innan du kan använda tjänsten.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som slutpunkter, tester, modeller och data är ordnade i **projekt** i anpassade Voice-portalen. Alla projekt som är specifik för ett land/språk och röst som du vill skapa kön. Du kan till exempel skapa ett projekt för en kvinnliga ton för din Callcenter chatt robotar som använder den engelska versionen i USA (en-US).

För att skapa ditt första projekt, Välj den **Text-till-Speech/anpassat röst** och klicka sedan på **nytt projekt**. Följ instruktionerna i guiden för att skapa ditt projekt. När du har skapat ett projekt, visas fyra flikar: **Data**, **utbildning**, **testning**, och **distribution**. Använd länkarna i [nästa steg](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#next-steps) att lära dig hur du använder varje flik.

## <a name="next-steps"></a>Nästa steg

- [Förbereda anpassade Voice-data](how-to-custom-voice.md)
- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Guide: Registrera din röst-exempel](record-custom-voice-samples.md)
