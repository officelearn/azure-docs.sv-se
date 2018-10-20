---
title: Migrera från API för Talöversättning till Speech-tjänsten
titleSuffix: Azure Cognitive Services
description: Använd det här avsnittet för att migrera dina program från Translator Speech API till Speech-tjänsten.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: a35c4a7f2d2b64bd3650df1f18d08da4ea191e3a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466400"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrera från API för Talöversättning till Speech-tjänsten

Använd den här artikeln för att migrera dina program från Microsoft Translator Speech API till den [Speech Service](index.yml). Den här guiden beskrivs skillnaderna mellan Translator Speech API och Speech Service och föreslår strategier för att migrera dina program.

> [!NOTE]
> Din prenumerationsnyckel för Translator Speech API godkännas inte av Speech-tjänsten. Du behöver att starta en ny Speech Service-prenumeration.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

| Funktion                                           | Translator Speech API                                  | Tjänst för taligenkänning | Information                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Översättning till text                               | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Översättning till tal                             | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Global slutpunkt                                   | : heavy_check_mark:                                              | : heavy_minus_sign:                 | Med Taltjänsten erbjuder för närvarande inte en global slutpunkt. En global slutpunkt kan automatiskt dirigera trafik till närmaste regionala slutpunkten, minska svarstiden i ditt program.                                                    |
| Regionala slutpunkter                                | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tidsgräns för anslutning                             | 90 minuter                                               | Obegränsade med SDK: N. 10 minuter med en websocket-anslutning                                                                                                                                                                                                                                                                                   |
| Auth-nyckel i huvudet                                | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Flera språk som är översatt på en enskild begäran | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK: er som är tillgängliga                                    | : heavy_minus_sign:                                              | : heavy_check_mark:                 | Se den [Speech Service dokumentation](index.yml) för tillgängliga SDK: erna.                                                                                                                                                    |
| Websocket-anslutningar                             | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Språk-API                                     | : heavy_check_mark:                                              | : heavy_minus_sign:                 | Speech-tjänsten stöder samma intervallet för språk som beskrivs i den [Translator API-referens för språk](../translator-speech/languages-reference.md) artikeln. |
| Siris Filter och markör                       | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM som indata                                 | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Andra filtyper som indata                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Ofullständiga resultat                                   | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tidsinställning Info                                       | : heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| Korrelations-ID                                    | : heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Anpassade talmodeller                              | : heavy_minus_sign:                                              | : heavy_check_mark:                 | Med Taltjänsten ger anpassade talmodeller som gör att du kan anpassa taligenkänning till din organisations unika ordförråd.                                                                                                                                           |
| Anpassade Översättningsmodeller                         | : heavy_minus_sign:                                              | : heavy_check_mark:                 | Prenumerera på Microsoft Text Translation-API kan du använda [anpassad Translator](https://www.microsoft.com/translator/business/customization/) (för närvarande i förhandsversion) för att använda dina egna data för exaktare översättningar.                                                 |

## <a name="migration-strategies"></a>Migreringsstrategier

Om du eller din organisation har du program i utveckling eller produktion som använder Translator Speech API kan uppdatera du dem för att använda Speech-tjänsten. Se den [Speech Service](index.yml) dokumentationen för SDK: er, kodexempel och självstudier. Här följer några saker att tänka på vid migrering:

* Med Taltjänsten erbjuder för närvarande inte en global slutpunkt. Du behöver att avgöra om ditt program fungerar effektivt med hjälp av en enda regional slutpunkt för alla dess trafik. Om det inte behöver använda geoplats för att avgöra den effektivaste slutpunkten.

* Om ditt program använder långlivade anslutningar och kan inte använda de tillgängliga SDK: er, kan du använda en websocket-anslutning och hantera 10 minuters timeout-gränsen genom att ansluta vid rätt tidpunkt.

* Om programmet använder Translator Text API och Translator Speech API för att aktivera anpassade översättningsmodeller, kommer du att kunna lägga till ”kategori-ID: N direkt med Speech-tjänsten.

* Med Taltjänsten kan slutföra översättningar till flera språk i en enda begäran, till skillnad från Translator Speech API.

## <a name="next-steps"></a>Nästa steg

* [Prova Speech Service kostnadsfritt](get-started.md)
* [Snabbstart: Känna igen tal i en UWP-app med hjälp av tal-SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Se också

* [Vad är Speech-tjänsten](overview.md)
* [Med Taltjänsten och SDK-dokumentation](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
