---
title: Vanliga frågor – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Innehåller svar på vanliga frågor om den anpassade översättare.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 25b7e7015b32609356eb138c86fbe537a87a7a22
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595815"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor om [anpassad översättare](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Vilka är de aktuella begränsningarna i anpassad översättare?

Det finns begränsningar och begränsningar avseende fil storlek, modell utbildning och modell distribution. Tänk på följande begränsningar när du konfigurerar din utbildning för att skapa en modell i en anpassad översättare.

- Skickade filer måste vara mindre än 100 MB stora.
- Monolingual-data stöds inte.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>När ska jag begära distribution för ett översättnings system som har tränats?

Det kan ta flera utbildningar att skapa det optimala översättnings systemet för ditt projekt. Du kanske vill prova att använda fler utbildnings data eller mer noggrant filtrerade data, om BLEU Poäng och/eller test resultaten inte är tillfredsställande. Du bör vara strikt och noggrann när du utformar din inställnings uppsättning och din test uppsättning för att vara helt representativ för terminologin och stilen för det material som du vill översätta. Du kan vara mer fria från att skriva dina utbildnings data och experimentera med olika alternativ. Begär en system distribution när du är nöjd med översättningarna i systemets test resultat, har inga mer data att lägga till i utbildningen för att förbättra det utbildade systemet och du vill ha åtkomst till den tränade modellen via API: er.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Hur många utbildade system kan distribueras i ett projekt?

Endast ett intränat system kan distribueras per projekt. Det kan ta flera utbildningar att skapa ett lämpligt översättnings system för ditt projekt och vi rekommenderar att du begär distribution av en utbildning som ger dig det bästa resultatet. Du kan fastställa kvaliteten på utbildningen genom BLEU-poängen (högre är bättre) och genom att konsultera granskarna innan du bestämmer att översättningens kvalitet är lämplig för distribution.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>När kan jag vänta på att mina utbildningar ska distribueras?

Distributionen tar vanligt vis mindre än en timme.

## <a name="how-do-you-access-a-deployed-system"></a>Hur kommer du åt ett distribuerat system?

Distribuerade system kan nås via Microsoft Translator Text API v3 genom att ange kategori kategori. Mer information om Translator Text API hittar du på webb sidan [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) .

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Hur gör jag för att hoppa över justerings-och menings brytningar om mina data redan är meningen?

Den anpassade översättaren hoppar över menings justering och menings brytningar för TMX-filer `.align` och text-filer med tillägget. `.align`filer ger användarna möjlighet att hoppa över en anpassad översättares mening och justerings process för de filer som är perfekt justerade och behöver ingen ytterligare bearbetning. Vi rekommenderar att `.align` du bara använder tillägget för filer som är perfekt justerade.

Om antalet extraherade meningar inte stämmer överens med de två filerna med samma bas namn, kommer den anpassade översättningen fortfarande att köra `.align` menings justeringen på filer.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Jag försökte ladda upp min TMX, men det säger att dokument bearbetningen misslyckades.

Se till att TMX överensstämmer med TMX 1.4 b-specifikationen på <https://www.gala-global.org/tmx-14b>.
