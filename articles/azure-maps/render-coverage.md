---
title: Återge täckning i Azure Maps | Microsoft Docs
description: Lär dig mer om rendera-täckning i Azure Maps
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 03/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: ab05277c4541ae859f79b1108c4cf8a7beb29271
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-maps-render-coverage"></a>Azure Maps återge täckning

Azure Maps använder både raster paneler och vector paneler för att skapa mappningar. På den lägsta möjliga upplösningen i hela världen får plats på en enda sida vid sida. På den högsta upplösningen representerar en bricka 38 kvadratisk mätare. När du zooma in på en karta se du därför allt mer information om kontinenter, regioner, orter och enskilda streets. Mer information finns i [Zooma nivåer och panelen rutnätet](zoom-levels-and-tile-grid.md).

Maps har dock inte samma grad av information och noggrannhet för alla regioner. Följande tabeller innehåller information om vilken detaljnivå renderade som ingår i varje region.

## <a name="legend"></a>Förklaring

| Symbol | Betydelse |
|--------|---------|
| ✓ | Region representeras med detaljerad information.   |
| Ø | Region representeras med förenklad data. |


## <a name="africa"></a>Afrika 


| Region | Raster paneler enhetlig | Vektorn paneler enhetlig |
| ------ | :------------------: | :------------------: |
| Algeriet                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       |   | ✓ |
| Kamerun                         | ✓ | ✓ |
| Centralafrikanska republiken         |   | Ø |
| Tchad                             |   | Ø |
| Komorerna                          |   | Ø |
| Republiken Kongo                            | ✓ | ✓ |
| Demokratiska republiken Kongo | ✓ | ✓ |
| Côte d'Ivoire                    |   | Ø |
| Djibouti                         |   | Ø |
| Egypten                            | ✓ | ✓ |
| Ekvatoralguinea                |   | Ø |
| Eritrea                          |   | Ø |
| Etiopien                         |   | Ø |
| Gabon                            | ✓ | ✓ |
| Gambia                           |   | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           |   | Ø |
| Guinea Bissau                    |   | Ø |
| Kenya                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          |   | Ø |
| Libyen                            |   | Ø |
| Madagaskar                       |   | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauritanien                       | ✓ | ✓ |
| Mauritius                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marocko                          | ✓ | ✓ |
| Moçambique                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Saint Helena, Ascension och Tristan da Cunha |   | Ø |
| São Tomé och Príncipe            |   | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     |   | Ø |
| Somalia                          |   | Ø |
| Sydafrika                     | ✓ | ✓ |
| Sydsudan                      |   | Ø |
| Sudan                            |   | Ø |
| Swaziland                        | ✓ | ✓ |
| Förenade Republiken Tanzania      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisien                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambia                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Nord- och Sydamerika

| Region | Raster paneler enhetlig | Vektorn paneler enhetlig |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua och Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermuda                   |   | ✓ |
| Mångnationella staten Bolivia |   | ✓ |
| Bonaire, Sint Eustatius och Saba |   | ✓ |
| Brasilien                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Caymanöarna            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| $Clippertonön         |   | ✓ |
| Colombia                  | ✓ | ✓ |
| Costa Rica                |   | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| Dominikanska republiken        | ✓ | ✓ |
| Ecuador                   |   | ✓ |
| Falklandsöarna |   | ✓ |
| Franska Guyana             | ✓ | ✓ |
| Grönland (Danmark)                 |   | Ø |
| Grenada                   | ✓ | ✓ |
| Guadeloupe                | ✓ | ✓ |
| Guatemala                 |   | ✓ |
| Guyana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaica                   | ✓ | ✓ |
| Martinique                | ✓ | ✓ |
| Mexiko                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicaragua                 | ✓ | ✓ |
| Nordmarianerna  |   | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  |   | ✓ |
| Peru                      | ✓ | ✓ |
| Puerto Rico               | ✓ | ✓ |
| Quebec (Kanada)           |   | ✓ |
| Saint Barthélemy          | ✓ | ✓ |
| Saint Kitts och Nevis     | ✓ | ✓ |
| Saint Lucia               | ✓ | ✓ |
| Saint ek (franska)     | ✓ | ✓ |
| Saint Pierre och Miquelon |   | ✓ |
| Saint Vincent och Grenadinerna | ✓ | ✓ |
| Sint Maarten (holländska)      | ✓ | ✓ |
| Sydgeorgien och Sydsandwichöarna |   | ✓ |
| Surinam                  |   | ✓ |
| Trinidad och Tobago       | ✓ | ✓ |
| Turks- och Caicosöarna  | ✓ | ✓ |
| USA             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Jungfruöarna, Storbritannien   | ✓ | ✓ |
| Jungfruöarna, USA      | ✓ | ✓ |

## <a name="asia"></a>Asien 

