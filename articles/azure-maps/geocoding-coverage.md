---
title: Omfattning för att avkoda i Azure Maps | Microsoft Docs
description: Lär dig mer om att avkoda täckning i Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 999e545d49b11eef84d1a03401eb28cde36e82af
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991374"
---
# <a name="azure-maps-geocoding-coverage"></a>Azure Maps för att avkoda täckning

När du söker efter en plats med Azure Maps, tar Sök tjänsten med dina Sök villkor och returnerar latitud-och longitud-koordinaterna, en process som kallas för kodning. Maps har dock inte samma nivå av information och noggrannhet för alla regioner och länder. Använd den här artikeln för att avgöra vilken typ av platser du kan söka efter på ett tillförlitligt sätt i varje region. 

Möjligheten att koda i ett land/en region är beroende av vägtrafikens data täckning och den landsspecifika precisionen i den landsspecifika tjänsten. Följande kategoriseringar används specificerar nivån för stöd för landsspecifika i varje land/region.
* **Adress punkter** – adresser data kan matchas mot en latitud-eller longitud-koordinat inom adress skiftena (egenskaps gränser). Kallas ibland "Rooftop". Detta är den högsta möjliga noggrannhets nivån för adresser. 
* **Hus siffror** – adresser interpoleras till en Latitude/longitud-koordinat på gatan.
* **Gata** – adresser matchas mot latitud/longitud-koordinaten för gatan som innehåller adressen. Hus numret kan inte bearbetas.
* **Ort-nivå** – Orts plats namn stöds.

## <a name="americas"></a>Nord- och Sydamerika

| Land/region                                       | Adress punkter | Hus nummer | Gatu nivå | Stads nivå | Intressanta punkter |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarktis                                          |                 |                |              |      ✓     |          ✓         |
| Antigua och Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius och Saba                   |                 |                |              |      ✓     |          ✓         |
| Brasilien                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kanada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Caymanöarna                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colombia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Kuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominikanskaa                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ecuador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falklandsöarna                                    |                 |                |              |      ✓     |          ✓         |
| Franska Guyana                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guyana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexiko                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Puerto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sankt Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts och Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre och Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent och Grenadinerna                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Sydgeorgien och Sydsandwichöarna        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad och Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Förenta staternas mindre öar                |                 |                |              |      ✓     |          ✓         |
| USA i Amerika                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Brittiska Jungfruöarna                              |                 |                |              |      ✓     |          ✓         |
| Amerikanska jungfru öarna                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asien och stillahavsområdet

| Land/region                                      | Adress punkter |Hus nummer | Gatu nivå | Stads nivå | Intressanta punkter |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Amerikanska Samoa                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australien                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Bhutan                                              |                 |                |              |      ✓     |          ✓         |
| Brittiska territoriet i Indiska oceanen                      |                 |                |              |      ✓     |          ✓         |
| Darussalam                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodja                                            |                 |                |              |      ✓     |          ✓         |
| Kina                                               |                 |                |              |      ✓     |          ✓         |
| Julön                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kokos öarna                             |                 |                |              |      ✓     |          ✓         |
| Komorerna                                             |                 |                |              |      ✓     |          ✓         |
| Cooköarna                                        |                 |                |              |      ✓     |          ✓         |
| Fiji                                                |                  |                |              |      ✓     |          ✓        |
| Franska Polynesien                                    |                 |                |              |      ✓     |          ✓         |
| Heard-och McDonaldöarna                   |                 |                |              |      ✓     |          ✓         |
| Hong Kong SAR                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonesien                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indien                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japan                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Korea                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macao SAR                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malaysia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mikronesien                                          |                 |                |              |      ✓     |          ✓         |
| Mongoliet                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Nya Kaledonien                                       |                 |                |              |      ✓     |          ✓         |
| Nya Zeeland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolkön                                      |                 |                |              |      ✓     |          ✓         |
| Nord Korea                                         |                 |                |              |      ✓     |          ✓         |
| Nordmarianerna                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua Nya Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Filippinerna                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Senkaku öarna                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapore                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Salomonöarna                                     |                 |                |              |      ✓     |          ✓         |
| Södra Kurils                                     |        ✓        |                |              |      ✓     |          ✓         |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thailand                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelauöarna                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks- och Caicosöarna                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis och Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| Land/region                                      | Adress punkter |Hus nummer | Gatu nivå | Stads nivå | Intressanta punkter |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albanien                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenien                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Österrike                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbajdzjan                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belgien                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnien-Hercegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgarien                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Vitryssland                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kroatien                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cypern                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Tjeckien                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Danmark                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Färöarna                                       |                 |                |              |      ✓     |          ✓         |
| Finland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Frankrike                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Georgien                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Tyskland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grekland                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Grönland                                           |                 |                |              |      ✓     |          ✓         |
| Guernseypund                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ungern                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Island                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isle of man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italien                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazakstan                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirgizistan                                          |                 |                |              |      ✓     |          ✓         |
| Lettland                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litauen                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Nord Makedonien                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldavien                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Nederländerna                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norge                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polen                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| \+ Azorerna och Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Rumänien                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ryska federationen                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbien                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovakien                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovenien                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spanien                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Sverige                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Schweiz                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadzjikistan                                          |                 |                |              |      ✓     |          ✓         |
| Turkiet                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukraina                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Storbritannien                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Vatikanstaten                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Mellanöstern och Afrika

| Land/region                                      | Adress punkter |Hus nummer | Gatu nivå | Stads nivå | Intressanta punkter |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghanistan                                         |                 |                |              |      ✓     |          ✓         |
| Algeriet                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrain                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvetön                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kamerun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Centralafrikanska republiken                            |                 |                |       ✓      |      ✓     |          ✓         |
| Tchad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratiska                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d'Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratiska republiken Kongo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Djibouti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egypten                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ekvatorialguinea, Republiken                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopien                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Franska södra territorier|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinea-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israel                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordanien                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libanon                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libyen                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagaskar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldiverna                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshall öarna                                    |                 |                |              |      ✓     |          ✓         |
| Mauretanien                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marocko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Moçambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Réunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Sankta Helena                                        |                 |                |              |      ✓     |          ✓         |
| Saudiarabien                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychellerna                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalia                                             |                 |                |              |      ✓     |          ✓         |
| Sydafrika                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sydsudan                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Swaziland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syrien                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé och Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisien                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Förenade Arabemiraten                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Nästa steg

Mer information om Azure Maps-kod finns på [Sök](https://docs.microsoft.com/rest/api/maps/search) referens sidorna.

Lär dig mer om [täcknings områden för trafik tjänsten Maps](traffic-coverage.md). 

