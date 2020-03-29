---
title: Kom igång med tjänsten Custom Speech - Tal
titleSuffix: Azure Cognitive Services
description: Custom Speech är en uppsättning onlineverktyg som gör att du kan utvärdera och förbättra vår tal-till-text-noggrannhet för dina program, verktyg och produkter. Allt som krävs för att komma igång är en handfull testljudfiler. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: e30587ed4d09ba792516ebc7b16529a6ee5ac57c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918962"
---
# <a name="what-is-custom-speech"></a>Vad är anpassat tal?

[Anpassat tal](https://aka.ms/customspeech) är en uppsättning onlineverktyg som gör att du kan utvärdera och förbättra Microsofts tal-till-text-noggrannhet för dina program, verktyg och produkter. Allt som krävs för att komma igång är en handfull testljudfiler. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.

## <a name="whats-in-custom-speech"></a>Vad finns i anpassat tal?

Innan du kan göra något med anpassat tal behöver du ett Azure-konto och en taltjänstprenumeration. När du har ett konto kan du förbereda dina data, träna och testa dina modeller, kontrollera igenkänningskvaliteten, utvärdera noggrannheten och slutligen distribuera och använda den anpassade tal-till-text-modellen.

I det här diagrammet visas de delar som utgör [portalen Anpassad tal](https://aka.ms/customspeech). Använd länkarna nedan om du vill veta mer om varje steg.

![Markerar de olika komponenter som utgör portalen För anpassat tal.](./media/custom-speech/custom-speech-overview.png)

1. [Prenumerera och skapa ett projekt](#set-up-your-azure-account) - Skapa ett Azure-konto och prenumerera på taltjänsten. Den här enhetliga prenumerationen ger dig tillgång till tal-till-text, text-till-tal, talöversättning och [portalen Anpassat tal](https://speech.microsoft.com/customspeech). Skapa sedan ditt första custom speech-projekt med hjälp av prenumerationen på taltjänsten.

2. [Ladda upp testdata](how-to-custom-speech-test-data.md) – Ladda upp testdata (ljudfiler) för att utvärdera Microsofts tal-till-text-erbjudande för dina program, verktyg och produkter.

3. [Kontrollera igenkänningskvaliteten](how-to-custom-speech-inspect-data.md) – Använd [portalen För anpassat tal](https://speech.microsoft.com/customspeech) för att spela upp uppladdat ljud och kontrollera taligenkänningskvaliteten på dina testdata. Kvantitativa mätningar finns i [Kontrollera data](how-to-custom-speech-inspect-data.md).

4. [Utvärdera noggrannheten](how-to-custom-speech-evaluate-data.md) - Utvärdera noggrannheten i tal-till-text-modellen. Portalen [För anpassat tal](https://speech.microsoft.com/customspeech) tillhandahåller en *felfrekvens*för Word , som kan användas för att avgöra om ytterligare utbildning krävs. Om du är nöjd med noggrannheten kan du använda API:erna för taltjänsten direkt. Om du vill förbättra noggrannheten med ett relativt genomsnitt på 5% - 20 % använder du fliken **Utbildning** i portalen för att ladda upp ytterligare träningsdata, till exempel avskrifter med mänskligt märkta utskrifter och relaterad text.

5. [Träna modellen](how-to-custom-speech-train-model.md) - Förbättra noggrannheten i din tal-till-text-modell genom att tillhandahålla skriftliga utskrifter (10-1000 timmar) och relaterad text (<200 MB) tillsammans med dina ljudtestdata. Dessa data hjälper till att träna tal-till-text-modellen. Efter träningen, testa om, och om du är nöjd med resultatet kan du distribuera din modell.

6. [Distribuera modellen](how-to-custom-speech-deploy-model.md) - Skapa en anpassad slutpunkt för din tal-till-text-modell och använd den i dina program, verktyg eller produkter.

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

En taltjänstprenumeration krävs innan du kan använda [portalen Anpassat tal](https://speech.microsoft.com/customspeech) för att skapa en anpassad modell. Följ dessa instruktioner för att skapa en standard prenumeration på taltjänsten: [Skapa en talprenumeration](get-started.md#new-resource).

> [!NOTE]
> Se till att skapa standardprenumerationer (S0), gratis provperiod (F0) prenumerationer stöds inte.

När du har skapat ett Azure-konto och en taltjänstprenumeration måste du logga in på [portalen Anpassat tal](https://speech.microsoft.com/customspeech) och ansluta din prenumeration.

1. Hämta prenumerationsnyckeln för Taltjänsten från Azure-portalen.
2. Logga in på [portalen Anpassat tal](https://aka.ms/custom-speech).
3. Välj den prenumeration du behöver arbeta med och skapa ett talprojekt.
4. Om du vill ändra prenumerationen använder du **kuggikonen** som finns i den övre navigeringen.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som data, modeller, tester och slutpunkter är ordnade i **Projekt** i [portalen Anpassat tal](https://speech.microsoft.com/customspeech). Varje projekt är specifikt för en domän och ett land/språk. Du kan till exempel skapa ett projekt för callcenter som använder engelska i USA.

Om du vill skapa det första projektet markerar du tal **tal från tal till text och**klickar sedan på Nytt **projekt**. Skapa projektet genom att följa instruktionerna i guiden. När du har skapat ett projekt bör du se fyra flikar: **Data,** **Testning,** **Utbildning**och **Distribution**. Använd länkarna i [nästa steg](#next-steps) för att lära dig hur du använder varje flik.

> [!IMPORTANT]
> Portalen [För anpassat tal](https://aka.ms/custom-speech) har nyligen uppdaterats! Om du har skapat tidigare data, modeller, tester och publicerade slutpunkter i CRIS.ai portalen eller med API:er måste du skapa ett nytt projekt i den nya portalen för att ansluta till dessa gamla entiteter.

## <a name="next-steps"></a>Nästa steg

* [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
