---
title: Rendera täckning i Azure Maps | Microsoft Docs
description: Läs mer om åter givnings täckning i Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5affc9952cfe13ebfeede126051f2236499d1343
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934298"
---
# <a name="azure-maps-render-coverage"></a>Azure Maps återge täckning

Azure Maps använder både raster paneler och vektor paneler för att skapa kartor. Med den lägsta upplösningen får hela världen en enda panel. Vid den högsta upplösningen representerar en enda panel 38 kvadratmeter. När du zoomar in på en karta kan du därför se mer information om kontinenter, regioner, städer och enskilda gator. Mer information finns i [zoomnings nivåer och panel rutnät](zoom-levels-and-tile-grid.md).

Maps har dock inte samma nivå av information och exakthet för alla regioner. Följande tabeller innehåller information om vilken nivå av återgiven information som du kan förväntar dig från varje region.

## <a name="legend"></a>Mönstret

| Hakparentes | Betydelse |
|--------|---------|
| ✓ | Regionen visas med detaljerade data.   |
| Ø | Regionen representeras med förenklade data. |


## <a name="africa"></a>Afrika 


| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Algeriet                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botswana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Kamerun                         | ✓ | ✓ |
| Centralafrikanska republiken         | ✓ | Ø |
| Tchad                             | ✓ | Ø |
| Komorerna                          | ✓ | Ø |
| Demokratiska republiken Kongo | ✓ | ✓ |
| Côte d'Ivoire                    | ✓ | Ø |
| Djibouti                         | ✓ | Ø |
| Egypten                            | ✓ | ✓ |
| Ekvatorialguinea                | ✓ | Ø |
| Eritrea                          | ✓ | Ø |
| Etiopien                         | ✓ | Ø |
| Gabon                            | ✓ | ✓ |
| Gambia                           | ✓ | Ø |
| Ghana                            | ✓ | ✓ |
| Guinea                           | ✓ | Ø |
| Guinea-Bissau                    | ✓ | Ø |
| Kenya                            | ✓ | ✓ |
| Lesotho                          | ✓ | ✓ |
| Liberia                          | ✓ | Ø |
| Libyen                            | ✓ | Ø |
| Madagaskar                       | ✓ | Ø |
| Malawi                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauretanien                       | ✓ | ✓ |
| Mauritius                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marocko                          | ✓ | ✓ |
| Moçambique                       | ✓ | ✓ |
| Namibia                          | ✓ | ✓ |
| Niger                            | ✓ | ✓ |
| Nigeria                          | ✓ | ✓ |
| Réunion                          | ✓ | ✓ |
| Rwanda                           | ✓ | ✓ |
| Saint Helena, Ascension och Tristan da Cunha | ✓ | Ø |
| São Tomé och Príncipe            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Sierra Leone                     | ✓ | ✓ |
| Somalia                          | ✓ | ✓ |
| Sydafrika                     | ✓ | ✓ |
| Sydsudan                      | ✓ | ✓ |
| Sudan                            | ✓ | ✓ |
| Swaziland                        | ✓ | ✓ |
| Förenade republiken Tanzania      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunisien                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zambia                           | ✓ | ✓ |
| Zimbabwe                         | ✓ | ✓ |

## <a name="americas"></a>Nord- och Sydamerika

| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antigua och Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermuda                   | ✓ | ✓ |
| Mångnationella staten staten Bolivia | ✓ | ✓ |
| Bonaire, Sint Eustatius och Saba | ✓ | ✓ |
| Brasilien                    | ✓ | ✓ |
| Kanada                    | ✓ | ✓ |
| Caymanöarna            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Colombia                  | ✓ | ✓ |
| Costa Rica                | ✓ | ✓ |
| Kuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| Dominikanska republiken        | ✓ | ✓ |
| Ecuador                   | ✓ | ✓ |
| Falk lands öarna (Malvinas) | ✓ | ✓ |
| Franska Guyana             | ✓ | ✓ |
| Grönland                 | ✓ | Ø |
| Grenada                   | ✓ | ✓ |
| Guadeloupe                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guyana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaica                   | ✓ | ✓ |
| Martinique                | ✓ | ✓ |
| Mexiko                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicaragua                 | ✓ | ✓ |
| Nordmarianerna  | ✓ | ✓ |
| Panama                    | ✓ | ✓ | 
| Paraguay                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Puerto Rico               | ✓ | ✓ |
| Quebec (Kanada)           | ✓ | ✓ |
| Sankt Barthélemy          | ✓ | ✓ |
| Saint Kitts och Nevis     | ✓ | ✓ |
| Saint Lucia               | ✓ | ✓ |
| Saint Martin (franska)     | ✓ | ✓ |
| Saint Pierre och Miquelon | ✓ | ✓ |
| Saint Vincent och Grenadinerna | ✓ | ✓ |
| Sint Maarten (nederländska)      | ✓ | ✓ |
| Sydgeorgien och Sydsandwichöarna | ✓ | ✓ |
| Surinam                  | ✓ | ✓ |
| Trinidad och Tobago       | ✓ | ✓ |
| Turks- och Caicosöarna  | ✓ | ✓ |
| USA             | ✓ | ✓ |
| Uruguay                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Jungfruöarna, Storbritannien   | ✓ | ✓ |
| Jungfruöarna, USA      | ✓ | ✓ |

