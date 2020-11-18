---
title: Custom Speech översikt – tal tjänst
titleSuffix: Azure Cognitive Services
description: Custom Speech är en uppsättning online-verktyg som gör att du kan utvärdera och förbättra all tal-till-text-precision för dina program, verktyg och produkter. Allt det krävs för att komma igång är en fåtal av test-ljudfiler. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 6dbe97d615753f0a90c8ba80aa7afa6dafa15eb2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658476"
---
# <a name="what-is-custom-speech"></a>Vad är Custom Speech?

[Custom Speech](https://aka.ms/customspeech) är en uppsättning UI-baserade verktyg som gör att du kan utvärdera och förbättra Microsofts tal-till-text-precision för dina program och produkter. Allt det krävs för att komma igång är en fåtal av test-ljudfiler. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.

## <a name="whats-in-custom-speech"></a>Vad är i Custom Speech?

Innan du kan göra något med Custom Speech behöver du ett Azure-konto och en röst tjänst prenumeration. När du har ett konto kan du Förbered dina data, träna och testa dina modeller, granska igenkännings kvalitet, utvärdera precision och slutligen distribuera och använda den anpassade tal-till-text-modellen.

Det här diagrammet visar de delar som utgör den [Custom Speech portalen](https://aka.ms/customspeech). Använd länkarna nedan för att lära dig mer om varje steg.

![Markerar de olika komponenterna som utgör Custom Speechs portalen.](./media/custom-speech/custom-speech-overview.png)

1. [Prenumerera och skapa ett projekt](#set-up-your-azure-account) – skapa ett Azure-konto och prenumerera på tal tjänsten. Den här enhetliga prenumerationen ger dig åtkomst till tal översättning av tal till text, text till tal, tal översättning och [Custom Speech-portalen](https://speech.microsoft.com/customspeech). Sedan använder du din röst tjänst prenumeration och skapar ditt första Custom Speech-projekt.

1. [Ladda upp test data](how-to-custom-speech-test-data.md) – Ladda upp test data (ljudfiler) för att utvärdera Microsofts tal-till-text-erbjudande för dina program, verktyg och produkter.

1. [Granska igenkännings kvalitet](how-to-custom-speech-inspect-data.md) – Använd [Custom Speech portalen](https://speech.microsoft.com/customspeech) för att spela upp ljud som laddats upp och kontrol lera kvaliteten på test data. Information om kvantitativa mått finns i [Granska data](how-to-custom-speech-inspect-data.md).

1. [Utvärdera och förbättra precisionen](how-to-custom-speech-evaluate-data.md) – utvärdera och förbättra noggrannheten hos tal-till-text-modellen. [Custom Speech-portalen](https://speech.microsoft.com/customspeech) ger en *fel frekvens i Word* som kan användas för att avgöra om det krävs ytterligare utbildning. Om du är nöjd med noggrannheten kan du använda API: erna för Speech service direkt. Om du vill förbättra noggrannheten med ett relativt genomsnitt på 5%-20%, använder du fliken **utbildning** i portalen för att ladda upp ytterligare tränings data, till exempel rapporter om mänsklig skrift och relaterad text.

1. [Träna och distribuera en modell](how-to-custom-speech-train-model.md) – förbättra noggrannheten hos din tal-till-text-modell genom att tillhandahålla skrivna avskrifter (10 – 1 000 timmar) och relaterad text (<200 MB) tillsammans med ljud test data. Dessa data hjälper till att träna tal-till-text-modellen. Efter utbildningen, testa om och om du är nöjd med resultatet kan du distribuera din modell till en anpassad slut punkt.

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

Det krävs ett Azure-konto och en röst tjänst prenumeration innan du kan använda [Custom Speech Portal](https://speech.microsoft.com/customspeech) för att skapa en anpassad modell. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Se till att skapa standard prenumerationer (S0), kostnads fria (F0) prenumerationer stöds inte.

När du har skapat ett Azure-konto och en röst tjänst prenumeration måste du logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech) och ansluta din prenumeration.

1. Logga in på [Custom Speech portalen](https://aka.ms/custom-speech).
1. Välj den prenumeration du behöver för att arbeta med och skapa ett tal projekt.
1. Om du vill ändra din prenumeration använder du **kugg hjuls** -ikonen som finns i det övre navigerings fönstret.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som data, modeller, tester och slut punkter organiseras i **projekt** i [Custom Speech portalen](https://speech.microsoft.com/customspeech). Varje projekt är specifika för en domän och ett land/språk. Du kan till exempel skapa ett projekt för Call Center som använder engelska i USA.

Om du vill skapa ditt första projekt väljer du tal **-till-text/anpassad tal** och klickar sedan på **nytt projekt**. Följ anvisningarna i guiden för att skapa projektet. När du har skapat ett projekt bör du se fyra flikar: **data**, **testning**, **utbildning** och **distribution**. Använd länkarna som beskrivs i [Nästa steg](#next-steps) för att lära dig hur du använder varje flik.

> [!IMPORTANT]
> [Custom Speech portalen](https://aka.ms/custom-speech) uppdaterades nyligen! Om du har skapat tidigare data, modeller, tester och publicerade slut punkter i CRIS.ai-portalen eller med API: er, måste du skapa ett nytt projekt i den nya portalen för att ansluta till dessa gamla entiteter.

## <a name="model-lifecycle"></a>Modell livs cykel

Det anpassade talet använder både **bas modeller** och **anpassade modeller**. Varje språk har en eller flera **bas modeller**. När en ny tal modell släpps till den vanliga tal tjänsten importeras den också till den Custom Speech tjänsten som en ny **bas modell**. De uppdateras vanligt vis var 3-6 månad, och äldre modeller blir mindre användbara över tid eftersom den senaste modellen vanligt vis har högre noggrannhet.

**Anpassade modeller** skapas däremot genom att anpassa en vald bas modell till ett visst kund scenario. Du kan fortsätta att använda en viss anpassad modell under en längre tids period när du har kommit till en som uppfyller dina behov, men vi rekommenderar att du regelbundet uppdaterar till den senaste bas modellen och tränar med ytterligare data.

Andra viktiga villkor som rör modell livs cykeln är:

* **Anpassning**: ta en bas modell och anpassa den till domänen/scenariot med hjälp av text data och/eller ljud data
* **Avkodning**: använda en modell och utföra tal igenkänning (avkoda ljud till text)
* **Slut punkt**: en användarspecifik distribution av antingen en bas modell eller en anpassad modell som *endast* är tillgänglig för en angiven användare.

### <a name="expiration-timeline"></a>Tids linje för förfallo datum

När nya modeller och nya funktioner blir tillgängliga och äldre, tas mindre exakta modeller bort, se följande tids linjer för modell-och slut punkts utgång:

**Bas modeller** 

* Anpassning: tillgänglig i 1 år. När modellen har importer ATS är den tillgänglig i 1 år för att skapa anpassade modeller. Efter ett år måste nya anpassade modeller skapas från en senare bas modell version.  
* Avkodning: tillgänglig i 2 år efter import. Det innebär att du kan skapa en slut punkt och använda batch-avskrift för 2 år med den här modellen. 
* Slut punkter: tillgängliga på samma tids linje som avkodning

**Anpassade modeller**

* Avkodning: tillgänglig i 2 år efter att modellen har skapats. Det innebär att du kan använda den anpassade modellen i 2 år (batch/Real/test) när den har skapats. Efter 2 år **bör du träna modellen** på grund av att bas modellen är föråldrad för anpassning.  
* Slut punkter: tillgängliga på samma tids linje som avkodning

När antingen en bas modell eller anpassad modell upphör att gälla, kommer den alltid att återgå till den **senaste bas modell versionen**. Därför kommer din implementering aldrig att brytas, men det kan bli mindre exakt för *dina data* om anpassade modeller upphör att gälla. Du kan se förfallo datum för en modell på följande platser i Custom Speech-portalen:

* Sammanfattning av modell utbildning
* Information om modell utbildning
* Distributionssammanfattning
* Distributions information

Du kan också kontrol lera förfallo datumen via [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) och [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) anpassade tal-API: er under `deprecationDates` egenskapen i JSON-svaret.

Observera att du kan uppgradera modellen på en anpassad tal slut punkt utan drift stopp genom att ändra modellen som används av slut punkten i distributions avsnittet i den anpassade tal portalen, eller via det anpassade tal-API: et.

## <a name="next-steps"></a>Nästa steg

* [Förbered och testa dina data](how-to-custom-speech-test-data.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera och förbättra modell precisionen](how-to-custom-speech-evaluate-data.md)
* [Träna och distribuera en modell](how-to-custom-speech-train-model.md)
