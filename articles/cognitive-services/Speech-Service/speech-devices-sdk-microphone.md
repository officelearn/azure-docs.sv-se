---
title: Tal Devices SDK mikrofon matris rekommendationer – Speech Services
titleSuffix: Azure Cognitive Services
description: Rekommendationer för tal Devices SDK mikrofon matris. Följande matris geometrier rekommenderas för användning med Microsoft ljud-stacken. Platsen för ljud källor och bakgrundsljud att avvisa bättre med större antal mikrofoner med beroenden på specifika program och användarscenarier formfaktorn för enheten.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 63dd64e900cf68e708032569ca75ac2e8b221491
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237003"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Tal enheter SDK mikrofon matris rekommendationer

I den här artikeln får du lära dig hur du utformar en mikrofon matris för tal enheter SDK.

SDK: N för tal enheter fungerar bäst med en mikrofon-matris som har utformats för enligt följande riktlinjer, inklusive mikrofon geometri och komponenten markerad. Vägledning ges också på integration och elektriska.

## <a name="microphone-geometry"></a>Mikrofon geometri

Följande matris geometrier rekommenderas för användning med Microsoft ljud-stacken. Platsen för ljud källor och bakgrundsljud att avvisa bättre med större antal mikrofoner med beroenden på specifika program och användarscenarier formfaktorn för enheten.

|          | Cirkulär matris    |       |  Linjär matris              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \# Mikrofoner  | 7                 | 4                 | 4              | 2              |
| geometri | 6 Outer, 1 Center, Radius = 42.5 mm, Evenly Spaced| 3 Outer, 1 Center, Radius = 42.5 mm, Evenly Spaced | Längd = 120 mm avstånd = 40 mm | Avstånd = 40 mm |

Mikrofon kanaler ska sorteras enligt numreringen visas för varje ovan matris, vilket ökar från 0.  Microsoft ljud Stack kräver en ytterligare referens dataström för ljuduppspelning att utföra eko.

## <a name="component-selection"></a>Komponenturvalet

Mikrofon komponenter ska väljas att korrekt återge en signal som är kostnadsfritt brus och störningar.

De rekommenderade egenskaperna när du väljer mikrofoner är:

| Parameter                         | Rekommenderas                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB (1 kHz signalen 94 dBSPL, A-viktade bruset)   |
| Amplitud matchar                | ± 1 dB @ 1 kHz                     |
| Matchning fasen                    | ± 2° @ 1 kHz                       |
| Akustiska överlagring punkt (AOP)     | \> 120 dBSPL (THD = 10%)          |
| Bithastighet                          | Minst 24-bitars                    |
| Samplingsfrekvens                     | Minsta 16 kHz\*                   |
| Directivity                       | Rundstrålande                   |
| Frekvens svar                | + 3 dB, 200 8000 Hz flytande Mask\*|
| Tillförlitlighet                       | Lagringsomfång temperatur 40 ° C till 70 ° C<br />Operativ temperaturintervall 20 ° C till 55 ° C  |

*\*Högre sampling betalning eller ”större” frekvensintervall kan krävas för program för hög kvalitet kommunikation (VoIP)*

Bra komponenturvalet måste kopplas till bra electroacoustic integrering för att undvika att försämra läsarens prestanda för de komponenter som används. Unika användningsfall kan också kräva ytterligare krav (till exempel: driva temperatur).

## <a name="microphone-array-integration"></a>Mikrofon matris-integrering

Prestanda för matriser när du har integrerat i en enhet och efter fast vinst eller EQ måste uppfylla följande rekommendationer:

|  Parameter        |    Rekommenderas |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB (1 kHz signalen 94 dBSPL, A-viktade bruset) |
|  Utdata känslighet  | -26 dBFS/Pa @ 1 kHz (rekommenderas) |
|  Amplitud matchar  | ± 2 dB, 200-8000 Hz |
|  Matchning fasen      | ± 5°, 200-8000 Hz |
| THD %                 | ≤ 1%, 200 8000 Hz, 94 dBSPL, 5 ordning |
|  Frekvens svar  | + 6 dB, 200 8000 Hz flytande Mask\* |

