---
title: Priser för erbjudanden som virtuell dator | Azure Marketplace
description: Beskriver de tre metoderna för att ange priser för erbjudanden för virtuella datorer.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dde3e04dc8f30ea5657139d50dd4456e5dfb57c2
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935727"
---
<a name="pricing-for-virtual-machine-offers"></a>Prissättning för erbjudanden på virtuella datorer
==================================

Det finns tre sätt att ange priser för erbjudanden som virtuell dator: anpassade core priser, priser per kärna och kalkylblad priser.


<a name="customized-core-pricing"></a>Anpassade core priser
-----------------------

Prissättning är specifik för varje region och core-kombination. Varje region i listan över säljer måste anges i den **virtualMachinePricing**/**regionPrices** avsnitt i definitionen.  Använd rätt valutakoder för varje [region](#regions) i din begäran.  I följande exempel visar dessa krav:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Per kärna-priser
----------------

I det här fallet utgivarna anger ett pris i USD för deras SKU: N och alla andra priser har genererats automatiskt. Pris per kärna har angetts i den **enda** parameter i förfrågan.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Priser för kalkylblad
-------------------

Utgivaren kan även överföra sina priser kalkylblad till en temporär lagringsplats och sedan inkludera URI i begäran som andra artefakter i filen. Kalkylbladet sedan har överförts kan översättas för att utvärdera det angivna pris schemat och uppdaterar slutligen erbjudandet med prisinformation. Efterföljande GET-begäranden för erbjudandet returneras i kalkylbladet URI och utvärderade priserna för regionen.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>Regioner
-------

I följande tabell visas de olika regionerna att du kan ange anpassade core priser och deras motsvarande valutakoder.

| **Region** | **Namn**             | **Valutakod** |
|------------|----------------------|-------------------|
| DZ         | Algeriet              | DZD               |
| AR         | Argentina            | ARS               |
| AU         | Australien            | AUD               |
| AT         | Österrike              | EUR               |
| BH         | Bahrain              | BHD               |
| BY         | Vitryssland              | RUB               |
| BE         | Belgien              | EUR               |
| BR         | Brasilien               | USD               |
| BG         | Bulgarien             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Colombia             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Kroatien              | HRK               |
| CY         | Cypern               | EUR               |
| CZ         | Tjeckien       | CZK               |
| DK         | Danmark              | DKK               |
| DO         | Dominikanska republiken   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egypten                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estland              | EUR               |
| FI         | Finland              | EUR               |
| FR         | Frankrike               | EUR               |
| DE         | Tyskland              | EUR               |
| GR         | Grekland               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hongkong SAR        | HKD               |
| HU         | Ungern              | HUF               |
| IS         | Island              | ISK               |
| IN         | Indien                | INR               |
| ID         | Indonesien            | IDR               |
| IE         | Irland              | EUR               |
| IL         | Israel               | ILS               |
| IT         | Italien                | EUR               |
| JP         | Japan                | JPY               |
| JO         | Jordanien               | JOD               |
| KZ         | Kazakhstan           | KZT               |
| KE         | Kenya                | KES               |
| KR         | Korea                | KRW               |
| KW         | Kuwait               | KWD               |
| LV         | Lettland               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litauen            | EUR               |
| LU         | Luxemburg           | EUR               |
| MK         | Nordmakedonien      | MKD               |
| MY         | Malaysia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Mexiko               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marocko              | MAD               |
| NL         | Nederländerna          | EUR               |
| NZ         | Nya Zeeland          | NZD               |
| NG         | Nigeria              | NGN               |
| NO         | Norge               | NOK               |
| OM         | Oman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | PENNA               |
| PH         | Filippinerna          | PHP               |
| PL         | Polen               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Qatar                | QAR               |
| RO         | Rumänien              | RON               |
| RU         | Ryssland               | RUB               |
| SA         | Saudiarabien         | SAR               |
| RS         | Serbien               | RSD               |
| SG         | Singapore            | DESCENT               |
| SK         | Slovakien             | EUR               |
| SI         | Slovenien             | EUR               |
| ZA         | Sydafrika         | ZAR               |
| ES         | Spanien                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Sverige               | SEK               |
| CH         | Schweiz          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Thailand             | THB               |
| TT         | Trinidad och Tobago  | TTD               |
| TN         | Tunisien              | TND               |
| TR         | Turkiet               | TRY               |
| UA         | Ukraina              | UAH               |
| AE         | Förenade Arabemiraten | EUR               |
| GB         | Storbritannien       | GBP               |
| USA         | USA        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
