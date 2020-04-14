---
title: Priser för virtuella datorerbjudanden | Azure Marketplace
description: Förklarar de tre metoderna för att ange prissättning av virtuella datorerbjudanden.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 487e66b39bc63363497cb3497d32158efd0c6c8a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255897"
---
<a name="pricing-for-virtual-machine-offers"></a>Prissättning för erbjudanden på virtuella datorer
==================================

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Det finns tre sätt att ange priser för erbjudanden om virtuella datorer: anpassad kärnprissättning, priser per kärna och kalkylprissättning.


<a name="customized-core-pricing"></a>Anpassad kärnprissättning
-----------------------

Prissättningen är specifik för varje region och kärnkombination. Varje region i försäljningslistan måste anges i avsnittet **virtualMachinePricing**/**regionPrices** i definitionen.  Använd rätt valutakoder för varje [region](#regions) i din begäran.  Följande exempel visar dessa krav:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Priser per kärna
----------------

I det här fallet anger utgivarena ett pris i USD för sin SKU och alla andra priser genereras automatiskt. Priset per kärna anges i den **enda** parametern i begäran.

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


<a name="spreadsheet-pricing"></a>Prissättning för kalkylblad
-------------------

Utgivaren kan också ladda upp sitt priskalkylblad till en tillfällig lagringsplats och sedan inkludera URI:n i begäran som andra filartefakter. Kalkylbladet laddas sedan upp, översätts för att utvärdera det angivna prisschemat och uppdaterar slutligen erbjudandet med prisinformationen. Efterföljande GET-begäranden för erbjudandet returnerar kalkylblads-URI och de utvärderade priserna för regionen.

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

<a name="new-core-sizes-added-on-722019"></a>Nya kärnstorlekar tillagda den 7/2/2019
---------------------------

VM-utgivare informerades den 2 juli 2019 om tillägg av nya priser för nya Azure-storlekar för virtuella datorer (baserat på antalet kärnor).  De nya priserna är för kärnstorlekarna 10, 44, 48, 60, 120, 208 och 416.  För befintliga VM erbjuder nya priser för dessa kärnor storlekar beräknades automatiskt baserat på aktuella priser.  Utgivare har fram till 1 augusti 2019 på sig att granska de extra priserna och göra önskade ändringar.  Efter detta datum, om inte redan återpublicerats av utgivaren, börjar de automatiskt beräknade priserna för dessa nya kärnstorlekar att gälla.


<a name="regions"></a>Regioner
-------

I följande tabell visas de olika regioner som du kan ange för anpassade kärnpriser och motsvarande valutakoder.

| **Regionen** | **Namn**             | **Valutakod** |
|------------|----------------------|-------------------|
| DZ         | Algeriet              | Dzd               |
| AR         | Argentina            | ARS               |
| AU         | Australien            | AUD               |
| AT         | Österrike              | EUR               |
| BH         | Bahrain              | Bhd               |
| BY         | Vitryssland              | RUB               |
| BE         | Belgien              | EUR               |
| BR         | Brasilien               | USD               |
| BG         | Bulgarien             | Bgn               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | Clp               |
| CO         | Colombia             | Cop               |
| CR         | Costa Rica           | Crc               |
| HR         | Kroatien              | Hrk               |
| CY         | Cypern               | EUR               |
| CZ         | Tjeckien       | Czk               |
| DK         | Danmark              | DKK               |
| DO         | Dominikanska republiken   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egypten                | Egp               |
| SV         | El Salvador          | USD               |
| EE         | Estland              | EUR               |
| FI         | Finland              | EUR               |
| FR         | Frankrike               | EUR               |
| DE         | Tyskland              | EUR               |
| GR         | Grekland               | EUR               |
| GT         | Guatemala            | Gtq               |
| HK         | Hongkong        | HKD               |
| HU         | Ungern              | Huf               |
| IS         | Island              | Isk               |
| IN         | Indien                | INR               |
| ID         | Indonesien            | IDR               |
| IE         | Irland              | EUR               |
| IL         | Israel               | Ils               |
| IT         | Italien                | EUR               |
| JP         | Japan                | JPY               |
| JO         | Jordanien               | JOD (JOD)               |
| KZ         | Kazakhstan           | Kzt               |
| KE         | Kenya                | Kes               |
| KR         | Korea                | KRW               |
| KW         | Kuwait               | Kwd               |
| LV         | Lettland               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litauen            | EUR               |
| LU         | Luxemburg           | EUR               |
| MK         | Nordmakedonien      | Mkd               |
| MY         | Malaysia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Mexiko               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marocko              | Mad               |
| NL         | Nederländerna          | EUR               |
| NZ         | Nya Zeeland          | NZD               |
| NG         | Nigeria              | Ngn               |
| NO         | Norge               | NOK               |
| OM         | Oman                 | Omr               |
| PK         | Pakistan             | Pkr               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | Pyg               |
| PE         | Peru                 | Penna               |
| PH         | Filippinerna          | PHP               |
| PL         | Polen               | Pln               |
| PT         | Portugal             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Qatar                | Qar               |
| RO         | Rumänien              | Ron               |
| RU         | Ryssland               | RUB               |
| SA         | Saudiarabien         | SAR               |
| RS         | Serbien               | Rsd               |
| SG         | Singapore            | SGD               |
| SK         | Slovakien             | EUR               |
| SI         | Slovenien             | EUR               |
| ZA         | Sydafrika         | ZAR               |
| ES         | Spanien                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Sverige               | SEK               |
| CH         | Schweiz          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Thailand             | Thb               |
| TT         | Trinidad och Tobago  | Ttd               |
| TN         | Tunisien              | Tnd               |
| TR         | Turkiet               | TRY               |
| UA         | Ukraina              | Uah               |
| AE         | Förenade Arabemiraten | EUR               |
| GB         | Storbritannien       | GBP               |
| USA         | USA        | USD               |
| UY         | Uruguay              | UYU (storbritannien)               |
| VE         | Venezuela            | USD               |
|  |  |  |
