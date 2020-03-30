---
title: Routningstäckning | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om regioner med routningstäckning för Microsoft Azure Maps.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b59816abf0cd03450c9fc4ebb8417c62df7e3288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529399"
---
# <a name="azure-maps-routing-coverage"></a>Routning av Azure Maps

Den här artikeln innehåller täckningsinformation för Azure Maps-routning. När en sökfråga returnerar Azure Maps en optimal väg från plats A till plats B. Du får korrekta restider, liveuppdateringar av reseinformation och ruttinstruktioner. Du kan också lägga till ytterligare sökparametrar som aktuell trafik, fordonstyp och villkor att undvika. Optimeringen av rutten beror på regionen. Det beror på att Azure Maps har olika nivåer av information och noggrannhet för olika regioner. I följande tabell visas regionerna och vilken typ av information du kan begära för dem.
 
Kolla in täckning för [**Geocoding**](geocoding-coverage.md).       
Kolla in täckning för [**trafik**](traffic-coverage.md).  
Kolla in täckning för [**Render**](render-coverage.md).
<br>  
**Legend**

| Symbol             | Betydelse                                |
|--------------------|----------------------------------------|
| ✓                  | Land/region som tillhandahålls med detaljerade uppgifter.   |
| Ø                  | Land/region försedd med förenklade uppgifter. |
| Landet saknas | Lands-/regiondata tillhandahålls inte.          |

<br>  

Följande tabell innehåller täckningsinformation för Azure Maps-routning.
<br>  


## <a name="africa"></a>Afrika

| Land/region                 | Routning         | Routning med trafik | Körning av lastbil |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Algeriet                        |        ✓       |                       |             |
| Angola                         |        ✓       |                       |             |
| Benin                          |        ✓       |                       |             |
| Botswana                       |        ✓       |                       |             |
| Burkina Faso                   |        ✓       |                       |             |
| Burundi                        |        ✓       |                       |             |
| Kamerun                       |        ✓       |                       |             |
| Cabo Verde                     |        ✓       |                       |             |
| Kongo- Brazzaville Kinshasa    |        ✓       |                       |             |
| Kongo- Kinshasa                |        ✓       |                       |             |
| Elfenbenskusten                  |        ✓       |                       |             |
| Egypten                          |        ✓       |         ✓            |             |
| Gabon                          |        ✓       |                       |             |
| Gambia                         |        ✓       |                       |             |
| Ghana                          |        ✓       |                       |             |
| Kenya                          |        ✓       |         ✓            |             |
| Lesotho                        |        Ø        |         ✓            |             |
| Malawi                         |        ✓       |                       |              |
| Mali                           |        ✓       |                       |             |
| Mauretanien                     |        ✓       |                       |             |
| Mauritius, Mayotte och Réunion |        ✓      |                       |             |
| Marocko                        |        ✓       |                       |             |
| Moçambique                     |        ✓       |         ✓            |             |
| Namibia                        |        ✓       |                       |             |
| Niger                          |        ✓       |                       |             |
| Nigeria                        |        ✓       |         ✓            |             |
| Rwanda                         |        ✓       |                       |             |
| Senegal                        |        ✓       |                       |             |
| Seychellerna                     |        ✓       |                       |             |
| Sydafrika                   |        ✓       ||         ✓            |             |
| Swaziland                      |        ✓       |                       |             |
| Tanzania                       |        ✓       |                       |             |
| Togo                           |        ✓       |                       |             |
| Tunisien                        |        ✓       |                       |             |
| Uganda                         |        ✓       |                       |             |
| Zambia                         |        ✓       |                       |             |
| Zimbabwe                       |        ✓       |                       |             |


## <a name="americas"></a>Nord- och Sydamerika

