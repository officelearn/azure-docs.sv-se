---
title: Lokaliseringsstöd i Azure Maps | Microsoft Docs
description: Lär dig mer om språk som stöds för tjänster i Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a9446301cc4bb46c989223ad020c7a8e8b353ad3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446193"
---
# <a name="localization-support-in-azure-maps"></a>Lokaliseringsstöd i Azure Maps

Azure Maps har stöd för olika språk och vyer som är baserat på land/region. Den här artikeln innehåller de språk som stöds och vyer för att implementera Azure Maps.


## <a name="azure-maps-supported-languages"></a>Språk som stöds av Azure Maps

Azure Maps har lokaliserats i olika språk tjänster. Följande tabell innehåller koder för språk som stöds för varje tjänst.  
  

| id         | Namn                   |  Kartor | Search | Routning | Trafik-incidenter | JS kartkontroll | Tidszon |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Arabiska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | Baskiska                 |       |    ✓   |         |                   |                |     ✓     |
| BG-BG      | Bulgariska              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| CA-ES      | Katalanska                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Förenklad kinesiska   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Traditionell kinesiska  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| HR-HR      | Kroatiska               |       |    ✓   |         |                   |                |     ✓     |
| CS-CZ      | Tjeckiska                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Danska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NL-NL      | Nederländska                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Nederländska (Belgien)        |       |    ✓   |         |                   |                |     ✓     |
| SV-Australien      | Engelska (Australien)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en NZ      | Engelska (Nya Zeeland)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Engelska (Storbritannien) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Engelska (USA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estniska               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Finska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Franska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Franska (Kanada)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | Galiciska               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Tyska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el GR      | Grekiska                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| han IL      | Hebreiska                 |       |    ✓   |         |         ✓         |                |     ✓     |
| Hej Indien      | Hindi                  |       |        |         |                   |                |     ✓     |
| hu-HU      | Ungerska              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ID-ID      | Indonesiska             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| IT-IT      | Italienska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japanska               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Kazakiska                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Koreanska                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Latinamerika spanska |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | Lettiska                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Litauiska             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| MS-Mina      | Malajiska (latinsk)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| NB-NO      | Norska, Bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutrala grunden sanningen - officiella språk för alla regioner i lokala skript om de är tillgängliga |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Neutral Ground Truth - Latin exonyms. Latin-skript som ska användas om det är tillgängligt |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polska                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Portugisiska (Brasilien)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| PT-PT      | Portugisiska (Portugal)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| RO-RO      | Rumänska               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Ryska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Serbiska (kyrillisk)     |       |    Serbiska (kyrillisk) (sr-RS)   |         |                   |                |     ✓     |
| SR-Latn-RS | Serbiska (latinsk)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Slovakiska              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | Slovenska              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Spanska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Spanska (Mexiko)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | Svenska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| TH-TH      | Thai                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| TR-TR      | Turkiska                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | Ukrainska               |       |    ✓   |         |                   |                |     ✓     |
| Vi VN      | Vietnamesiska             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Azure Maps stöds vyer

> [!Note]
> Vi publicerar Azure Maps i följande länder/regioner den 1 augusti 2019:
>  * Argentina
>  * Indien
>  * Marocko
>  * Pakistan
>
> Efter den 1 augusti 2019 **visa** parameterinställningen definierar returnerade kartinnehållet för nya regioner/länder som anges ovan. Vi uppmanar dig att se till att du har konfigurerat parametern vy som krävs för REST API: er och SDK: er som använder dina tjänster.
>  
>
>  **REST API: er:**
>  
>  Se till att du har ställt in parametern Visa efter behov. Visa parametern anger vilken uppsättning geopolitiskt omtvistad innehåll returneras via Azure Maps-tjänsterna. 
>
>  Berörda Azure Maps REST-tjänster:
>    
>    * Hämta Kartrutan
>    * Hämta Kartbilden 
>    * Hämta Search Fuzzy
>    * Hämta Search POI
>    * Hämta Sök POI kategorin
>    * Hämta sökning i närheten
>    * Hämta Search-adressen
>    * Hämta Search adressen strukturerad
>    * Hämta Search adressen omvänd
>    * Hämta Search omvänd mellan gata
>    * Sök efter inuti geometri
>    * Förhandsversionen av Batch post Search-adress
>    * Publicera förhandsgranskning adress omvänd Batch
>    * Sök efter längs vägen
>    * Förhandsversionen av inlägget Search Fuzzy Batch
>
>    
>  **SDK: er:**
>
>  Kontrollera att du har ställt in parametern Visa efter behov och du har den senaste versionen av Web SDK- och Android SDK. SDK: er som påverkas:
>
>    * Azure Maps webb-SDK
>    * Azure Maps Android SDK


Azure Maps **visa** parametern (kallas även ”användaren region parametern”) är en två bokstäver som är ISO-3166 landskod som visar rätt mappningar för det land/region som anger vilken uppsättning geopolitiskt omtvistad innehåll returnerade via Azure Maps-tjänster, inklusive kantlinjer och etiketter som visas på kartan. 

Som standard visa parametern anges till **enhetliga**, även om du inte har definierat den i begäran. Det är ditt ansvar att avgöra var dina användare och sedan ange parametern Visa korrekt för den platsen. Du kan också har du möjlighet att ange ”visa = Auto', vilket returnerar kartdata baserat på IP-adressen för begäran.  Parametern vyn i Azure Maps måste användas i enlighet med tillämpliga lagar, inklusive de som angående mappningen av det land där kartor, bilder och andra data och tredje parts innehåll som du har behörighet att komma åt via Azure Maps är tillgänglig.


Följande tabell innehåller vyerna som stöds.

| Visa         | Beskrivning                            |  Kartor | Search | JS Kartkontroll |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Förenade Arabemiraten (arabiska View)    |   ✓   |        |     ✓          |
| AR           | Argentina (argentinska View)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (arabiska View)                 |   ✓   |        |     ✓          |
| IN           | Indien (indiska View)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arabiska View)                    |   ✓   |        |     ✓          |
| JO           | Jordanien (arabiska View)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (arabiska View)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arabiska View)                 |   ✓   |        |     ✓          |
| MA           | Marocko (Marockansk View)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (arabiska View)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (Pakistansk View)              |   ✓   |    ✓    |     ✓          |
| PS           | Palestinska myndigheten (arabiska View)    |   ✓   |        |     ✓          |
| QA           | Qatar (arabiska View)                   |   ✓   |        |     ✓          |
| SA           | Saudiarabien (arabiska View)            |   ✓   |        |     ✓          |
| SY           | Syrien (arabiska View)                   |   ✓   |        |     ✓          |
| ALL           | Jemen (arabiska View)                   |   ✓   |        |     ✓          |
| Automatisk         | Returnera kartdata baserat på IP-adressen för begäran.|   ✓   |    ✓   |     ✓          |
| Enhetligt      | Enhetlig vy (alternativ)                  |   ✓   |   ✓     |     ✓          |
