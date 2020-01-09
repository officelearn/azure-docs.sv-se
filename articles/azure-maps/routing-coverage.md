---
title: Routnings täckning i Azure Maps | Microsoft Docs
description: Lär dig mer om routning av täckning i Azure Maps
author: jingjing-z
ms.author: jinzh
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 4ca837d871dc439cadd1ca5b69bf9c7a5d12fddc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408355"
---
# <a name="azure-maps-routing-coverage"></a>Azure Maps routnings täckning

Den här artikeln innehåller information om disponering för Azure Maps routning. När du söker efter optimal väg från plats A till B kan Azure Maps ge hög noggrannhet, direkt uppdaterad rese information och väg instruktioner. Vägen tar hänsyn till ytterligare Sök villkor, till exempel aktuell trafik, fordons typ och villkor för att undvika. Möjligheten att optimera vägen beror på regionen som Azure Maps har olika nivåer av information och exakthet för olika regioner. I följande tabell visas de regioner och vilken typ av information som du kan begära för dem.
 
Ta en titt på täckning för [**kodning**](geocoding-coverage.md).       
Ta en titt på täckning för [**trafik**](traffic-coverage.md).  
Kolla ut täckning för [**rendering**](render-coverage.md).
<br>  
**Förklaring**

| Symbol             | Betydelse                                |
|--------------------|----------------------------------------|
| ✓                  | Land/region som tillhandahålls med detaljerade data.   |
| Ø                  | Land/region som tillhandahålls med förenklade data. |
| Land saknas | Lands-/regions data har inte angetts.          |

<br>  

Följande tabell innehåller information om disponering för Azure Maps routning.
<br>  


## <a name="africa"></a>Afrika

| Land/region                 | Routning         | Routning med trafik | Truck-routning |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Algeriet                        |        ✓       |                       |             |
| Angola                         |        ✓       |                       |             |
| Benin                          |        ✓       |                       |             |
| Botswana                       |        ✓       |                       |             |
| Burkina Faso                   |        ✓       |                       |             |
| Burundi                        |        ✓       |                       |             |
| Kamerun                       |        ✓       |                       |             |
| Cabo Verde                     |        ✓       |                       |             |
| Republiken Kongo-Brazzaville kongolesiska    |        ✓       |                       |             |
| Republiken Kongo-kongolesiska                |        ✓       |                       |             |
| Côte d’Ivoire                  |        ✓       |                       |             |
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

| Land/region                 | Routning         | Routning med trafik | Truck-routning |
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
| Saint Kitts och Nevis            |        ✓       |                      |             |
| St. Lucia                      |        ✓       |                      |             |
| Saint Vincent & grenadin       |        ✓       |                      |             |
| Surinam                       |        ✓       |                      |             |
| Trinidad & Tobago              |        ✓       |                      |             |
| USA                  |        ✓       |         ✓            |     ✓      | 
| \+ Amerikanska Samoa                |        ✓       |                      |             |
| \+ Nordmarianerna      |        ✓       |                      |             |
| +Puerto Rico                   |        ✓       |                      |             |
| \+ Amerikanska jungfru öarna           |        ✓       |                      |             |
| Uruguay                        |        ✓       |         ✓            |     ✓      | 
| Venezuela                      |        ✓       |                      |             |


## <a name="asia-pacific"></a>Asien och stillahavsområdet

| Land/region                 | Routning         | Routning med trafik | Truck-routning |
|--------------------------------|:---------------:|:--------------------:|:------------:|
| Australien                      |        ✓       |         ✓            |     ✓       |
| \+ Julön              |        ✓       |                      |             |
| Brunei                         |        ✓       |         ✓            |             |
| Kambodja                       |        ✓       |                      |             |
| Fiji                           |        ✓       |                      |             |
| Guam                           |        ✓       |                      |             |
| Hongkong SAR                  |        ✓       |         ✓            |             |
| Indien                          |        ✓       |         ✓            |             |
| Indonesien                      |        ✓       |         ✓            |             |
| Kazakstan                |        ✓       |                     |             |
| Korea                          |        Ø       |                      |             |
| Laos                           |        ✓       |                      |             |
| Macao SAR                      |        ✓       |         ✓            |             |
| Malaysia                       |        ✓       |         ✓            |             |
| Myanmar                        |        ✓       |                      |             |
| Nya Zeeland                    |        ✓       |         ✓            |     ✓       | 
| Filippinerna                    |        ✓       |         ✓            |             |
| Singapore                      |        ✓       |         ✓            |             |
| Taiwan                         |        ✓       |         ✓            |             |
| Thailand                       |        ✓       |         ✓            |             |
| Vietnam                        |        ✓       |         ✓            |             |


## <a name="europe"></a>Europa

| Land/region                 | Routning         | Routning med trafik | Truck-routning |
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
| \+ Färöarna                 |        ✓       |                      |             |
| Estland                        |        ✓       |         ✓            |     ✓      |
| Finland                        |        ✓       |         ✓            |     ✓      |
| Frankrike                         |        ✓       |         ✓            |     ✓      | 
| +Guadeloupe                    |        ✓       |                      |             |
| \+ Martinique                    |        ✓       |                      |             |
| \+ St. Barthélemy                |        ✓       |                      |             |
| \+ St. Martin                    |        ✓       |                      |             |
| \+ Saint Pierre & Miquelon         |        ✓       |                      |             |
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
| \+ Karibien Nederländerna         |        ✓       |                      |             |
| +Curaçao                       |        ✓       |                      |             |
| +Sint Maarten                  |        ✓       |                      |             |
| Norge                         |        ✓       |         ✓            |     ✓      | 
| Polen                         |        ✓       |         ✓            |     ✓      | 
| Portugal                       |        ✓       |         ✓            |     ✓      |  
| Rumänien                        |        ✓       |         ✓            |     ✓      | 
| Ryska federationen             |        ✓       |         ✓            |     ✓      | 
| San Marino                     |        ✓       |         ✓            |             |
| +Sint Maarten                  |        ✓       |                      |             |
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
| \+ Brittiska Jungfru öarna        |        ✓       |                      |             |
| \+ Cayman öarna                |        ✓       |                      |             |
| +Gibraltar                     |        ✓       |         ✓            |             | 
| +Montserrat                    |        ✓       |                      |             |
| \+ Turks-och Caicos-öarna      |        ✓       |                      |             |
| Vatikanstaten                   |        ✓       |         ✓            |             |
| Isle of Man                    |        ✓       |                      |             |


## <a name="middle-east"></a>Mellanöstern

| Land/region                 | Routning         | Routning med trafik | Truck-routning |
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

Mer information om Azure Maps routning finns i referens sidorna för [routning](https://docs.microsoft.com/rest/api/maps/route) .