| Land/region                 | Routning         | Routning med trafik | Körning av lastbil |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Argentina                      |        ✓       |         ✓            |     ✓       |
| Antigua och Barbuda            |        ✓       |                      |             |
| Bahamas                        |        ✓       |                      |             |
| Barbados                       |        ✓       |                      |             |
| Belize                         |        ✓       |                      |             |
| Brasilien                         |        ✓       |         ✓            |     ✓       | 
| Kanada                         |        ✓       |         ✓            |     ✓      |
| Chile                          |        ✓       |         ✓            |     ✓      |
| Colombia                       |        ✓       |         ✓            |             |
| Costa Rica                     |        ✓       |                      |             |
| Kuba                           |        ✓       |                      |             |
| Dominica                       |        ✓       |                      |             |
| Dominikanska republiken             |        ✓       |                      |             |
| Ecuador                        |        ✓       |                      |             |
| El Salvador                    |        ✓       |                      |             |
| Franska Guyana                  |        ✓       |                      |             |
| Grenada                        |        ✓       |                      |             |
| Guatemala                      |        ✓       |                      |             |
| Guyana                         |        ✓       |                      |             |
| Haiti                          |        ✓       |                      |             |
| Honduras                       |        ✓       |                      |             |
| Jamaica                        |        ✓       |                      |             |
| Mexiko                         |        ✓       |         ✓            |     ✓      | 
| Nicaragua                      |        ✓       |                      |             |
| Panama                         |        ✓       |                      |             |
| Paraguay                       |        ✓       |                      |             |
| Peru                           |        ✓       |         ✓            |             |
| St Kitts och Nevis            |        ✓       |                      |             |
| S:t Lucia                      |        ✓       |                      |             |
| St Vincent & Grenadinerna       |        ✓       |                      |             |
| Surinam                       |        ✓       |                      |             |
| Trinidad & Tobago              |        ✓       |                      |             |
| USA                  |        ✓       |         ✓            |     ✓      | 
| +Amerikanska Samoa                |        ✓       |                      |             |
| +Nordmarianerna      |        ✓       |                      |             |
| +Puerto Rico                   |        ✓       |                      |             |
| +Amerikanska Jungfruöarna           |        ✓       |                      |             |
| Uruguay                        |        ✓       |         ✓            |     ✓      | 
| Venezuela                      |        ✓       |                      |             |


## <a name="asia-pacific"></a>Asien och stillahavsområdet

| Land/region                 | Routning         | Routning med trafik | Körning av lastbil |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Australien                      |        ✓       |         ✓            |     ✓       |
| +Julön              |        ✓       |                      |             |
| Brunei                         |        ✓       |         ✓            |             |
| Kambodja                       |        ✓       |                      |             |
| Fiji                           |        ✓       |                      |             |
| Guam                           |        ✓       |                      |             |
| Hongkong                  |        ✓       |         ✓            |             |
| Indien                          |        ✓       |         ✓            |             |
| Indonesien                      |        ✓       |         ✓            |             |
| Kazakhstan                |        ✓       |                     |             |
| Korea                          |        Ø       |                      |             |
| Laos                           |        ✓       |                      |             |
| Folkrepubliken Kinas särskilda administrativa region Macao                      |        ✓       |         ✓            |             |
| Malaysia                       |        ✓       |         ✓            |             |
| Myanmar                        |        ✓       |                      |             |
| Nya Zeeland                    |        ✓       |         ✓            |     ✓       | 
| Filippinerna                    |        ✓       |         ✓            |             |
| Singapore                      |        ✓       |         ✓            |             |
| Taiwan                         |        ✓       |         ✓            |             |
| Thailand                       |        ✓       |         ✓            |             |
| Vietnam                        |        ✓       |         ✓            |             |


## <a name="europe"></a>Europa

