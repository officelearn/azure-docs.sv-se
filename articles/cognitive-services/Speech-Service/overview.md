---
title: Vad är taltjänst (förhandsversion)?
description: 'Tjänsten tal, en del av Microsofts Cognitive Services och unites flera Azure taltjänster som tidigare var tillgängliga separat: Bing-tal (som består av taligenkänning och text till tal), anpassat tal och Talöversättning.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445642"
---
# <a name="what-is-the-speech-service-preview"></a>Vad är taltjänst (förhandsversion)?

Med taltjänsten drivs av de tekniker som används i andra Microsoftprodukter, inklusive Cortana och Microsoft Office.  Samma tjänsten är tillgänglig för alla kunder som en Cognitive Service. 

> [!NOTE]
> Speech-tjänsten är för närvarande i offentlig förhandsversion. Gå tillbaka här regelbundet efter uppdateringar till dokumentation och ytterligare exempel.

Med en prenumeration ger våra Speech service utvecklare ett enkelt sätt att ge sina program kraftfulla talbaserade funktioner. Dina appar kan nu funktionen voice-kommandot, transkription, diktering, talsyntes och översättning.

## <a name="speech-service-features"></a>Speech service-funktioner

|Funktion|Beskrivning|
|-|-|
|[Tal till text](speech-to-text.md)| Transkriberar ljud till text som ditt program kan acceptera som indata. Är integrerat med den [tjänst för Språkförståelse](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) för att härleda användaravsikt från yttranden.|
|[Text till tal](text-to-speech.md)| Konverterar oformaterad text till tal för naturlig standardrösttyper, levereras till ditt program i en ljudfil. Flera röster, varierande i kön eller accent, är tillgängliga för många språk som stöds. |
|[Talöversättning –](speech-translation.md)| Kan användas antingen för att översätta strömmande ljud i nära realtid eller för att behandla inspelade tal. |
|[Tal enheter SDK](speech-devices-sdk.md)| Med introduktionen av enhetliga Speech-tjänsten erbjuder Microsoft och dess samarbetspartner en plattform för integrerad maskin-och programvara som optimerats för utveckla tal-aktiverade enheter |

## <a name="using-the-speech-service"></a>Med Speech-tjänsten

Speech-tjänsten är tillgänglig på två sätt. [SDK: N](speech-sdk.md) avlägsnar information om nätverksprotokoll. Den [REST API](rest-apis.md) fungerar med alla programmeringsspråk, men inte tillhandahåller de funktioner som erbjuds av SDK: N.

|<br>Metod|Tal<br>till Text|Text som ska<br>Tal|Tal<br>Översättning|<br>Beskrivning|
|-|-|-|-|-|
|[SDK: er](speech-sdk.md)|Ja|Nej|Ja|Bibliotek för specifika programmeringsspråk som förenklar utveckling.|
|[REST](rest-apis.md)|Ja|Ja|Nej|En enkel HTTP-baserad API som gör det enkelt att lägga till tal i ditt program.|

## <a name="next-steps"></a>Nästa steg

Få en kostnadsfri utvärderingsprenumeration nyckel för Speech-tjänsten.

> [!div class="nextstepaction"]
> [Prova Speech-tjänsten utan kostnad](get-started.md)
