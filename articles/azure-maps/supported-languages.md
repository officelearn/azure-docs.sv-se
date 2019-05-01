---
title: Lokaliseringsstöd i Azure Maps | Microsoft Docs
description: Lär dig mer om språk som stöds för tjänster i Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1928185521419006a487a933e2ecba79894a09d3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686789"
---
# <a name="localization-support-in-azure-maps"></a>Lokaliseringsstöd i Azure Maps

Azure Maps har stöd för olika språk och vyer som är baserat på land/region. Den här artikeln innehåller de språk som stöds och vyer för att implementera Azure Maps.


## <a name="azure-maps-supported-languages"></a>Språk som stöds av Azure Maps

Azure Maps har lokaliserats i olika språk tjänster. Följande tabell innehåller koder för språk som stöds för varje tjänst.  
  

| ID         | Namn                   |  Kartor | Search | Routning | Trafik-incidenter | JS kartkontroll | Tidszon |
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

Azure Maps visa parametern (kallas även ”användaren region parametern”) är en 2-bokstav ISO 3166 landskod som visar rätt maps för det land/region som anger vilken uppsättning geopolitiskt omtvistad kantlinjer och etiketter visas på kartan.  Som standard visa parametern anges till **”Unified”**.  Länder/regioner som inte finns på listan som standard till vyn ”enhetliga”. Det är ditt ansvar att avgöra var dina användare och sedan ange parametern Visa korrekt för den platsen. Parametern vyn i Azure Maps måste användas i enlighet med tillämpliga lagar, inklusive de som angående mappningen av det land där kartor, bilder och andra data och tredje parts innehåll som du har behörighet att komma åt via Azure Maps är tillgänglig.

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
| Enhetlig      | Enhetlig vy (alternativ)                  |   ✓   |   ✓     |     ✓          |
