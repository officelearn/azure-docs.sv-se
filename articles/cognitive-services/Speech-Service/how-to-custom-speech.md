---
title: Kom igång med Custom Speech Speech service
titleSuffix: Azure Cognitive Services
description: Custom Speech är en uppsättning online-verktyg som gör att du kan utvärdera och förbättra all tal-till-text-precision för dina program, verktyg och produkter. Allt det krävs för att komma igång är en fåtal av test-ljudfiler. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: dapine
ms.openlocfilehash: 0788ff84cd5b0d50e0798d0d72ee84b47cb5f0b1
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368321"
---
# <a name="what-is-custom-speech"></a>Vad är Custom Speech?

[Custom Speech](https://aka.ms/customspeech) är en uppsättning online-verktyg som gör att du kan utvärdera och förbättra Microsofts tal-till-text-precision för dina program, verktyg och produkter. Allt det krävs för att komma igång är en fåtal av test-ljudfiler. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.

## <a name="whats-in-custom-speech"></a>Vad är i Custom Speech?

Innan du kan göra något med Custom Speech behöver du ett Azure-konto och en röst tjänst prenumeration. När du har ett konto kan du Förbered dina data, träna och testa dina modeller, granska igenkännings kvalitet, utvärdera precision och slutligen distribuera och använda den anpassade tal-till-text-modellen.

Det här diagrammet visar de delar som utgör den [Custom Speech portalen](https://aka.ms/customspeech). Använd länkarna nedan för att lära dig mer om varje steg.

![Markerar de olika komponenterna som utgör Custom Speechs portalen.](./media/custom-speech/custom-speech-overview.png)

1. [Prenumerera och skapa ett projekt](#set-up-your-azure-account) – skapa ett Azure-konto och prenumerera på tal tjänsten. Den här enhetliga prenumerationen ger dig åtkomst till tal översättning av tal till text, text till tal, tal översättning och [Custom Speech-portalen](https://speech.microsoft.com/customspeech). Sedan använder du din röst tjänst prenumeration och skapar ditt första Custom Speech-projekt.

2. [Ladda upp test data](how-to-custom-speech-test-data.md) – Ladda upp test data (ljudfiler) för att utvärdera Microsofts tal-till-text-erbjudande för dina program, verktyg och produkter.

3. [Granska igenkännings kvalitet](how-to-custom-speech-inspect-data.md) – Använd [Custom Speech portalen](https://speech.microsoft.com/customspeech) för att spela upp ljud som laddats upp och kontrol lera kvaliteten på test data. Information om kvantitativa mått finns i [Granska data](how-to-custom-speech-inspect-data.md).

4. [Utvärdera precision](how-to-custom-speech-evaluate-data.md) – utvärdera precisionen för tal till text-modellen. [Custom Speech-portalen](https://speech.microsoft.com/customspeech) ger en *fel frekvens i Word*som kan användas för att avgöra om det krävs ytterligare utbildning. Om du är nöjd med noggrannheten kan du använda API: erna för Speech service direkt. Om du vill förbättra noggrannheten med ett relativt genomsnitt på 5%-20%, använder du fliken **utbildning** i portalen för att ladda upp ytterligare tränings data, till exempel rapporter om mänsklig skrift och relaterad text.

5. [Träna modellen](how-to-custom-speech-train-model.md) – förbättra noggrannheten hos din tal-till-text-modell genom att tillhandahålla skrivna avskrifter (10 – 1 000 timmar) och relaterad text (< 200 MB) tillsammans med dina ljud test data. Dessa data hjälper till att träna tal-till-text-modellen. Efter utbildningen, testa om och om du är nöjd med resultatet kan du distribuera din modell.

6. [Distribuera modellen](how-to-custom-speech-deploy-model.md) – skapa en anpassad slut punkt för din tal-till-text-modell och Använd den i dina program, verktyg eller produkter.

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

En röst tjänst prenumeration krävs innan du kan använda [Custom Speech Portal](https://speech.microsoft.com/customspeech) för att skapa en anpassad modell. Följ de här anvisningarna för att skapa en standard prenumeration för tal tjänster: [skapa en tal prenumeration](get-started.md#try-the-speech-service-using-a-new-azure-account).

> [!NOTE]
> Se till att skapa standard prenumerationer (S0), prenumerationer för kostnads fria utvärderings versioner (F0) stöds inte.

När du har skapat ett Azure-konto och en röst tjänst prenumeration måste du logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech) och ansluta din prenumeration.

1. Hämta din prenumerations nyckel för röst tjänsten från Azure Portal.
2. Logga in på [Custom Speech portalen](https://aka.ms/custom-speech).
3. Välj den prenumeration du behöver för att arbeta med och skapa ett tal projekt.
4. Om du vill ändra din prenumeration använder du **kugg hjuls** -ikonen som finns i det övre navigerings fönstret.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som data, modeller, tester och slut punkter organiseras i **projekt** i [Custom Speech portalen](https://speech.microsoft.com/customspeech). Varje projekt är specifika för en domän och ett land/språk. Du kan till exempel skapa ett projekt för Call Center som använder engelska i USA.

Om du vill skapa ditt första projekt väljer du tal **-till-text/anpassad tal**och klickar sedan på **nytt projekt**. Följ anvisningarna i guiden för att skapa projektet. När du har skapat ett projekt bör du se fyra flikar: **data**, **testning**, **utbildning**och **distribution**. Använd länkarna som beskrivs i [Nästa steg](#next-steps) för att lära dig hur du använder varje flik.

> [!IMPORTANT]
> [Custom Speech portalen](https://aka.ms/custom-speech) uppdaterades nyligen! Om du har skapat tidigare projekt med data, modeller, tester och publicerade slut punkter i CRI: er-portalen eller med API: er, måste du skapa ett nytt projekt i den nya portalen för att ansluta till dessa gamla entiteter.

## <a name="next-steps"></a>Nästa steg

* [Förbered och testa dina data](how-to-custom-speech-test-data.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
