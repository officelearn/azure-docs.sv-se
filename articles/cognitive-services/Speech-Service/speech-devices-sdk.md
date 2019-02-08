---
title: Om enheterna som tal SDK – Speech Services
titleSuffix: Azure Cognitive Services
description: 'Kom igång med SDK: N för tal-enheter. Speech Services fungerar med en mängd olika enheter och ljud datakällor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är ett redan utformad bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d7ef018b376b96f967a065857839761fc5822239
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876318"
---
# <a name="about-the-speech-devices-sdk-preview"></a>Om enheterna som tal SDK (förhandsversion)

Den [Speech Service](overview.md) fungerar med en mängd olika enheter och ljud datakällor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är ett pretuned bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.

Med hjälp av tal Devices SDK kan du:
* Snabbt testa nya voice-scenarier.
* Integrera enkelt molnbaserad tal-tjänst i din enhet.
* Skapa en enastående användarupplevelse för kunderna.

SDK: N för tal enheter förbrukar den [tal SDK](speech-sdk.md). Den använder tal SDK för att skicka ljud som bearbetas av våra avancerade ljud bearbetning-algoritmen från enhetens mikrofon-matris på den [taltjänst](overview.md). Den använder flera ljud för att ge mer exakta långt fält [taligenkänning](speech-to-text.md) via bruset Undertryckning, eko, beamforming och dereverberation.

Du kan också använda tal Devices SDK för att skapa omgivande enheter som har en egen [anpassade wake word](speech-devices-sdk-create-kws.md), så Låt dig ledas som initierar någon interaktion från användaren är unikt för ditt varumärke.

Tal Devices SDK underlättar en mängd olika voice-aktiverade scenarier, till exempel enhet till skrivordning system, i butiken eller in-home assistenter och smart talare. Du kan svara på användare med text, tala tillbaka till dem i en standard eller [anpassade röst](how-to-customize-voice-font.md), ange sökresultaten [översätta](speech-translation.md) till andra språk och mycket mer. Vi ser fram emot att se vad du skapa!

## <a name="development-kit-providers"></a>Development kit-providers

Referensdesign dessa fullständiga, slutpunkt-till-slutpunkts-system är för närvarande tillgängliga:

|||
|-|-|
|[![ROOBO-logotyp](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO innehåller fullständig artificiell intelligens (AI) systemlösningar för electric hushållsapparater, bilar, robotar, toys och andra branscher. ROOBOS referensdesign minska avsevärt utveckling tid till marknad via integrering med Microsoft Speech-tjänsten. [Gå till ROOBO](http://ddk.roobo.com/).|

## <a name="next-steps"></a>Nästa steg

Kom igång genom att hämta en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/ai/) och registrera dig för tal Devices SDK.

> [!div class="nextstepaction"]
> [Registrera dig för tal Devices SDK](get-speech-devices-sdk.md)
