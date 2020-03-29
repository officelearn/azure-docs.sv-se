---
title: Migrera från translator-tal-API:et till taltjänsten
titleSuffix: Azure Cognitive Services
description: Läs om hur du migrerar dina program från Translator Speech API till taltjänsten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560906"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrera från translator-tal-API:et till taltjänsten

Använd den här artikeln om du vill migrera dina program från Microsoft Translator Speech API till [taltjänsten](index.yml). Den här guiden beskriver skillnaderna mellan representations-api:et och taltjänsten och föreslår strategier för att migrera dina program.

> [!NOTE]
> Prenumerationsnyckeln för Translator Speech API accepteras inte av taltjänsten. Du måste skapa en ny prenumeration på taltjänsten.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

| Funktion                                           | Translator Speech API                                  | Tjänst för taligenkänning | Information                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Översättning till text                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Översättning till tal                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Global slutpunkt                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Taltjänsten erbjuder ingen global slutpunkt. En global slutpunkt kan automatiskt dirigera trafik till närmaste regionala slutpunkt, vilket minskar svarstiden i ditt program.                                                    |
| Regionala slutpunkter                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tidsgräns för anslutning                             | 90 minuter                                               | Obegränsad med SDK. 10 minuter med en WebSockets-anslutning.                                                                                                                                                                                                                                                                                   |
| Auth-tangenten i sidhuvudet                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Flera språk översatta i en enda begäran | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK:er tillgängliga                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Se [taltjänstdokumentationen](index.yml) för tillgängliga SDK:er.                                                                                                                                                    |
| WebSockets-anslutningar                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API för språk                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Taltjänsten stöder samma språkintervall som beskrivs i [referensartikeln för Translator API-språk.](../translator-speech/languages-reference.md) |
| Svordomar filter och markör                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM som ingång                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Andra filtyper som indata                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Partiella resultat                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Information om tidsinställning                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| Korrelations-ID                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Anpassade talmodeller                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Taltjänsten erbjuder anpassade talmodeller som gör att du kan anpassa taligenkänning till organisationens unika ordförråd.                                                                                                                                           |
| Anpassade översättningsmodeller                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Genom att prenumerera på Microsoft Text Translation API kan du använda [Custom Translator](https://www.microsoft.com/translator/business/customization/) för att använda dina egna data för mer exakta översättningar.                                                 |

## <a name="migration-strategies"></a>Migreringsstrategier

Om du eller din organisation har program under utveckling eller produktion som använder Translator Speech API bör du uppdatera dem för att använda taltjänsten. Se [taltjänstdokumentationen](index.yml) för tillgängliga SDK:er, kodexempel och självstudier. Tänk på följande när du migrerar:

* Taltjänsten erbjuder ingen global slutpunkt. Ta reda på om ditt program fungerar effektivt när det använder en enda regional slutpunkt för all trafik. Om inte, använd geolokalisering för att bestämma den mest effektiva slutpunkten.

* Om programmet använder långlivade anslutningar och inte kan använda tillgängliga SDK:er kan du använda en WebSockets-anslutning. Hantera 10-minuters tidsgränsen genom att återansluta vid lämpliga tidpunkter.

* Om ditt program använder Translator Text API och Translator Speech API för att aktivera anpassade översättningsmodeller kan du lägga till kategori-ID:er direkt med hjälp av taltjänsten.

* Till skillnad från Translator Speech API kan taltjänsten slutföra översättningar till flera språk i en enda begäran.

## <a name="next-steps"></a>Nästa steg

* [Prova Taltjänst gratis](get-started.md)
* [Snabbstart: Känna igen tal i en UWP-app med tal-SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Se även

* [Vad är taltjänsten](overview.md)
* [Taltjänst och Tal-SDK-dokumentation](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
