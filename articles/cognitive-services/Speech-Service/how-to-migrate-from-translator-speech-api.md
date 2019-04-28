---
title: Migrera från API för Talöversättning till Speech-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du migrerar dina program från Translator Speech API till Speech Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: 35f970e81d27511bd35610bc2988a5ea4832906b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995753"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrera från API för Talöversättning till Speech-tjänsten

Använd den här artikeln för att migrera dina program från Microsoft Translator Speech API till den [Speech Service](index.yml). Den här guiden beskrivs skillnaderna mellan Translator Speech API och Speech Service och föreslår strategier för att migrera dina program.

> [!NOTE]
> Din prenumerationsnyckel för Translator Speech API godkännas inte av Speech-tjänsten. Du behöver skapa en ny Speech Services-prenumeration.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

| Funktion                                           | Translator Speech API                                  | Speech Services | Information                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Översättning till text                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Översättning till tal                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Global slutpunkt                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Speech Services erbjuder inte en global slutpunkt. En global slutpunkt kan automatiskt dirigera trafik till närmaste regionala slutpunkten, minska svarstiden i ditt program.                                                    |
| Regionala slutpunkter                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tidsgräns för anslutning                             | 90 minuter                                               | Obegränsade med SDK: N. 10 minuter med en WebSockets-anslutning.                                                                                                                                                                                                                                                                                   |
| Auth-nyckel i huvudet                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Flera språk som är översatt på en enskild begäran | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK: er som är tillgängliga                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Se den [Speech Services dokumentation](index.yml) för tillgängliga SDK: erna.                                                                                                                                                    |
| WebSockets anslutningar                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Språk-API                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Speech Services har stöd för samma intervallet för språk som beskrivs i den [Translator API-referens för språk](../translator-speech/languages-reference.md) artikeln. |
| Siris Filter och markör                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM som indata                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Andra filtyper som indata                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Ofullständiga resultat                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tidsinställning info                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| Korrelations-ID                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Anpassade talmodeller                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Speech Services ger anpassade talmodeller som gör att du kan anpassa taligenkänning till din organisations unika ordförråd.                                                                                                                                           |
| Anpassade översättningsmodeller                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Prenumerera på Microsoft Text Translation-API kan du använda [anpassad Translator](https://www.microsoft.com/translator/business/customization/) att använda dina egna data för exaktare översättningar.                                                 |

## <a name="migration-strategies"></a>Migreringsstrategier

Om du eller din organisation har du program i utveckling eller produktion som använder Translator Speech API kan uppdatera du dem för att använda Speech-tjänsten. Se den [Speech Service](index.yml) dokumentationen för SDK: er, kodexempel och självstudier. Tänk på följande när du migrerar:

* Speech Services erbjuder inte en global slutpunkt. Kontrollera om ditt program fungerar effektivt när den använder en enda regional slutpunkt för alla dess trafik. Annars kan du använda geoplats för att fastställa den effektivaste slutpunkten.

* Om ditt program använder långlivade anslutningar och kan inte använda de tillgängliga SDK: er, kan du använda en WebSockets-anslutning. Hantera 10 minuters timeout-gränsen genom att ansluta vid rätt tidpunkt.

* Om ditt program använder Translator Text API och Translator Speech API för att aktivera anpassade översättningsmodeller kan du lägga till kategori-ID: N direkt med hjälp av Speech-tjänsten.

* Till skillnad från Translator Speech API slutföra Speech Services översättningar till flera språk i en enskild begäran.

## <a name="next-steps"></a>Nästa steg

* [Prova Speech Services kostnadsfritt](get-started.md)
* [Snabbstart: Känna igen tal i en UWP-app med hjälp av tal-SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Se också

* [Vad är Speech Service](overview.md)
* [Taltjänster och tal SDK-dokumentation](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
