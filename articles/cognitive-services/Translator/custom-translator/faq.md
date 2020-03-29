---
title: Vanliga frågor och svar - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller svar på vanliga frågor om Azure Cognitive Services Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 97d399f4a8ec704fd90eb6c49f0835be7e9e4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836601"
---
# <a name="custom-translator-frequently-asked-questions"></a>Anpassade översättare vanliga frågor

Den här artikeln innehåller svar på vanliga frågor om [Anpassad översättare](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Vilka är de aktuella begränsningarna i Custom Translator?

Det finns begränsningar och begränsningar när det gäller filstorlek, modellutbildning och modelldistribution. Tänk på dessa begränsningar när du konfigurerar din utbildning för att skapa en modell i Anpassad översättare.

- Inskickade filer måste vara mindre än 100 MB i storlek.
- Enspråkiga data stöds inte.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>När ska jag begära distribution för ett översättningssystem som har tränats?

Det kan ta flera utbildningar för att skapa det optimala översättningssystemet för ditt projekt. Du kanske vill prova att använda mer träningsdata eller mer noggrant filtrerade data, om BLEU-poängen och/ eller testresultaten inte är tillfredsställande. Du bör vara strikt och försiktig med att utforma din tuning set och din testuppsättning, att vara helt representativ för terminologin och stil av material du vill översätta. Du kan vara mer liberal i att komponera dina träningsdata och experimentera med olika alternativ. Begär en systemdistribution när du är nöjd med översättningarna i systemtestresultaten, har inga fler data att lägga till i utbildningen för att förbättra ditt tränade system och du vill komma åt den tränade modellen via API:er.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Hur många utbildade system kan distribueras i ett projekt?

Endast ett tränat system kan distribueras per projekt. Det kan ta flera utbildningar för att skapa ett lämpligt översättningssystem för ditt projekt och vi uppmuntrar dig att begära distribution av en utbildning som ger dig det bästa resultatet. Du kan bestämma kvaliteten på utbildningen med BLEU-poängen (högre är bättre) och genom att samråda med granskare innan du bestämmer dig för att kvaliteten på översättningarna är lämplig för distribution.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>När kan jag förvänta mig att mina utbildningar ska distribueras?

Distributionen tar vanligtvis mindre än en timme.

## <a name="how-do-you-access-a-deployed-system"></a>Hur kommer du åt ett distribuerat system?

Distribuerade system kan nås via Microsoft Translator Text API V3 genom att ange CategoryID. Mer information om Translator Text API finns på [API-referenswebbsidan.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Hur hoppar jag över justering och meningsbrytning om mina data redan är meningsjusterade?

Custom Translator hoppar över meningsjustering och meningsbrytning för TMX-filer och för textfiler med `.align` tillägget. `.align`filer ger användarna en möjlighet att hoppa custom translator's mening bryta och justera processen för de filer som är perfekt justerade, och behöver ingen ytterligare bearbetning. Vi rekommenderar `.align` att du använder tillägg endast för filer som är helt justerade.

Om antalet extraherade meningar inte matchar de två filerna med samma basnamn kör `.align` Custom Translator fortfarande meningsjusteraren på filer.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Jag försökte ladda upp min TMX, men det står "dokumentbehandling misslyckades".

Se till att TMX överensstämmer med TMX 1.4b-specifikationen på <https://www.gala-global.org/tmx-14b>.
