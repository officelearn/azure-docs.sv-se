---
title: Stöd för lokalisering | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om språk som stöds för tjänsterna i Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3b3733dfc32dae21ddcf4c5f73cddf9ad6b7fc59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334076"
---
# <a name="localization-support-in-azure-maps"></a>Stöd för lokalisering i Azure Maps

Azure Maps stöder olika språk och vyer baserat på land/region. Den här artikeln innehåller språk och vyer som stöds för att vägleda implementeringen av Dina Azure Maps-kartor.


## <a name="azure-maps-supported-languages"></a>Språk som stöds av Azure Maps

Azure Maps har lokaliserats på olika språk i sina tjänster. I följande tabell finns språkkoder som stöds för varje tjänst.  
  

| ID         | Namn                   |  Kartor | Search | Routning | Väder | Trafikincidenter | JS kartkontroll |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabiska                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (Indien)         |       |       |         |     ✓    |                   |                |
| bs-Ba      | Bosniska                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Baskiska                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulgariska              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Katalanska                |       |    ✓   |         |    ✓      |                   |                |
| zh-Hans (olika)    | Kinesiska (förenklad)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Kinesiska (Hongkong SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Kinesiska (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Kroatiska               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Tjeckiska                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Danska                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Nederländska (Belgien)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Nederländska (Nederländerna)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| sv-AU      | Engelska (Australien)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| sv-NZ      | Engelska (Nya Zeeland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Engelska (Storbritannien) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| sv-SE      | Engelska (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estniska               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finska                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Franska                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Franska (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galiciska               |       |    ✓   |         |         |                   |                |
| de-DE      | Tyska                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Grekiska                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Gujarati                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebreiska                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Ungerska              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| är-ÄR      | Isländska              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonesiska             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italienska                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japanska               |       |        |         |     ✓    |                   |                |
| kn-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazakiska                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreansk                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Latinamerikansk spanska |       |    ✓   |         |         |                   |                |
| lv-LV      | Lettiska                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litauiska             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Makedonska             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malajiska (latin)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-in      | Marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norska (bokmål)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| Ngt        | Neutral Ground Truth - Officiella språk för alla regioner i lokala skript om sådana finns |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral Ground Truth - Latin exonyms. Latinska skript kommer att användas om tillgängligt |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polska                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugisiska (Brasilien)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugisiska (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN (på andra plats)      | Punjabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumänska               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Ryska                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbiska (kyrillisk)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Serbiska (latinsk) (Serbien)        |       |       |         |     sr-latn (sr-latn)    |                   |                |
| sk-SK      | Slovakiska             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Slovenska              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spanska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Spanska (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Svenska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-in      | Tamil (Indien)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (Indien)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thailändska                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turkiska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrainska               |       |    ✓   |         |     ✓    |                   |                |
| din-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbekiska                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamesiska             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps-vyer som stöds

> [!Note]
> Den 1 augusti 2019 släpptes Azure Maps i följande länder/regioner:
>  * Argentina
>  * Indien
>  * Marocko
>  * Pakistan
>
> Efter den 1 augusti 2019 definierar **parametern Visa** det returnerade kartinnehållet för de nya regioner/länder som anges ovan. Azure **View** Maps View-parametern (kallas även "parameter för användarregion") är en landskod med två bokstäver ISO-3166 som visar rätt kartor för det landet/regionen som anger vilken uppsättning geopolitiskt omtvistat innehåll som returneras via Azure Maps-tjänster, inklusive kantlinjer och etiketter som visas på kartan. 

Se till att du ställer in parametern **Visa** som krävs för REST-API:erna och SDK:erna, som tjänsterna använder.
>  
>
>  **Api:er för vila:**
>  
>  Kontrollera att du har ställt in parametern Visa efter behov. Vyparametern anger vilken uppsättning geopolitiskt omtvistat innehåll som returneras via Azure Maps-tjänster. 
>
>  Påverkas Azure Maps REST Services:
>    
>    * Hämta kartpanel
>    * Hämta kartbild 
>    * Få Sök Fuzzy
>    * Hämta sök-SEI
>    * Hämta sök-POI-kategori
>    * Sök i närheten
>    * Hämta sökadress
>    * Få sökadressen strukturerad
>    * Få sökadressen omvänd
>    * Hämta sökadress omvänd Cross Street
>    * Post Sök inuti geometri
>    * Förhandsgranskning av postsökadress
>    * Förhandsgranskning av omvänd batch för bakåtstringsbeställning av inläggsadress
>    * Post Sök längs vägen
>    * Förhandsgranskning av luddiga batchar för inlägg
>
>    
>  **SDK:**
>
>  Kontrollera att du har ställt in parametern **Visa** efter behov och att du har den senaste versionen av Web SDK och Android SDK. Berörda SDK:er:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Som standard är vyparametern inställd **på Enhetlig**, även om du inte har definierat den i begäran. Bestäm användarnas plats. Ställ sedan in parametern **Visa** korrekt för den platsen. Alternativt kan du ställa in "Visa=Auto", som returnerar kartdata baserat på IP-adressen för begäran.  **Parametern Visa** i Azure Maps måste användas i enlighet med gällande lagar, inklusive de lagar om mappning av landet där kartor, avbildningar och annat data- och tredjepartsinnehåll som du har behörighet att komma åt via Azure Maps görs tillgängligt.


I följande tabell visas vyer som stöds.

| Visa         | Beskrivning                            |  Kartor | Search | JS Kartkontroll |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Förenade Arabemiraten (arabiska vyn)    |   ✓   |        |     ✓          |
| AR           | Argentina (argentinsk vy)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (arabiska View)                 |   ✓   |        |     ✓          |
| IN           | Indien (indisk vy)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arabiska Visa)                    |   ✓   |        |     ✓          |
| JO           | Jordanien (arabisk vy)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (arabiska vyn)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arabisk vy)                 |   ✓   |        |     ✓          |
| MA           | Marocko (marockansk vy)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (arabiska vyn)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (pakistansk vy)              |   ✓   |    ✓    |     ✓          |
| PS           | Palestinska myndigheten (arabiska vyn)    |   ✓   |        |     ✓          |
| QA           | Qatar (arabiska vyn)                   |   ✓   |        |     ✓          |
| SA           | Saudiarabien (arabiska vyn)            |   ✓   |        |     ✓          |
| SY           | Syrien (arabiska Visa)                   |   ✓   |        |     ✓          |
| Ni           | Jemen (arabiska Visa)                   |   ✓   |        |     ✓          |
| Automatisk         | Returnera kartdata baserat på IP-adressen för begäran.|   ✓   |    ✓   |     ✓          |
| Enhetlig      | Enhetlig vy (övriga)                  |   ✓   |   ✓     |     ✓          |
