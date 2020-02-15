---
title: Lokaliserings stöd | Microsoft Azure Maps
description: I den här artikeln får du lära dig om språk som stöds för tjänsterna i Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5de04b4e37e79a47f89bb235aad2b0f9cab69e6a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208156"
---
# <a name="localization-support-in-azure-maps"></a>Lokaliserings stöd i Azure Maps

Azure Maps stöder olika språk och vyer baserat på land/region. Den här artikeln innehåller språk och vyer som stöds för att hjälpa dig att hjälpa dig med din Azure Maps implementering.


## <a name="azure-maps-supported-languages"></a>Azure Maps språk som stöds

Azure Maps har lokaliserats på olika typer av tjänster för sina tjänster. Följande tabell innehåller de språk koder som stöds för varje tjänst.  
  

| ID         | Namn                   |  Maps | Söka | Routning | Väder | Trafik incidenter | JS-kart kontroll |
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
| sv-SE      | Engelska (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
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
> Den 1 augusti 2019 släpptes Azure Maps i följande länder/regioner:
>  * Argentina
>  * Indien
>  * Marocko
>  * Pakistan
>
> Efter den 1 augusti 2019 definierar **visnings** parametern det returnerade kart innehållet för de nya regionerna/länderna som anges ovan. Azure Maps **View** -parametern (kallas även för "användar region parameter") är en ISO-3166-landskod som visar rätt kartor för landet/regionen och som anger vilken uppsättning geografi innehåll som anges av polypolitiskt som returneras via Azure Maps Services, inklusive kant linjer och etiketter som visas på kartan. 

Se till att ställa in parametern **Visa** som krävs för REST-API: er och SDK: er som dina tjänster använder.
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
>  Kontrol lera att du har ställt in parametern **Visa** efter behov och att du har den senaste versionen av Web SDK och Android SDK. Berörda SDK: er:
>
>    * Azure Maps webb-SDK
>    * Azure Maps Android SDK

Som standard har parametern View angetts till **Unified**, även om du inte har definierat den i begäran. Bestäm platsen för dina användare. Ange sedan parametern **View** korrekt för den platsen. Du kan också ange "Visa = Auto", som returnerar kartdata baserat på IP-adressen för begäran.  Parametern **View** i Azure Maps måste användas i enlighet med gällande lagar, inklusive de lagar som gäller mappning av det land där kartor, bilder och andra data och innehåll från tredje part som du har behörighet att komma åt via Azure Maps görs tillgängligt.


Följande tabell innehåller vyer som stöds.

| Visa         | Beskrivning                            |  Maps | Söka | JS-Kartkontroll |
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
| Gemensam      | Enhetlig vy (andra)                  |   ✓   |   ✓     |     ✓          |
