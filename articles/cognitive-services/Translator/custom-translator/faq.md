---
title: Vanliga frågor och svar - anpassade Translator
titleSuffix: Azure Cognitive Services
description: Innehåller svar på vanliga frågor om anpassade Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: reference
ms.openlocfilehash: 38ebde9179d79c091edb269c1be4c9c956f34006
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627584"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar om [anpassad Translator](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Vilka är de aktuella begränsningarna i anpassade Translator?

Det finns begränsningar och gränser med avseende på filstorlek, modellinlärning och distribution av modeller. Tänk på dessa begränsningar när du konfigurerar utbildning för att skapa en modell i anpassade Translator.

- Skickade filerna måste vara mindre än 100 MB i storlek.

- Enspråkig data stöds inte.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>När bör jag begära distribution för en översättningssystemet som har tränats?

Det kan ta flera utbildningar att skapa den optimala översättningssystemet för ditt projekt. Du kanske vill prova att använda fler träningsdata eller mer noggrant filtrerade data om BLEU poäng och / eller testresultaten inte är nöjd. Du bör vara strikt och noggrann i designa din justering uppsättning och testet inställda, ska vara helt representativt terminologi och stil material som du vill översätta. Du kan vara mer fria i skapa dina utbildningsdata och experimentera med olika alternativ. Begär system distribution när du är nöjd med översättningarna i testresultaten system, har inga fler data att lägga till i utbildning för att förbättra tränade systemet och du vill komma åt den tränade modellen via API: er.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Hur många tränade system kan distribueras i ett projekt?

Endast en tränad system kan distribueras per projekt. Det kan ta flera utbildningar att skapa en lämplig översättningssystemet för ditt projekt och vi rekommenderar att du kan begära distribution av en utbildning som ger dig det bästa resultatet. Du kan fastställa kvaliteten på utbildningen BLEU poäng (högre är bättre), och med hjälp av med granskare innan du bestämmer dig att översättningskvaliteten är lämplig för distribution.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>När kan jag förvänta mig Mina kurser som ska distribueras

Distributionen tar vanligtvis mindre än en timme.

## <a name="how-do-you-access-a-deployed-system"></a>Hur kommer du åt en distribuerade system?

Distribuerade system kan nås via Microsoft Translator Text API V3 genom att ange CategoryID. Mer information om Translator Text API finns i den [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) webbsidan.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Hur jag hoppa över justering och meningen som större om Mina data redan mening justerad?

Anpassad Translator hoppar över mening justering och de senaste mening för TMX filer och textfiler med den `.align` tillägget. `.align` filer ge användarna ett alternativ till anpassad Translator meningen större och justeringen för filer som är perfekt justerad och behöver ingen ytterligare bearbetning. Vi rekommenderar att du använder `.align` filnamnstillägget endast för filer som är perfekt justerad.

Om antalet extraherade meningar inte matchar de två filerna med samma grundläggande namn, anpassade Translator fortfarande körs mening aligner `.align` filer.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Jag försökte överföra min TMX, men den säger ”Dokumentbearbetning misslyckades”.

Se till att TMX motsvarar TMX 1.4b-specifikationen på <https://www.gala-global.org/tmx-14b>.
