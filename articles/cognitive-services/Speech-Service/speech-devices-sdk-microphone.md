---
title: Rekommendationer för SDK-mikrofonmatris för talenheter
titleSuffix: Azure Cognitive Services
description: Rekommendationer för SDK-mikrofonmatriser för talenheter. Dessa matrisgeometrier rekommenderas för användning med Microsoft Audio Stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168130"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Rekommendationer för SDK-mikrofon för talenheter

I den här artikeln får du lära dig hur du utformar en mikrofonmatris för Speech Devices SDK.

Speech Devices SDK fungerar bäst med en mikrofonmatris som har utformats enligt följande riktlinjer, inklusive mikrofongeometri och komponentval. Vägledning ges också om integration och elektriska överväganden.

## <a name="microphone-geometry"></a>Mikrofongeometri

Följande matrisgeometrier rekommenderas för användning med Microsoft Audio Stack. Placeringen av ljudkällor och avvisande av omgivningsljud förbättras med större antal mikrofoner med beroenden av specifika program, användarscenarier och enhetsformfaktorn.

|     | Cirkulär matris |     | Linjär array |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#Mikrofoner | 7 | 4 | 4 | 2 |
| Geometri | 6 Yttre, 1 Mitt, Radie = 42,5 mm, jämnt fördelade | 3 Yttre, 1 Mitt, Radie = 42,5 mm, jämnt fördelade | Längd = 120 mm, Avstånd = 40 mm | Avstånd = 40 mm |

Mikrofonkanaler bör beställas enligt den numrering som avbildas för varje matris ovan, ökar från 0. Microsoft Audio Stack kräver ytterligare en referensström av ljuduppspelning för att utföra ekoavbokning.

## <a name="component-selection"></a>Val av komponent

Mikrofonkomponenter bör väljas för att korrekt återge en signal utan buller och distorsion.

De rekommenderade egenskaperna när du väljer mikrofoner är:

| Parameter | Rekommenderas |
| --------- | ----------- |
| Snr | \>= 65 dB (1 kHz signal 94 dBSPL, A-viktat brus) |
| Amplitudmatchning | ± 1 dB @ 1 kHz |
| Matchning av fas | ± 2° @ 1 kHz |
| Akustisk överbelastningspunkt (AOP) | \>= 120 dBSPL (THD = 10%) |
| Bithastighet | Minst 24-bitars |
| Samplingsfrekvens | Minst 16 kHz\* |
| Frekvensomfång | ± 3 dB, 200-8000 Hz flytande mask\* |
| Tillförlitlighet | Temperaturområde för förvaring -40°C till 70°C<br />Drifttemperaturområde -20°C till 55°C |

\*_Högre provtagningshastigheter eller "bredare" frekvensområden kan vara nödvändiga för högkvalitativa kommunikationsprogram (VoIP)_

Bra komponentval måste paras ihop med god elektroakustisk integration för att undvika att försämra prestandan hos de komponenter som används. Unika användningsfall kan också kräva ytterligare krav (t.ex. driftstemperaturområden).

## <a name="microphone-array-integration"></a>Integrering av mikrofonmatris

Mikrofonmatrisens prestanda när den är integrerad i en enhet skiljer sig från komponentspecifikationen. Det är viktigt att se till att mikrofonerna matchas väl efter integration. Därför bör enhetens prestanda som mäts efter en fast förstärkning eller EQ uppfylla följande rekommendationer:

| Parameter          | Rekommenderas                                        |
| ------------------ | -------------------------------------------------- |
| Snr                | \>63 dB (1 kHz signal 94 dBSPL, A-viktat brus) |
| Utgångskänslighet | -26 dBFS/Pa @ 1 kHz (rekommenderas)                  |
| Amplitudmatchning | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5:e ordningen             |
| Frekvensomfång | ± 6 dB, 200-8000 Hz flytande mask\*\*              |

\*\*_En låg distorsion högtalare krävs för att mäta THD (t.ex._

\*\*_"Bredare" frekvensområden kan vara nödvändiga för högkvalitativa kommunikationsprogram (VoIP)_

## <a name="speaker-integration-recommendations"></a>Rekommendationer för integrering av högtalare

Eftersom ekoavbokning är nödvändig för taligenkänningsenheter som innehåller högtalare, ges ytterligare rekommendationer för val och integrering av högtalare.

| Parameter | Rekommenderas |
| --------- | ----------- |
| Linjäritet överväganden | Ingen icke-linjär bearbetning efter högtalarreferens, annars krävs en maskinvarubaserad loopback-referensström |
| Loopback för högtalare | Tillhandahålls via WASAPI, privata API: er, anpassade ALSA plug-in (Linux), eller tillhandahålls via firmware kanal |
| THD% | 3. Oktav Band minst 5: e ordningen, 70 dBA Uppspelning @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Ekokoppling till mikrofoner | \>-10 dB TCLw med ITU-T G.122 Bilaga B.4-metoden, normaliserad till mikrofonnivå<br />TCLw = TCLwmeasured \+ (uppmätt nivå - målutdatakänslighet)<br />TCLw = TCLwmeasured \+ (uppmätt nivå - (-26)) |

## <a name="integration-design-architecture"></a>Arkitektur för integrationsdesign

Följande riktlinjer för arkitektur är nödvändiga när mikrofoner integreras i en enhet:

| Parameter | Rekommendation |
| --------- | -------------- |
| Likhet med mic-port | Alla mikrofonportar är lika långa i matrisen |
| Dimensioner för micport | Portstorlek Ø0,8-1,0 mm. Portlängd / \< Port diameter 2 |
| Mic tätning         | Tätningspackningar implementeras jämnt i stack-up. Rekommendera \> 70% kompressionsförhållande för skumpackningar |
| Mic Tillförlitlighet     | Maskan ska användas för att förhindra damm och inträngning (mellan PCB för bottenportade mikrofoner och tätningspackning/övre lock) |
| Isolering av mikrofon       | Gummipackningar och vibrationskoppling genom struktur, särskilt för att isolera vibrationsvägar på grund av integrerade högtalare |
| Provtagning Klocka      | Enhetsljud måste vara fritt från jitter och avbrott med låg drift |
| Postkapacitet   | Enheten måste kunna spela in enskilda kanallysströmmar samtidigt |
| USB                 | Alla USB-ljudingångenheter måste ställa in beskrivningar enligt [USB Audio Devices Rev3-specifikationen](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Mikrofongeometri | Drivrutiner måste implementera [mikrofonmatrisgeometribeskrivare](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) korrekt |
| Upptäckbarhet     | Enheter får inte ha några oupptäckta eller okontrollerbara maskinvaru-, firmware- eller tredjepartsprogrambaserade icke-linjära ljudbehandlingsalgoritmer till/från enheten |
| Spela in format      | Insamlingsformat måste använda en samplingsfrekvens på minst 16 kHz och rekommenderat 24-bitarsdjup |

## <a name="electrical-architecture-considerations"></a>Elektriska arkitekturöverväganden

I förekommande fall kan matriser vara anslutna till en USB-värd (till exempel en SoC som kör Microsoft Audio Stack) och gränssnitt till taltjänster eller andra program.

Maskinvarukomponenter som PDM-till-TDM-konvertering bör säkerställa att mikrofonernas dynamiska omfång och SNR bevaras i re-samplers.

Usb Audio Class 2.0 med hög hastighet bör stödjas i alla ljud-MCUs för att ge den bandbredd som krävs för upp till sju kanaler med högre samplingshastigheter och bitdjup.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om SDK-talenheterna](speech-devices-sdk.md)