## <a name="asia"></a>Asien 

| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Afghanistan               |   | Ø |
| Bahrain                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Bhutan                    |   | Ø |
| Brittiska territoriet i Indiska oceanen |   | Ø |
| Darussalam                    | ✓ | ✓ |
| Kambodja                  |   | Ø |
| Kina                     |   | Ø |
| Kokos öarna   |   | Ø |
| Demokratiska folkrepubliken Korea |   | Ø |
| Hong Kong SAR             | ✓ | ✓ |
| Indien                     | Ø | ✓ | 
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
| Macao SAR                 | ✓ | ✓ |
| Malaysia                  | ✓ | ✓ |
| Maldiverna                  |   | Ø |
| Mongoliet                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Oman                      | ✓ | ✓ |
| Pakistan                  |   | Ø |
| Filippinerna               | ✓ | ✓ |
| Qatar                     | ✓ | ✓ |
| Republiken Korea         | ✓ | Ø |
| Saudiarabien              | ✓ | ✓ |
| Senkaku öarna           |   | ✓ |
| Singapore                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| Arabrepubliken Syrien      |   | Ø |
| Taiwan                    | ✓ | ✓ |
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

| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Amerikanska Samoa            |   | ✓ |
| Australien                 | ✓ | ✓ |
| Cooköarna              |   | Ø |
| Fiji                      |   | Ø |
| Franska Polynesien          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Marshall öarna          |   | Ø |
| Mikronesien                |   | Ø |
| Nauru                     |   | Ø |
| Nya Kaledonien             |   | Ø |
| Nya Zeeland               | ✓ | ✓ |
| Niue                      |   | Ø |
| Norfolkön            |   | Ø |
| Palau                     |   | Ø |
| Papua Nya Guinea          |   | Ø |
| Pitcairn                  |   | Ø |
| Samoa                     |   | Ø |
| Salomonöarna           |   | Ø|
| Tokelauöarna                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis och Futuna         |   | Ø |


## <a name="europe"></a>Europa

| Land/region | Raster paneler Unified | Vektor paneler Unified |
| ------ | :------------------: | :------------------: |
| Albanien                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armenien                   | ✓ | Ø |
| Österrike                   | ✓ | ✓ |
| Azerbajdzjan                | ✓ | Ø |
| Vitryssland                   | Ø | ✓ |
| Belgien                   | ✓ | ✓ |
| Bosnien-Hercegovina        | ✓ | ✓ |
| Bulgarien                  | ✓ | ✓ |
| Kroatien                   | ✓ | ✓ |
| Cypern                    | ✓ | ✓ |
| Tjeckien            | ✓ | ✓ |
| Danmark                   | ✓ | ✓ |
| Estland                   | ✓ | ✓ |
| Färöarna             | ✓ | Ø |
| Finland                   | ✓ | ✓ |
| Frankrike                    | ✓ | ✓ |
| Georgien                   | ✓ | Ø |
| Tyskland                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Grekland                    | ✓ | ✓ |
| Guernseypund                  | ✓ | ✓ |
| Ungern                   | ✓ | ✓ |
| Island                   | ✓ | ✓ |
| Irland                   | ✓ | ✓ |
| Isle of Man               | ✓ | ✓ |
| Italien                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Lettland                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Litauen                 | ✓ | ✓ |
| Luxemburg                | ✓ | ✓ |
| Nord Makedonien           | ✓ | ✓ |
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
| Södra Kurils           | ✓ | ✓ |
| Spanien                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Sverige                    | ✓ | ✓ |
| Schweiz               | ✓ | ✓ |
| Turkiet                    | ✓ | ✓ |
| Ukraina                   | ✓ | ✓ |
| Storbritannien            | ✓ | ✓ |
| Vatikanstaten              | ✓ | ✓ |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Maps rendering finns i [zoomnings nivåer och panel rutnät](zoom-levels-and-tile-grid.md).

Lär dig mer om [täcknings områdena för Maps-routningstjänsten](routing-coverage.md). 