*\*”Större” frekvensintervall kan krävas för program för hög kvalitet kommunikation (VoIP)*

## <a name="speaker-integration-recommendations"></a>Rekommendationer för API-integration

Som eko krävs för tal för enheter som innehåller talare, finns ytterligare rekommendationer för val av talare och integrering.

| Parameter                         | Rekommenderas                       |
|-----------------------------------|-----------------------------------|
| Linearitet överväganden          | Inga icke-linjära bearbetning efter API-referens, annars en maskinvarubaserad loopback referens dataström är obligatoriskt  |
| Talare Loopback                  | Tillhandahålls via WASAPI, privat API: er, anpassade ALSA plugin-programmet (Linux) eller tillhandahålls via inbyggd programvara kanal      |
| THD %                              | 3: e oktavbanden minsta 5 ordningen, 70 dBA uppspelning @ 0,8 m ≤ 6.3%, 315 – 500 Hz ≤ 5% 630 5000 Hz                 |
| Echo-koppling till mikrofoner      | \> -10 dB TCLw med ITU-T G.122 bilaga B.4 metoden normaliseras till mic-nivå<br />TCLw = TCLwmeasured \+ (mätt nivå - rikta utdata känslighet)<br />TCLw = TCLwmeasured \+ (mätt nivå - (-26)) |

## <a name="integration-design-architecture"></a>Design-Integreringsarkitektur

Följande riktlinjer för arkitekturen krävs när de integrerar mikrofoner i en enhet:

| Parameter                         | Rekommendation                    |
|-----------------------------------|-----------------------------------|
| MIC-Port likheter               | Alla mikrofon portar är samma längd i matrisen    |
| MIC-Port dimensioner               | Port storlek Ø0.8 1.0 mm. Port längd / Port Diameter \< 2              |
| MIC försegling                       | Hur du skrivskyddar packningar enhetligt implementeras i stack upp. Rekommenderar \> 70% komprimeringsförhållandet för skum packningar     |
| MIC tillförlitlighet                   | Nät som ska användas för att förhindra damm och ingress (mellan PCB för nedre porteras mikrofoner och hur du skrivskyddar packning/upp cover)  |
| MIC-isolering                     | Gummipackningar och vibrationer Frikoppling via struktur, särskilt för att isolera eventuella vibrationer sökvägar på grund av inbyggda högtalare      |
| Sampling klockan                    | Enheten ljud får inte innehålla jitter och bortfall med låg drift    |
| Registrera funktionen                 | Enheten måste kunna registrera enskild kanal raw strömmar samtidigt |
| USB                               | Alla USB-enheter måste ställa in beskrivningar enligt den [USB-enheter Rev3-specifikationen ljud](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Mikrofon geometri               | Drivrutinerna måste implementera [mikrofon matris geometri beskrivningar](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) korrekt  |
| För att göra                   | Enheter får inte ha några undiscoverable eller fungerar maskinvara, inbyggd programvara eller 3 part programvarubaserad icke-linjära ljud bearbetning algoritmer till och från enheten|
| Avbilda Format                    | Avbilda format måste använda en minsta samplingsfrekvensen för 16 kHz och rekommenderade 24-bitars djup      |

## <a name="electrical-architecture-considerations"></a>Arkitektur för elektriska överväganden

Om tillämpligt, matriser kan anslutas till en USB-värd (till exempel en SoC som kör Microsoft ljud-stacken) och gränssnitt för att taltjänster eller andra program.

Maskinvarukomponenter som Kontaktar till TDM konvertering bör se till att dynamiskt omfång och SNR av mikrofonerna bevaras i ny insamling.

Snabba USB ljud klass 2.0 bör stödjas inom alla ljud MCU för att tillhandahålla den nödvändiga bandbredden för upp till sju kanaler med högre samplingsfrekvensen och bitars djup.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om tal Devices SDK](speech-devices-sdk.md)
