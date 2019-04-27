---
title: Migrera från Bing-tal till Azure Speech Services
titleSuffix: Azure Cognitive Services
description: Lär dig hur du migrerar från en befintlig prenumeration på Bing-tal till Azure Speech Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 6324da55c8af4934185fa39a106939844788adba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653724"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrera från Bing-tal till Taltjänst

Använd den här artikeln för att migrera dina program från Bing-Taligenkänning till Speech-tjänsten.

Den här artikeln beskrivs skillnaderna mellan tal-API: er för Bing och Speech Services och ger förslag på strategier för att migrera dina program. Din prenumerationsnyckel för taligenkänning för Bing fungerar inte med Taltjänsten; Du behöver en ny Speech Services-prenumeration.

En enda Speech Services prenumerationsnyckel ger åtkomst till följande funktioner. Varje funktion mäts separat, så att du bara debiteras för de funktioner du använder.

* [Tal till text](speech-to-text.md)
* [Anpassat tal till text](https://cris.ai)
* [Text till tal](text-to-speech.md)
* [Anpassad text till talade röster](how-to-customize-voice-font.md)
* [Talöversättning](speech-translation.md) (omfattar inte [textöversättning](../translator/translator-info-overview.md))

Den [tal SDK](speech-sdk.md) är en funktionell ersättning för klientbibliotek för Bing-taligenkänning, men använder ett annat API.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

Speech Services liknar huvudsakligen Bing-taligenkänning med följande skillnader.

Funktion | Bing-taligenkänning | Speech Services | Information
-|-|-|-
C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | Speech Services har stöd för Windows och Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Services har stöd för Android- och taligenkänning enheter.
C#-SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Services har stöd för Windows 10, Universal Windows Platform (UWP) och .NET Standard 2.0.
Kontinuerlig taligenkänning | 10 minuter | Obegränsat (med SDK) | Både tal för Bing och Speech Services WebSockets protokoll stöd för upp till 10 minuter per anrop. Men SDK tal automatiskt återansluts vid timeout eller koppla från.
Partiell eller mellanliggande resultat | :heavy_check_mark: | :heavy_check_mark: | Med WebSockets-protokollet eller SDK.
Anpassade talmodeller | :heavy_check_mark: | :heavy_check_mark: | Bing-taligenkänning kräver en separat prenumeration för anpassat tal.
Anpassade rösttyper | :heavy_check_mark: | :heavy_check_mark: | Bing-taligenkänning kräver en separat anpassad Voice-prenumeration.
Få en 24-kHz | : heavy_minus_sign: | :heavy_check_mark:
Igenkänning av talavsikt | Kräver separat LUIS-API-anrop | Integrerat (SDK) |  Du kan använda en LUIS-nyckel med Speech-tjänsten.
Enkel avsiktsigenkänning | : heavy_minus_sign: | :heavy_check_mark:
Batch transkription av länge ljudfiler | : heavy_minus_sign: | :heavy_check_mark:
Igenkänningsläge | Manuell via slutpunkt URI | Automatisk | Igenkänning av läge är inte tillgänglig i Speech Service.
Slutpunkten ort | Global | Regional | Regionala slutpunkter förbättra svarstiden.
REST API:er | :heavy_check_mark: | :heavy_check_mark: | Speech Services REST API: er är kompatibla med Bing-tal (olika slutpunkt). REST API: er stöd för text till tal och begränsade funktioner för tal till text.
WebSockets protokoll | :heavy_check_mark: | :heavy_check_mark: | API för taligenkänning tjänster WebSockets är kompatibel med Bing-tal (olika slutpunkt). Migrera till tal SDK: N om det är möjligt, för att förenkla din kod.
Tjänst-till-tjänst-API-anrop | :heavy_check_mark: | : heavy_minus_sign: | Tillhandahålla i Bing-taligenkänning via C#-Tjänstbibliotek.
SDK med öppen källkod | :heavy_check_mark: | : heavy_minus_sign: |

Speech Services använder en prismodell som baseras på tidpunkt (i stället för en transaktion som bygger modell). Se [Speech Services priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) mer information.

## <a name="migration-strategies"></a>Migreringsstrategier

Om du eller din organisation har program i utveckling eller produktion som använder en Bing-Taligenkänning, bör du uppdatera dem för att använda Speech Services så snart som möjligt. Se den [Speech Services dokumentation](index.yml) för tillgängliga SDK: er, kodexempel och självstudier.

Speech Services [REST API: er](rest-apis.md) är kompatibla med API: er för Bing-tal. Om du använder Bing Speech REST-API: er, behöver du bara ändra REST-slutpunkten och växla till en prenumerationsnyckel för Speech Services.

Speech Services WebSockets-protokoll är också kompatibla med de som används av Bing-taligenkänning. Vi rekommenderar att för nyutveckling bör du använda tal SDK i stället för WebSockets. Det är en bra idé att migrera befintliga kod i SDK samt. Som med REST API: er kräver befintlig kod som använder Bing-taligenkänning via WebSockets dock endast en ändring i slutpunkt och en nyckel som har uppdaterats.

Om du använder ett klientbibliotek för Bing-taligenkänning för ett specifikt programmeringsspråk, migrera till den [tal SDK](speech-sdk.md) kräver ändringar i ditt program, eftersom API: et är olika. Tal-SDK kan göra din kod enklare, samtidigt som också ger dig tillgång till nya funktioner.

Tal-SDK stöder för närvarande, C# (Windows 10, UWP, .NET Standard), Java (Android och anpassade enheter), Objective C (iOS), C++-Windows och Linux- och JavaScript. API: er för alla plattformar som liknar varandra, underlätta utveckling för flera plattformar.

Speech Services erbjuder inte en global slutpunkt. Kontrollera om ditt program fungerar effektivt när den använder en enda regional slutpunkt för alla dess trafik. Annars kan du använda geoplats för att fastställa den effektivaste slutpunkten. Du behöver en separat Speech Services-prenumeration i varje region som du använder.

Om ditt program använder långlivade anslutningar och kan inte använda en tillgängliga SDK: N, kan du använda en WebSockets-anslutning. Hantera 10 minuters timeout-gränsen genom att ansluta vid rätt tidpunkt.

Kom igång med tal-SDK:

1. Ladda ned den [tal SDK](speech-sdk.md).
1. Gå igenom Speech Services [snabbstartguider](quickstart-csharp-dotnet-windows.md) och [självstudier](how-to-recognize-intents-from-speech-csharp.md). Också titta på den [kodexempel](samples.md) att få med de nya API: er.
1. Uppdatera programmet för användning med Speech Services.

## <a name="support"></a>Support

Bing-taligenkänning kunder bör kontakta kundsupporten genom att öppna en [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Du kan även kontakta oss om ditt behov av support kräver en [tekniskt supportavtal](https://azure.microsoft.com/support/plans/).

Speech Service, SDK och API-stöd finns Speech Services [supportsidan](support.md).

## <a name="next-steps"></a>Nästa steg

* [Prova Speech Services kostnadsfritt](get-started.md)
* [Snabbstart: Känna igen tal i en UWP-app med hjälp av tal-SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Se också
* [Speech Services viktig information](releasenotes.md)
* [Vad är Speech Service](overview.md)
* [Taltjänster och tal SDK-dokumentation](speech-sdk.md#get-the-sdk)
