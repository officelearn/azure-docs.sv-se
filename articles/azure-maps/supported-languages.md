---
title: Lokaliserings stöd i Azure Maps | Microsoft Docs
description: Lär dig mer om språk som stöds för tjänsterna i Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 64197cc85822997926a8011af8cb3b981fa9064d
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286424"
---
# <a name="localization-support-in-azure-maps"></a>Lokaliserings stöd i Azure Maps

Azure Maps stöder olika språk och vyer baserat på land/region. Den här artikeln innehåller språk och vyer som stöds för att hjälpa dig att hjälpa dig med din Azure Maps implementering.


## <a name="azure-maps-supported-languages"></a>Azure Maps språk som stöds

Azure Maps har lokaliserats på flera olika språk i tjänsterna. Följande tabell innehåller de språk koder som stöds för varje tjänst.  
  

| ID         | Namn                   |  Maps | Search | Routning | Väder | Trafik incidenter | JS-kart kontroll |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabiska                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| BN – BD      | Bangla (Bangladesh)    |       |       |         |     ✓    |                   |                |
| BN – IN      | Bangla (Indien)         |       |       |         |     ✓    |                   |                |
| BS-BA      | Bosniska                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Baskiska                 |       |    ✓   |         |         |                   |                |
| BG-BG      | Bulgariska              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| CA-ES      | Katalanska                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Förenklad kinesiska   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Kinesiska (Hongkong SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Kinesiska (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| HR-HR      | Kroatiska               |       |    ✓   |         |    ✓      |                   |                |
| CS-CZ      | Tjeckiska                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Danska                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Nederländska (Belgien)        |       |    ✓   |         |      ✓    |                   |                |
| NL-NL      | Nederländska (Nederländerna)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| SV-Australien      | Engelska (Australien)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en NZ      | Engelska (Nya Zeeland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Engelska (Storbritannien) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Engelska (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estniska               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filippinska               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finska                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Franska                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Franska (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galiciska               |       |    ✓   |         |         |                   |                |
| de-DE      | Tyska                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el GR      | Grekiska                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| Gu – IN      | Gujarati                |       |       |         |     ✓    |                   |                |
| han IL      | Hebreiska                 |       |    ✓   |         |     ✓    |         ✓         |                |
| Hej Indien      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Ungerska              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| är-är      | Isländska              |       |       |         |     ✓    |                   |                |
| ID-ID      | Indonesiska             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| IT-IT      | Italienska                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japanska               |       |        |         |     ✓    |                   |                |
| KN-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazakiska                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreanska                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Latinska spanska |       |    ✓   |         |         |                   |                |
| lv-LV      | Lettiska                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litauiska             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK – MK      | Makedonska             |       |       |         |     ✓    |                   |                |
| MS-Mina      | Malajiska (latinsk)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr-IN      | Marathi                 |       |       |         |     ✓    |                   |                |
| NB-NO      | Norska, bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutrala mark sanningen-officiella språk för alla regioner i lokala skript om det är tillgängligt |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral mark sanningen – Latin exonyms. Latinska skript kommer att användas om det är tillgängligt |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polska                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugisiska (Brasilien)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| PT-PT      | Portugisiska (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | Punjabi                 |       |       |         |     ✓    |                   |                |
| RO-RO      | Rumänska               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Ryska                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbiska (kyrillisk)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| SR-latn-RS | Serbiska (latinsk)        |       |       |         |     SR-latn    |                   |                |
| sk-SK      | Slovakiska             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Slovenska              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spanska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Spanska (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| SV-SE      | Svenska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta IN      | Tamil (Indien)                 |       |       |         |     ✓    |                   |                |
| te Indien      | Telugu (Indien)                 |       |       |         |     ✓    |                   |                |
| TH-TH      | Thai                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| TR-TR      | Turkiska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrainska               |       |    ✓   |         |     ✓    |                   |                |
| din-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbekiska                 |       |       |         |     ✓    |                   |                |
| Vi VN      | Vietnamesiska             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps vyer som stöds

> [!Note]
> Azure Maps släpptes i följande länder/regioner den 1 augusti 2019:
>  * Argentina
>  * Indien
>  * Marocko
>  * Pakistan
>
> Efter den 1 augusti 2019 definierar **parameter inställningen** det returnerade kart innehållet för de nya regionerna/länderna som anges ovan. Vi rekommenderar att du ser till att du har ställt in parametern Visa som krävs för de REST-API: er och SDK: er som dina tjänster använder.
>  
>
>  **REST-API: er:**
>  
>  Se till att du har ställt in parametern Visa efter behov. Visa parameter anger vilken uppsättning av ett interpolitiskt innehåll som ska returneras via Azure Maps Services. 
>
>  Berörda Azure Maps REST-tjänster:
>    
>    * Panelen Hämta karta
>    * Hämta kart bild 
>    * Hämta en sökning i fuzzy
>    * Hämta Sök-POI
>    * Hämta Sök POI kategori
>    * Hämta Sök i närheten
>    * Hämta Sök adress
>    * Hämta Sök adressen strukturerad
>    * Hämta omvänd söknings adress
>    * Hämta Sök adress omvänt kors gatan
>    * Publicera sökning i geometri
>    * Publicera söknings adress batch för hands version
>    * Publicera söknings adress, omvänd batch-förhandsgranskning
>    * Publicera sökning längs väg
>    * Publicera sökning i fuzzy-kommando för hands version
>
>    
>  **Er**
>
>  Kontrol lera att du har ställt in parametern Visa efter behov och att du har den senaste versionen av Web SDK och Android SDK. Berörda SDK: er:
>
>    * Azure Maps webb-SDK
>    * Azure Maps Android SDK


Azure Maps **View** -parametern (kallas även för "användar region parameter") är en ISO-3166-landskod som visar rätt kartor för landet/regionen och som anger vilken uppsättning geografi innehåll som anges av polypolitiskt som returneras via Azure Maps Services, inklusive kant linjer och etiketter som visas på kartan. 

Som standard har parametern View värdet **Unified**, även om du inte har definierat den i begäran. Det är ditt ansvar att bestämma platsen för dina användare och sedan ange parametern View korrekt för den platsen. Alternativt kan du välja "Visa = Auto", som returnerar kartdata baserat på IP-adressen för begäran.  Parametern View i Azure Maps måste användas i enlighet med tillämplig lagstiftning, inklusive de som gäller kart läggning av det land där kartor, bilder och andra data och innehåll från tredje part som du har behörighet att komma åt via Azure Maps görs tillgängligt.


Följande tabell innehåller vyer som stöds.

| Visa         | Beskrivning                            |  Maps | Search | JS-Kartkontroll |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Förenade Arabemiraten (arabisk vy)    |   ✓   |        |     ✓          |
| AR           | Argentina (argentinsk vy)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (arabisk vy)                 |   ✓   |        |     ✓          |
| IN           | Indien (indiska vyn)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arabisk vy)                    |   ✓   |        |     ✓          |
| JO           | Jordanien (arabisk vy)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (arabisk vy)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arabisk vy)                 |   ✓   |        |     ✓          |
| MA           | Marocko (marockansk View)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (arabisk vy)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (pakistansk vy)              |   ✓   |    ✓    |     ✓          |
| PS           | Palestinska myndigheten (arabisk vy)    |   ✓   |        |     ✓          |
| QA           | Qatar (arabisk vy)                   |   ✓   |        |     ✓          |
| SA           | Saudiarabien (arabisk vy)            |   ✓   |        |     ✓          |
| SY           | Syrien (arabisk vy)                   |   ✓   |        |     ✓          |
| CHANSEN           | Jemen (arabisk vy)                   |   ✓   |        |     ✓          |
| disk         | Returnera mappnings data baserat på IP-adressen för begäran.|   ✓   |    ✓   |     ✓          |
| Enhetligt      | Enhetlig vy (andra)                  |   ✓   |   ✓     |     ✓          |
