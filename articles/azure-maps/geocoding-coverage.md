---
title: Omfattning för att koda | Microsoft Azure Maps
description: Processen att konvertera plats adressen till latitud-och longitud-koordinater kallas för att koda. I den här artikeln får du lära dig om regioner med distrikts täckning i Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 12/31/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 38d25b7494f8b2f3e1abdac959edde8969d7d4c7
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911635"
---
# <a name="azure-maps-geocoding-coverage"></a>Azure Maps för att avkoda täckning

När du söker efter en plats med Azure Maps Sök tjänsten, till exempel Sök [adress](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress), tar dina Sök villkor och returnerar koordinaterna för latitud och longitud. Den här processen kallas för kodning. Azure Maps har dock inte samma nivå av information och noggrannhet för alla regioner och länder. Använd den här artikeln för att avgöra vilken typ av platser du kan söka efter på ett tillförlitligt sätt i varje region. 

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
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
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
| Amerikas förenta stater                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
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
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kambodja                                            |                 |                |              |      ✓     |          ✓         |
| Kina                                               |                 |                |              |      ✓     |          ✓         |
| Julön                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Kokosöarna                             |                 |                |              |      ✓     |          ✓         |
| Komorerna                                             |                 |                |              |      ✓     |          ✓         |
| Cooköarna                                        |                 |                |              |      ✓     |          ✓         |
| Fiji                                                |                  |                |              |      ✓     |          ✓        |
| Franska Polynesien                                    |                 |                |              |      ✓     |          ✓         |
| Heardön och McDonaldöarna                   |                 |                |              |      ✓     |          ✓         |
| Hongkong SAR                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
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
| Nordkorea                                         |                 |                |              |      ✓     |          ✓         |
| Nordmarianerna                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua Nya Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Filippinerna                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoaöarna                                               |                 |                |              |      ✓     |          ✓         |
| Senkaku-öarna/Diaoyutai-öarna i Oceanien och Västindien                                     |        ✓        |                |              |      ✓     |          ✓         |
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
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
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
| Nordmakedonien                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
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
| Kongo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d'Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Demokratiska republiken Kongo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Djibouti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egypten                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ekvatorialguinea, Republiken                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiopien                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Franska sydterritorierna|                        |                |              |      ✓     |          ✓         |
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
| Marshallöarna                                    |                 |                |              |      ✓     |          ✓         |
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