| Land/region                 | Routning         | Routning med trafik | Körning av lastbil |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Albanien                        |        ✓       |                      |             |
| Andorra                        |        ✓       |         ✓            |             |
| Österrike                        |        ✓       |         ✓            |     ✓       | 
| Vitryssland                        |        ✓       |                      |             |
| Belgien                        |        ✓       |         ✓            |     ✓      | 
| Bolivia                        |        ✓       |                      |             |
| Bosnien-Hercegovina             |        ✓       |                      |             |
| Bulgarien                       |        ✓       |         ✓            |     ✓      | 
| Kroatien                        |        ✓       |         ✓            |     ✓      |
| Cypern                         |        ✓       |                      |             |
| Tjeckien                 |        ✓       |         ✓            |     ✓      |
| Danmark                        |        ✓       |         ✓            |     ✓      |
| +Färöarna                 |        ✓       |                      |             |
| Estland                        |        ✓       |         ✓            |     ✓      |
| Finland                        |        ✓       |         ✓            |     ✓      |
| Frankrike                         |        ✓       |         ✓            |     ✓      | 
| +Guadeloupe                    |        ✓       |                      |             |
| +Martinique                    |        ✓       |                      |             |
| +St. Barthélemy                |        ✓       |                      |             |
| +St. Martin                    |        ✓       |                      |             |
| +St Pierre & Miquelon         |        ✓       |                      |             |
| Georgien                        |        ✓       |                      |             |
| Tyskland                        |        ✓       |         ✓            |     ✓      | 
| Grekland                         |        ✓       |         ✓            |     ✓      |  
| Guernsey                       |        ✓       |                      |             |
| Ungern                        |        ✓       |         ✓            |     ✓      | 
| Island                        |        ✓       |         ✓            |             |
| Irland                        |        ✓       |         ✓            |     ✓      | 
| Italien                          |        ✓       |         ✓            |     ✓      | 
| Jersey                         |        ✓       |                      |             |
| Lettland                         |        ✓       |         ✓            |     ✓      | 
| Liechtenstein                  |        ✓       |         ✓            |             |
| Litauen                      |        ✓       |         ✓            |     ✓      | 
| Luxemburg                     |        ✓       |         ✓            |     ✓      | 
| Nordmakedonien                |        ✓       |                      |             |
| Malta                          |        ✓       |         ✓            |             |
| Moldavien                        |        ✓       |                      |             |
| Monaco                         |        ✓       |         ✓            |             |
| Montenegro                     |        ✓       |                      |     ✓      | 
| Nederländerna                    |        ✓       |         ✓            |     ✓      | 
| +Aruba                         |        ✓       |                      |             | 
| +Karibien Nederländerna         |        ✓       |                      |             |
| +Curaçao                       |        ✓       |                      |             |
| +Sint Maarten                  |        ✓       |                      |             |
| Norge                         |        ✓       |         ✓            |     ✓      | 
| Polen                         |        ✓       |         ✓            |     ✓      | 
| Portugal                       |        ✓       |         ✓            |     ✓      |  
| Rumänien                        |        ✓       |         ✓            |     ✓      | 
| Ryska federationen             |        ✓       |         ✓            |     ✓      | 
| San Marino                     |        ✓       |         ✓            |             |
| Serbien                         |        ✓       |                      |     ✓      | 
| Slovakien                       |        ✓       |         ✓            |     ✓      | 
| Slovenien                       |        ✓       |         ✓            |     ✓      |
| Spanien                          |        ✓       |         ✓            |     ✓      | 
| Sverige                         |        ✓       |         ✓            |     ✓      | 
| Schweiz                    |        ✓       |         ✓            |     ✓      | 
| Turkiet                         |        ✓       |         ✓            |     ✓      | 
| Ukraina                        |        ✓       |         ✓            |             | 
| Storbritannien                 |        ✓       |         ✓            |     ✓      | 
| +Anguilla                      |        ✓       |                      |             |
| +Bermuda                       |        ✓       |                      |             |
| +Brittiska Jungfruöarna        |        ✓       |                      |             |
| +Caymanöarna                |        ✓       |                      |             |
| +Gibraltar                     |        ✓       |         ✓            |             | 
| +Montserrat                    |        ✓       |                      |             |
| +Turks- och Caicosöarna      |        ✓       |                      |             |
| Vatikanstaten                   |        ✓       |         ✓            |             |
| Isle of Man                    |        ✓       |                      |             |


## <a name="middle-east"></a>Mellanöstern

| Land/region                 | Routning         | Routning med trafik | Körning av lastbil |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Bahrain                        |        ✓       |         ✓            |             |
| Irak                           |        ✓       |                      |             |
| Israel                         |        ✓       |         ✓            |             |
| Jordanien                         |        ✓       |                      |             |
| Kuwait                         |        ✓       |         ✓            |             |
| Libanon                        |        ✓       |                      |             |
| Oman                           |        ✓       |         ✓            |             |
| Qatar                          |        ✓       |         ✓            |             |
| Saudiarabien                   |        ✓       |         ✓            |             |
| Förenade Arabemiraten           |        ✓       |         ✓            |             |
| Jemen                          |        ✓       |                      |             |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure Maps-routning finns på referenssidorna [för routning.](https://docs.microsoft.com/rest/api/maps/route)
