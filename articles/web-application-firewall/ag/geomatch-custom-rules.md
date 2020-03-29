---
title: Anpassade regler för Geomatch (Azure Web Application Firewall)
description: Den här artikeln är en översikt över WAF-geomatchnings anpassade regler för geomatchning (Web Application Firewall) på Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: 6725e1de21dbd103850071f7511e2800c6bd7b69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961093"
---
# <a name="geomatch-custom-rules-preview"></a>Anpassade regler för Geomatch (förhandsgranskning)

Med anpassade regler kan du skapa skräddarsydda regler som passar de exakta behoven hos dina program och säkerhetsprinciper. Nu kan du begränsa åtkomsten till dina webbprogram efter land/region. Som med alla anpassade regler kan den här logiken förvärras med andra regler som passar behoven hos ditt program.

Om du vill skapa en anpassad geofiltreringsregel väljer du helt enkelt *Geografisk plats* som matchningstyp och väljer sedan det land eller de länder som du vill tillåta/blockera från ditt program. Se [hur du skapar anpassade regler i Powershell](configure-waf-custom-rules.md) och fler anpassade regelexempel (skapa-anpassad-waf-rules.md) för mer information.

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="country-codes"></a>Landskoder

Om du använder operatorn Geomatch kan selektorerna vara någon av följande tvåsiffriga landskoder. 

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
| BL | Sankt Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brasilien|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botswana|
| BY | Vitryssland|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratiska republiken Kongo|
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
| HK | Hongkong|
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
| MO | Folkrepubliken Kinas särskilda administrativa region Macao|
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

När du har lärt dig mer om anpassade regler [skapar du egna anpassade regler](create-custom-waf-rules.md).
