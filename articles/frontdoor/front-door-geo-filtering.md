---
title: Självstudie – Geofiltrering på en domän för Azure Front Door Service | Microsoft Docs
description: I den här artikeln får du lära dig om geofiltreringprinciper för Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: KumudD
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: 1d6343705e8d27fb2d22827cf792de332861456e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007975"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>Vad är geo-filtrering på en domän för Azure-åtkomsten?

Som standard svarar Azure Front Door Service på användarnas begäranden oavsett var användaren som skickar begäran finns. Men i vissa fall kan du begränsa åtkomsten till dina webbprogram efter land. Webbtjänsten application firewall (WAF) på ytterdörren kan du definiera en princip med hjälp av anpassade åtkomstregler för specifik sökväg på din slutpunkt för att tillåta eller blockera åtkomst från angivna länder. 

En WAF-principen innehåller vanligtvis en uppsättning anpassade regler. En regel består av matchningsvillkor, en åtgärd och en prioritet. I matchningsvillkor definierar du en matchningsvariabel, en operator och ett matchningsvärde.  För en geofiltreringsregel är matchningsvariabeln REMOTE_ADDR, operatorn är GeoMatch och värdet är landskoden på två bokstäver av intresse. Du kan kombinera ett GeoMatch-villkor och ett matchningsvillkor för REQUEST_URI-sträng för att skapa en sökvägsbaserad geofiltreringsregel.

Du kan konfigurera en princip för geo-filtrering för ytterdörren genom att antingen använda [Azure PowerShell](front-door-tutorial-geo-filtering.md) eller med hjälp av vår [snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="country-code-reference"></a>Referens till land

|Landskod | Landsnamn |
| ----- | ----- |
| AD | Andorra |
| AE | Förenade Arabemiraten|
| AF | Afghanistan|
| AG | Antigua och Barbuda|
| AL | Albanien|
| AM | Armenien|
| AO | Angola|
| AR | Argentina|
| AS | Amerikanska Samoa|
| AT | Österrike|
| AU | Australien|
| AZ | Azerbajdzjan|
| BA | Bosnien och Hercegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Belgien|
| BF | Burkina Faso|
| BG | Bulgarien|
| BH | Bahrain|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthelemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brasilien|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botswana|
| BY | Vitryssland|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratiska republiken Kongo |
| CF | Centralafrikanska republiken|
| CH | Schweiz|
| CI | Cote d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | Kina|
| CO | Colombia|
| CR | Costa Rica|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Cypern|
| CZ | Tjeckien|
| DE | Tyskland|
| DK | Danmark|
| DO | Dominikanska republiken|
| DZ | Algeriet|
| EC | Ecuador|
| EE | Estland|
| EG | Egypten|
| ES | Spanien|
| ET | Etiopien|
| FI | Finland|
| FJ | Fiji|
| FM | Mikronesien|
| FR | Frankrike|
| GB | Storbritannien|
| GE | Georgien|
| GF | Franska Guyana|
| GH | Ghana|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Grekland|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong SAR|
| HN | Honduras|
| HR | Kroatien|
| HT | Haiti|
| HU | Ungern|
| ID | Indonesien|
| IE | Irland|
| IL | Israel|
| IN | Indien|
| IQ | Irak|
| IR | Islamiska republiken Iran|
| IS | Island|
| IT | Italien|
| JM | Jamaica|
| JO | Jordanien|
| JP | Japan|
| KE | Kenya|
| KG | Kirgizistan|
| KH | Kambodja|
| KI | Kiribati|
| KN | Saint Kitts och Nevis|
| KP | Nordkorea|
| KR | Sydkorea|
| KW | Kuwait|
| KY | Caymanöarna|
| KZ | Kazakhstan|
| LA | Demokratiska folkrepubliken Laos|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litauen|
| LU | Luxemburg|
| LV | Lettland|
| LY | Libyen |
| MA | Marocko|
| MD | Moldavien|
| MG | Madagaskar|
| MK | Nordmakedonien|
| ML | Mali|
| MM | Myanmar|
| MN | Mongoliet|
| MO | Macao SAR|
| MQ | Martinique|
| MR | Mauretanien|
| MT | Malta|
| MV | Maldiverna|
| MW | Malawi|
| MX | Mexiko|
| MY | Malaysia|
| MZ | Moçambique|
| Ej tillämpligt | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Nederländerna|
| NO | Norge|
| NP | Nepal|
| NR | Nauru|
| NZ | Nya Zeeland|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filippinerna|
| PK | Pakistan|
| PL | Polen|
| PR | Puerto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | Réunion|
| RO | Rumänien|
| RS | Serbien|
| RU | Ryska federationen|
| RW | Rwanda|
| SA | Saudiarabien|
| SD | Sudan|
| SE | Sverige|
| SG | Singapore|
| SI | Slovenien|
| SK | Slovakien|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Sydsudan|
| SV | El Salvador|
| SY | Arabrepubliken Syrien|
| SZ | Swaziland|
| TC | Turks- och Caicosöarna|
| TG | Togo|
| TH | Thailand|
| TN | Tunisien|
| TR | Turkiet|
| TT | Trinidad och Tobago|
| TW | Taiwan|
| TZ | Tanzania|
| UA | Ukraina|
| UG | Uganda|
| USA | USA|
| UY | Uruguay|
| UZ | Uzbekistan|
| VC | Saint Vincent och Grenadinerna|
| VE | Venezuela|
| VG | Jungfruöarna, Storbritannien|
| VI | Jungfruöarna, USA|
| VN | Vietnam|
| ZA | Sydafrika|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [säkerhet på programnivå med Front Door](front-door-application-security.md).
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
