---
title: Funktionslista för Azure Media Player
description: En funktionsreferens för Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727234"
---
# <a name="feature-list"></a>Funktionslista #
Här är listan över testade funktioner och funktioner som inte stöds:

|                                         | Testade | DELVIS TESTAD | Oprövad | Unsupported | ANTECKNINGAR                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Uppspelning                                |        |                  |          |             |                                                                                                                      |
| Grundläggande uppspelning på begäran                | X      |                  |          |             | Stöder endast strömmar från Azure Media Services                                                                      |
| Grundläggande liveuppspelning                     | X      |                  |          |             | Stöder endast strömmar från Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Stöder Azure Media Services Key Delivery Service                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Stöder Azure Media Services Key Delivery Service                                                                   |
| Widevine                                |        | X                |          |             | Stöder Widevine PSSH-rutor som beskrivs i manifest                                                                    |
| FairPlay                                |        | X                |          |             | Stöder Azure Media Services Key Delivery Service                                                                   |
| Techs                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Flash Reserv (blinkar)                | X      |                  |          |             | Alla funktioner är inte tillgängliga på den här tekniken.                                                                         |
| Silverlight Reserv SilverlightsS      | X      |                  |          |             | Alla funktioner är inte tillgängliga på den här tekniken.                                                                         |
| Inbyggt HLS-genomströmning (Html5)         |        | X                |          |             | Alla funktioner är inte tillgängliga på den här tekniken på grund av plattformsbegränsningar.                                            |
| Funktioner                                |        |                  |          |             |                                                                                                                      |
| API-stöd                             | X      |                  |          |             | Se listan över kända problem                                                                                                |
| Grundläggande användargränssnitt                                | X      |                  |          |                                                                                                                                    |
| Initiering via JavaScript       | X      |                  |          |             |                                                                                                                      |
| Initiering via videotagg        |        | X                |          |             |                                                                                                                      |
| Segmentadressering - Tidsbaserad         | X      |                  |          |             |                                                                                                                      |
| Segmentadressering - Indexbaserad        |        |                  |          | X           |                                                                                                                      |
| Segmentadressering - Byte Baserat         |        |                  |          | X           |                                                                                                                      |
| Url-brännare för Azure Media Services       |        | X                |          |             |                                                                                                                      |
| Tillgänglighet - Bildtexter och undertexter  |        | X                |          |             |  WebVTT stöds för on demand, live CEA 708 delvis testad                                                       |
| Hjälpmedel - Snabbtangenter                 | X      |                  |          |             |                                                                                                                      |
| Tillgänglighet - hög kontrast           |        | X                |          |             |                                                                                                                      |
| Hjälpmedel - Flikfokus               |        | X                |          |             |                                                                                                                      |
| Felmeddelanden                         |        | X                |          |             | Felmeddelanden är inkonsekventa mellan tekniker                                                                         |
| Utlösande händelse                        | X      |                  |          |             |                                                                                                                      |
| Diagnostik                             |        | X                |          |             | Diagnostikinformation är endast tillgänglig på AzureHtml5JS-tekniken och delvis tillgänglig på SilverlightSS-tekniken. |
| Anpassningsbar teknisk order                 |        | X                |          |             |                                                                                                                      |
| Heuristik - Grundläggande                      | X      |                  |          |             |                                                                                                                      |
| Heuristik - Anpassning              |        |                  | X        |             | Anpassning är endast tillgänglig med AzureHtml5JS-tekniken.                                                          |
| Avbrott                         | X      |                  |          |             |                                                                                                                      |
| Välj bithastighet                          | X      |                  |          |             | Det här API:et är endast tillgängligt på AzureHtml5JS- och FlashSS-teknikerna.                                                    |
| Ström med flera ljud                      |        | X                |          |             | Programmatisk ljudväxling stöds på AzureHtml5JS- och FlashSS-teknikerna och är tillgängligt via UI-val på AzureHtml5JS, FlashSS och native Html5 (i Safari).  De flesta plattformar kräver samma codec privata data för att byta ljudströmmar (samma codec, kanal, samplingsfrekvens, etc.). |
| Lokalisering av användargränssnitt                         |        | X                |          |             |                                                                                                                      |
| Uppspelning av flera instanser                 |        |                  |          | X           | Det här scenariot kan fungera för vissa tekniker men stöds för närvarande inte och är oprövat. Du kan också få detta att fungera med iframes |
| Stöd för annonser                             |        | x                |          |             | AMP stöder införandet av linjära annonser före och efter kastet från VAST-kompatibla annonsservrar för VOD i AzureHtml5JS-tekniken |
| Analytics                               |        | X                |          |             | AMP ger möjlighet att lyssna på analys- och diagnostikhändelser för att skicka till en Analytics-serverning som du väljer.  Alla händelser och egenskaper är inte tillgängliga för tekniker på grund av plattformsbegränsningar.                                                                            |
| Anpassade skal                            |        |                  | X        |             | Aktivera inställningskontroller till false i AMP och använda din egen HTML och CSS.           |
| Sök Bar Skrubbning                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Endast ljud                              |        |                  |          | X           | Kan fungera i vissa tekniker för adaptiv streaming men stöds för närvarande inte och fungerar inte i AzureHtml5JS. Progressiv MP3-uppspelning kan fungera med HTML5-tekniken om plattformen stöder den.                                                                                                        |
| Endast video                              |        |                  |          | X           | Kan fungera i vissa tekniker för adaptiv streaming men stöds för närvarande inte och fungerar inte i AzureHtml5JS.      |
| Presentation med flera perioder               |        |                  |          | X                                                                                                                                  |
| Flera kameravinklar                  |        |                  |          | X           |                                                                                                                      |
| Uppspelningshastighet                          |        | X                |          |             | Uppspelningshastighet stöds i de flesta scenarier utom det mobila fallet på grund av en partiell bugg i Chrome                 |

## <a name="next-steps"></a>Nästa steg ##
- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)