| Region | Raster paneler enhetlig | Vektorn paneler enhetlig |
| ------ | :------------------: | :------------------: |
| Afghanistan               |   | Ø |
| Bahrain                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Bhutan                    |   | Ø |
| Brittiska territoriet i Indiska oceanen |   | Ø |
| Brunei                    | ✓ | ✓ |
| Kambodja                  |   | Ø |
| Kina                     |   | Ø |
| Kokosöarna   |   | Ø |
| Demokratiska folkrepubliken Korea |   | Ø |
| Dokdo och Takeshima       |   | Ø |
| Hongkong SAR                 | ✓ | ✓ |
| Indonesien                 | ✓ | ✓ |
| Iran                      |   | Ø |
| Irak                      | ✓ | ✓ |
| Israel                    |   | ✓ |
| Japan                     |   | Ø |
| Jordanien                    | ✓ | ✓ |
| Kazakstan                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Kirgizistan                |   | Ø |
| Demokratiska folkrepubliken Laos |   | Ø |
| Libanon                   | ✓ | ✓ |
| Macao SAR                     | ✓ | ✓ |
| Malaysia                  | ✓ | ✓ |
| Maldiverna                  |   | Ø |
| Mongoliet                  |   | Ø |
| Myanmar                   |   | Ø |
| Napal                     |   | Ø |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | Ø |
| Filippinerna               | ✓ | ✓ |
| Qatar                     | ✓ | ✓ |
| Sydkorea         | ✓ | Ø |
| Saudiarabien              | ✓ | ✓ |
| Senkaku-öarna/Diaoyutai-öarna i Oceanien och Västindien           |   | ✓ |
| Singapore                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| Arabrepubliken Syrien      |   | Ø |
| Taiwan SAR                    | ✓ | ✓ |
| Tadzjikistan                |   | Ø |
| Thailand                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turkmenistan              |   | Ø |
| Förenade Arabemiraten      | ✓ | ✓ |
| Förenta staternas mindre öar |   | Ø |
| Uzbekistan                |   | Ø |
| Vietnam                   | ✓ | ✓ |
| Jemen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceanien

| Region | Raster paneler enhetlig | Vektorn paneler enhetlig |
| ------ | :------------------: | :------------------: |
| Amerikanska Samoa            |   | ✓ |
| Australien                 | ✓ | ✓ |
| Cooköarna              |   | Ø |
| Fiji                      |   | Ø |
| Franska Polynesien          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Marshallöarna          |   | Ø |
| Mikronesien                |   | Ø |
| Nauru                     |   | Ø |
| Nya Kaledonien             |   | Ø |
| Nya Zeeland               | ✓ | ✓ |
| Niue                      |   | Ø |
| Norfolkön            |   | Ø |
| Palau                     |   | Ø |
| Papua Nya Guinea          |   | Ø |
| Pitcairn                  |   | Ø |
| Samoaöarna                     |   | Ø |
| Salomonöarna           |   | Ø|
| Tokelauöarna                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis och Futuna         |   | Ø |


## <a name="europe"></a>Europa

| Region | Raster paneler enhetlig | Vektorn paneler enhetlig |
| ------ | :------------------: | :------------------: |
| Albanien                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armenien                   |   | Ø |
| Österrike                   | ✓ | ✓ |
| Azerbajdzjan                |   | Ø |
| Vitryssland                   | Ø | ✓ |
| Belgien                   | ✓ | ✓ |
| Bosnien och Hercegovina        | ✓ | ✓ |
| Bulgarien                  | ✓ | ✓ |
| Kroatien                   | ✓ | ✓ |
| Cypern                    | ✓ | ✓ |
| Tjeckien            | ✓ | ✓ |
| Danmark                   | ✓ | ✓ |
| Estland                   | ✓ | ✓ |
| Färöarna             |   | Ø |
| Finland                   | ✓ | ✓ |
| Frankrike                    | ✓ | ✓ |
| Georgien                   |   | Ø |
| Tyskland                   | ✓ |✓ |
| Gibraltar                 | ✓ |   |
| Grekland                    | ✓ | ✓ |
| Guernsey                  |   | ✓ |
| Ungern                   | ✓ | ✓ |
| Island                   | ✓ | ✓ |
| Irland (Republiken)     | ✓ | ✓ |
| Isle of Man               |   | ✓ |
| Italien                     | ✓ | ✓ |
| Jan Mayen                 |   | ✓ |
| Jersey                    |   | ✓ |
| Lettland                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Litauen                 | ✓ | ✓ |
| Luxemburg                | ✓ | ✓ |
| Republiken Makedonien                 | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldavien                   | ✓ | ✓ |
| Monaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Nederländerna               | ✓ | ✓ |
| Norge                    | ✓ | ✓ |
| Polen                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Rumänien                   | ✓ | ✓ |
| Ryska federationen        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Serbien                    | ✓ | ✓ |
| Slovakien                  | ✓ | ✓ |
| Slovenien                  | ✓ | ✓ |
| Södra Kurils           |   | ✓ |
| Spanien                     | ✓ | ✓ |
| Svalbard                  |   | ✓ |
| Sverige                    | ✓ |   |
| Schweiz               | ✓ | ✓ |
| Turkiet                    | ✓ | ✓ |
| Ukraina                   | ✓ | ✓ |
| Storbritannien            | ✓ | ✓ |
| Vatikanstaten              | ✓ | ✓ |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps återgivning [Zooma nivåer och panelen rutnätet](zoom-levels-and-tile-grid.md).

Lär dig mer om den [täckningsområden för Maps routing service](routing-coverage.md). 