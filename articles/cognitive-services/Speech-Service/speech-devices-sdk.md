---
title: Om enheterna som tal SDK
description: 'En introduktion till SDK: N för tal-enheter.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 97a5d00d43ac6a9d4b28ba23ca69d9b67e1bdf6f
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049807"
---
# <a name="about-the-speech-devices-sdk-preview"></a>Om enheterna som tal SDK (förhandsversion)

Den [Microsoft Speech service](overview.md) fungerar med en mängd olika enheter och ljud datakällor. Nu kan du dra ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är redan utformad-biblioteket tillsammans med ett syfte mikrofoner development Kit. Ger dig kraften att snabbt testa nya voice-scenarier, gör tal Devices SDK det enkelt att integrera din enhet molnbaserade Microsoft Speech Service och skapa en enastående användarupplevelse för kunderna. 

SDK: N för tal enheter förbrukar den [tal SDK](speech-sdk.md), och använder tal SDK för att skicka ljud som bearbetas av våra avancerade ljud bearbetning-algoritmen från enhetens mikrofon-matris på den [Microsoft Speech Service](overview.md).  Den använder flera kanaler ljud för att ge mer exakta långt fält [taligenkänning](speech-to-text.md) via bruset Undertryckning, eko, beamforming och inaktivering genljudet.

Tal Devices SDK kan du skapa omgivande enheter med dina egna [anpassade wake word](speech-devices-sdk-create-kws.md), så Låt dig ledas som initierar någon interaktion från användaren är unikt för ditt varumärke. 

SDK: N underlättar en mängd olika voice-aktiverade scenarier, till exempel enhet till skrivordning system, i butiken eller in-home assistenter och smart talare. Du kan svara på användare med text, tala tillbaka till dem i en standard eller [anpassade röst](how-to-customize-voice-font.md), ange sökresultaten [översätta](speech-translation.md) till andra språk och mycket mer. Vi ser fram emot att se vad du skapa!



## <a name="development-kit-providers"></a>Development kit-providers

Referensdesign för fullständiga, slutpunkt-till-slutpunkts-system. Mer kommer snart!

|||
|-|-|
|[![ROOBO-logotyp](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO tillhandahåller kompletta lösningar för AI-system för electric hushållsapparater, bilar, robotar, toys och andra branscher. ROOBOS referensdesign minska avsevärt utveckling tid till marknad via integrering med Microsoft Speech-tjänsten. [Besök ROOBO](http://ddk.roobo.com/)|

## <a name="next-steps"></a>Nästa steg

Kom igång genom att hämta en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/ai/) och registrera dig för tal Devices SDK.

> [!div class="nextstepaction"]
> [Registrera dig för tal Devices SDK](get-speech-devices-sdk.md)

