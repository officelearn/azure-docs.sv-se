---
title: Kom igång med anpassad tal – Speech Services
titlesuffix: Azure Cognitive Services
description: Anpassat tal är en uppsättning online-verktyg som gör det möjligt att utvärdera och förbättra Microsofts tal till text Precision för dina program, verktyg och produkter. Allt som behövs för att komma igång är en handfull test ljudfiler. Följ länkarna nedan för att börja skapa en anpassad tal till text-upplevelse.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: ab33feb1ffdbced193afaba8f52719b3c215652f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511121"
---
# <a name="what-is-custom-speech"></a>Vad är anpassade tal?

[Custom Speech](https://aka.ms/custom-speech) är en uppsättning online-verktyg som gör det möjligt att utvärdera och förbättra Microsofts tal till text Precision för dina program, verktyg och produkter. Allt som behövs för att komma igång är en handfull test ljudfiler. Följ länkarna nedan för att börja skapa en anpassad tal till text-upplevelse.

## <a name="whats-in-custom-speech"></a>Vad ingår i anpassat tal?

Innan du kan göra något med anpassat tal, behöver du ett Azure-konto och en Speech Services-prenumeration. När du har ett konto kan kan du Förbered dina data, träna och testa dina modeller, granska erkännande kvalitet, utvärdera Precision, och slutligen distribuera och använda anpassad tal till text-modell.

Det här diagrammet visar de delar som utgör portal för anpassat tal. Använd länkarna nedan om du vill veta mer om varje steg.

![Visar de olika komponenterna som utgör portal för anpassat tal.](./media/custom-speech/custom-speech-overview.png)

1. [Prenumerera på och skapa ett projekt](#set-up-your-azure-account) – skapa ett Azure-konto och prenumerera på Speech Services. Den här enhetlig prenumerationen ger dig åtkomst till tal-till-text, tal, talöversättning och portal för anpassat tal. Sedan använder prenumerationen Speech Services, skapa ditt första projekt för anpassat tal.

2. [Ladda upp testdata](how-to-custom-speech-test-data.md) – ladda upp testdata (ljudfiler) för att utvärdera Microsofts tal-till-text erbjudande för dina program, verktyg och produkter.

3. [Inspektera erkännande kvalitet](how-to-custom-speech-inspect-data.md) -Använd Custom Speech-portalen för att spela upp ljudet överförda och inspektera tal igenkänning av kvaliteten på dina testdata. Kvantitativa mått finns [granska data](how-to-custom-speech-inspect-data.md).

4. [Utvärdera Precision](how-to-custom-speech-evaluate-data.md) -utvärdera riktighet tal till text-modellen. Anpassad tal-portalen ger en *Word Felfrekvens*, som kan användas för att avgöra om det krävs ytterligare utbildning. Om du är nöjd med noggrannheten kan använda du tal Service API: er direkt. Om du vill förbättra noggrannheten genom det relativa genomsnittet av 5-20%, använder du den **utbildning** fliken i portalen för att ladda upp ytterligare data, till exempel mänskliga etikett betyg och relaterade text.

5. [Träna modellen](how-to-custom-speech-train-model.md) – förbättra tal till text-modellen genom att tillhandahålla skriftliga avskrifter (10 – 1 000 timmar) och relaterade text (< 200 MB) tillsammans med dina ljud testdata. Den här informationen hjälper dig för att träna modellen tal till text. Efter utbildning, testa, och om du är nöjd med resultatet kan du distribuera din modell.

6. [Distribuera modellen](how-to-custom-speech-deploy-model.md) – skapa en anpassad slutpunkt för din modell för tal till text och använda den i ditt program, verktyg eller produkter.

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

Det krävs en Speech Services-prenumeration innan du kan använda portal för anpassat tal för att skapa en anpassad modell. Följ dessa instruktioner för att skapa en standard Speech Services-prenumeration: [Skapa en prenumeration för tal](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Var noga med att skapa standardprenumerationer (S0), kostnadsfria utvärderingsversioner (F0) stöds inte.

När du har skapat ett Azure-konto och en Speech Services-prenumeration, måste du logga in på portalen för anpassat tal och ansluter din prenumeration.

1. Hämta prenumerationsnyckeln Speech Services från Azure-portalen.
2. Logga in på den [portal för anpassat tal](https://aka.ms/custom-speech).
3. Välj den prenumeration som du vill arbeta med och skap en tal-projekt.
4. Om du vill ändra din prenumeration kan du använda den **kugghjulet** ikonen finns i det övre navigeringsfältet.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som slutpunkter, tester, modeller och data är ordnade i **projekt** i portal för anpassat tal. Alla projekt som är specifik för en domän och land/språk. Du kan till exempel skapa ett projekt för call Center som använder den engelska versionen i USA.

För att skapa ditt första projekt, Välj den **tal till text/anpassat tal**, klicka sedan på **nytt projekt**. Följ instruktionerna i guiden för att skapa ditt projekt. När du har skapat ett projekt, bör du se fyra flikar: **Data**, **testning**, **utbildning**, och **distribution**. Använd länkarna i [nästa steg](#next-steps) att lära dig hur du använder varje flik.

## <a name="next-steps"></a>Nästa steg

* [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
