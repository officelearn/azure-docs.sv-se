---
title: Lokaliserings stöd i Azure Maps | Microsoft Docs
description: Lär dig mer om språk som stöds för tjänsterna i Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3382cffdc41685f8329a640aaf6c6c526375a83
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299835"
---
# <a name="localization-support-in-azure-maps"></a>Lokaliserings stöd i Azure Maps

Azure Maps stöder olika språk och vyer baserat på land/region. Den här artikeln innehåller språk och vyer som stöds för att hjälpa dig att hjälpa dig med din Azure Maps implementering.


## <a name="azure-maps-supported-languages"></a>Azure Maps språk som stöds

Azure Maps har lokaliserats på flera olika språk i tjänsterna. Följande tabell innehåller de språk koder som stöds för varje tjänst.  
  

| ID         | Namn                   |  Maps | Sök | Routning | Trafik incidenter | JS-kart kontroll | Tidszon |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| AF – ZA      | Afrikaans              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Arabiska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EU-ES      | Baskiska                 |       |    ✓   |         |                   |                |     ✓     |
| BG-BG      | Bulgariska              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ca-ES      | Katalanska                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Kinesiska (förenklad)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Kinesiska (traditionell)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| HR – HR      | Kroatiska               |       |    ✓   |         |                   |                |     ✓     |
| CS-CZ      | Tjeckiska                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Danska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-NL      | Nederländska                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NL-vara      | Nederländska (Belgien)        |       |    ✓   |         |                   |                |     ✓     |
| en – AU      | Engelska (Australien)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-NZ      | Engelska (Nya Zeeland)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Engelska (Storbritannien) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Engelska (USA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estniska               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Finska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Franska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Franska (kanadensisk)      |       |    ✓   |         |                   |                |     ✓     |
| GL-ES      | Galiciska               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Tyska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| El – GR      | Grekiska                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| He-IL      | Hebreiska                 |       |    ✓   |         |         ✓         |                |     ✓     |
| Hi-IN      | Hindi                  |       |        |         |                   |                |     ✓     |
| hu – HU      | Ungerska              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ID-ID      | Indonesiska             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| det – IT      | Italienska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japanska               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Kazakiska                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Koreanska                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| ES – 419     | Latinska spanska |       |    ✓   |         |                   |                |     ✓     |
| LV-LV      | Lettiska                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Litauiska             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| MS-MY      | Malajiska (latinsk)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| NB-nej      | Norska, bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutrala mark sanningen-officiella språk för alla regioner i lokala skript om det är tillgängligt |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Neutral mark sanningen – Latin exonyms. Latinska skript kommer att användas om det är tillgängligt |   ✓     |        |         |                   |        ✓         |          |
| PL-PL      | Polska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Portugisiska (Brasilien)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Portugisiska (Portugal)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| RO-RO      | Rumänska               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Ryska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-cyrl-RS | Serbiska (kyrillisk)     |       |    Serbiska (kyrillisk) (SR-RS)   |         |                   |                |     ✓     |
| SR-latn-RS | Serbiska (latinsk)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Slovakiska              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| SL-SL      | Slovenska              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Spanska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ES – MX      | Spanska (Mexiko)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sa-SE     | Svenska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Th-TH      | Thai                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| TR-TR      | Turkiska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Storbritannien – UA      | Ukrainska               |       |    ✓   |         |                   |                |     ✓     |
| Vi – VN      | Vietnamesiska             |       |    ✓   |         |                   |                |     ✓     |


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


Azure Maps **View** -parameter (kallas även för "användar region parameter") är en ISO-3166-landskod som visar rätt kartor för landet/regionen och som anger vilken uppsättning geografi innehåll som anges av polypolitisk information som returneras via Azure Maps tjänster, inklusive kant linjer och etiketter som visas på kartan. 

Som standard har parametern View värdet **Unified**, även om du inte har definierat den i begäran. Det är ditt ansvar att bestämma platsen för dina användare och sedan ange parametern View korrekt för den platsen. Alternativt kan du välja "Visa = Auto", som returnerar kartdata baserat på IP-adressen för begäran.  Parametern View i Azure Maps måste användas i enlighet med tillämplig lagstiftning, inklusive de som gäller kart läggning av det land där kartor, bilder och andra data och innehåll från tredje part som du har behörighet att komma åt via Azure Maps görs tillgängligt.


Följande tabell innehåller vyer som stöds.

| Visa         | Beskrivning                            |  Maps | Sök | JS-Kartkontroll |
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
| Disk         | Returnera mappnings data baserat på IP-adressen för begäran.|   ✓   |    ✓   |     ✓          |
| Enhetligt      | Enhetlig vy (andra)                  |   ✓   |   ✓     |     ✓          |
