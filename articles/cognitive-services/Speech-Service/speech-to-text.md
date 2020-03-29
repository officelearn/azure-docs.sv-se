---
title: Tal-till-text - Taltjänst
titleSuffix: Azure Cognitive Services
description: Tal-till-text-funktionen möjliggör transkription i realtid av ljudströmmar till text. Dina program, verktyg eller enheter kan använda, visa och vidta åtgärder för den här textinmatningen. Den här tjänsten fungerar sömlöst med text-till-tal (talsyntes) och talöversättningsfunktioner.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 2854f4e8d91164c8ae1f35761d6f605cae725245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052635"
---
# <a name="what-is-speech-to-text"></a>Vad är tal-till-text?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Tal-till-text från taltjänsten, även kallad taligenkänning, möjliggör transkription i realtid av ljudströmmar till text. Dina program, verktyg eller enheter kan använda, visa och vidta åtgärder för den här texten som kommandoinmatning. Den här tjänsten drivs av samma igenkänningsteknik som Microsoft använder för Cortana- och Office-produkter. Det fungerar sömlöst med <a href="./speech-translation.md" target="_blank">översättning <span class="docon docon-navigate-external x-hidden-focus"></span> </a> och <a href="./text-to-speech.md" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> text-till-tal-tjänsterbjudanden.</a> En fullständig lista över tillgängliga tal-till-text-språk finns i [språk som stöds](language-support.md#speech-to-text).

Tal-till-text-tjänsten som standard använder den universella språkmodellen. Den här modellen har tränats med hjälp av Microsoft-ägda data och distribueras i molnet. Det är optimalt för konversations- och diktamensscenarier. När du använder tal-till-text för igenkänning och transkription i en unik miljö kan du skapa och träna anpassade akustiska modeller, språk- och uttalsmodeller. Anpassning är användbart för att ta itu med omgivningsbuller eller branschspecifikt ordförråd.

> [!NOTE]
> Bing Speech avvecklades den 15 oktober 2019. Om dina program, verktyg eller produkter använder API:erna för Bing-tal har vi skapat guider som hjälper dig att migrera till taltjänsten.
> - [Migrera från Bing-tal till taltjänsten](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Komma igång med tal till text

Tal-till-text-tjänsten är tillgänglig via [Tal-SDK](speech-sdk.md). Det finns flera vanliga scenarier tillgängliga som snabbstarter, på olika språk och plattformar:

 - [Snabbstart: Känna igen tal med mikrofoningång](quickstarts/speech-to-text-from-microphone.md)
 - [Snabbstart: Känna igen tal från en fil](quickstarts/speech-to-text-from-file.md)
 - [Snabbstart: Känna igen tal som lagras i blob-lagring](quickstarts/from-blob.md)

Om du föredrar att använda rest-tjänsten tal till text läser du [REST-API:er](rest-speech-to-text.md).

## <a name="tutorials-and-sample-code"></a>Självstudier och exempelkod

När du har haft en chans att använda taltjänsten kan du prova vår handledning som lär dig hur du känner igen avsikter från tal med tal-SDK och LUIS.

- [Självstudiekurs: Känna igen avsikter från tal med tal-SDK och LUIS med C #](how-to-recognize-intents-from-speech-csharp.md)

Exempelkod för Tal-SDK finns på GitHub. Dessa exempel täcker vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel-shot erkännande, och arbeta med anpassade modeller.

- [Exempel på tal till text (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Prov för batchutskrift (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Anpassning

Förutom standardmodellen Taltjänst kan du skapa anpassade modeller. Anpassning hjälper till att övervinna hinder för taligenkänning, som talstil, ordförråd och bakgrundsljud, se [Anpassat tal](how-to-custom-speech.md). Anpassningsalternativen varierar beroende på språk/språk, se [språk som stöds](supported-languages.md) för att verifiera supporten.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Nästa steg

- [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
- [Hämta tal-SDK](speech-sdk.md)
