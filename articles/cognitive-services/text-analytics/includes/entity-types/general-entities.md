---
title: Allmänna namngivna entiteter
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211370"
---
## <a name="general-entity-types"></a>Typer av entitet:

### <a name="person"></a>Person

Känn igen personnamn i text.

Språk:
* `Arabic`Offentlig förhandsversion: `Chinese-Simplified`, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , , `Danish`, , , , , , , , , , , , , , , , och `Czech``Turkish`

| Undertypsnamn | Beskrivning                                                      | Tillgänglig från och med modellversion |
|--------------|------------------------------------------------------------------|---------------------------------------|
| Ej tillämpligt          | Namn på igenkända personer, till exempel `Bill Gates``Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType (PersonType)
Jobbtyp eller roll som innehas av en person.

Språk:
* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                                                                | Tillgänglig från och med modellversion |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Jobbtyper till `civil engineer` `salesperson`exempel `chef` `librarian`, , , ,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Location

Naturliga och mänskligt skapade landmärken, strukturer, geografiska särdrag och geopolitiska enheter.

Språk:

* `Arabic`Offentlig förhandsversion: `Chinese-Simplified`, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , , `Danish`, , , , , , , , , , , , , , , , och `Czech``Turkish`

| Undertypsnamn              | Beskrivning                                                                              | Tillgänglig från och med modellversion |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt                       | platser, till `Atlantic Ocean` `library`exempel `Eiffel Tower`, ,`Statue of Liberty`  | `2019-10-01`                           |
| Geopolitisk enhet (GPE) - endast engelska| Städer, länder, stater `Seattle` `Pennsylvania`till `South Africa`exempel , ,`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organisation  

Erkända organisationer, företag, byråer och andra grupper av människor. Till exempel: företag, politiska grupper, musikband, idrottsklubbar, statliga organ och offentliga organisationer. Nationaliteter och religioner ingår inte i denna enhetstyp. 

Språk: 

* `Arabic`Offentlig förhandsversion: `Chinese-Simplified`, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , , `Danish`, , , , , , , , , , , , , , , , och `Czech``Turkish`

| Undertypsnamn | Beskrivning                                                                                             | Tillgänglig från och med modellversion |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | organisationer, till `Microsoft`exempel `NASA` `National Oceanic and Atmospheric Administration`, , ,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Händelse  

Historiska, sociala och naturliga händelser.  

Språk: 

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                                            | Tillgänglig från och med modellversion |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Händelser som `wedding` `hurricane`, `car accident` `solar eclipse`, , ,`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produkt  

Fysiska objekt i olika kategorier.  

Språk: 

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                                                        | Tillgänglig från och med modellversion |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Till exempel `Microsoft Surface laptop` `sunglasses`, `motorcycle` `bag`, ,`Xbox` | `2020-02-01`                           |
| Databehandling    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Skicklighet  

En enhet som beskriver en kapacitet eller expertis.  

Språk: 

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                                                 | Tillgänglig från och med modellversion |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefonnummer

Telefonnummer (endast amerikanska telefonnummer). 

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                    | Tillgänglig från och med modellversion |
|--------------|------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Amerikanska telefonnummer, till exempel`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>E-post

E-postadress. 

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                      | Tillgänglig från och med modellversion |
|--------------|--------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | E-postadress, till exempel`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

Internet-URL:er.

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                          | Tillgänglig från och med modellversion |
|--------------|------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Webbadresser till webbplatser, till exempel`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP-adress

Internet-protokolladress

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                              | Tillgänglig från och med modellversion |
|--------------|------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Nätverksadress till exempel`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Datum och tid entiteter. 

* Tillgänglig från och med modellversion`2019-10-01`

Språk:

* Offentlig förhandsversion: `Chinese-Simplified`, `English`, `French` `German` och`Spanish`

| Undertypsnamn    | Exempel                     |
|-------------|------------------------------|
| Ej tillämpligt         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Datum  | `May 2nd, 2017`, `05/02/2017`       |
| Tid     | `8:15`, `6AM`              |
| DatumRange    | `August 2nd to August 5th`         |
| TimeRange (Tidsföring)   | `4-6PM`, `10:00AM to Noon`          |
| Varaktighet | `2.5 minutes`, `one and a half hours`         |
| Ange | `every Saturday`         |

###  <a name="quantity"></a>Kvantitet

Siffror och numeriska kvantiteter. 

* Tillgänglig från och med modellversion`2019-10-01`

Språk:

* Offentlig förhandsversion: `Chinese-Simplified`, `English`, `French` `German` och`Spanish`

| Undertypsnamn    | Exempel                     |
|-------------|------------------------------|
| Tal         | `6`, `six`                   |
| Procent  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Ålder         | `90 day old`, `30 years old` |
| Valuta    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Temperatur | `32 degrees`, `10°C`         |
