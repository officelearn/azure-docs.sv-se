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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77211370"
---
## <a name="general-entity-types"></a>Allmänna enhets typer:

### <a name="person"></a>Person

Identifiera person namn i text.

Språk:
* Offentlig för hands `Arabic`version `Czech`: `Chinese-Simplified`, `Danish`, `Dutch` `English` `Finnish` `French` `Norwegian (Bokmål)` `Polish` `Spanish` `Swedish` ,, `Portuguese (Portugal)`,,,,,, `Portuguese (Brazil)`, `Russian`,,,,,,,, och `German` `Hungarian` `Italian` `Japanese` `Korean``Turkish`

| Under typs namn | Beskrivning                                                      | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------------------|---------------------------------------|
| Ej tillämpligt          | Identifierade person namn, till `Bill Gates`exempel`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Jobb typ eller roll som innehas av en person.

Språk:
* Offentlig för hands version:`English`

| Under typs namn | Beskrivning                                                                                | Tillgängligt från och med modell version |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Jobb typer till exempel `civil engineer` `salesperson` `chef` `librarian`,,,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Plats

Naturliga och mänskliga landmärkeer, strukturer, geografiska egenskaper och geografiskt politiska enheter.

Språk:

* Offentlig för hands `Arabic`version `Czech`: `Chinese-Simplified`, `Danish`, `Dutch` `English` `Finnish` `French` `Norwegian (Bokmål)` `Polish` `Spanish` `Swedish` ,, `Portuguese (Portugal)`,,,,,, `Portuguese (Brazil)`, `Russian`,,,,,,,, och `German` `Hungarian` `Italian` `Japanese` `Korean``Turkish`

| Under typs namn              | Beskrivning                                                                              | Tillgängligt från och med modell version |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt                       | platser, till exempel `Atlantic Ocean` `library` `Eiffel Tower`,,`Statue of Liberty`  | `2019-10-01`                           |
| GPE (politisk entitet) – endast engelska| Städer, länder, regioner, t `Seattle`. `Pennsylvania`ex `South Africa`.,`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organisation  

Erkända organisationer, organisationer, organ och andra grupper av personer. Exempel: företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer. Nationella objekt och religions ingår inte i den här entitetstypen. 

Språk: 

* Offentlig för hands `Arabic`version `Czech`: `Chinese-Simplified`, `Danish`, `Dutch` `English` `Finnish` `French` `Norwegian (Bokmål)` `Polish` `Spanish` `Swedish` ,, `Portuguese (Portugal)`,,,,,, `Portuguese (Brazil)`, `Russian`,,,,,,,, och `German` `Hungarian` `Italian` `Japanese` `Korean``Turkish`

| Under typs namn | Beskrivning                                                                                             | Tillgängligt från och med modell version |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | organisationer, till exempel `Microsoft` `NASA` `National Oceanic and Atmospheric Administration`,,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Händelse  

Historiska, sociala och naturliga händelser.  

Språk: 

* Offentlig för hands version:`English`

| Under typs namn | Beskrivning                                                            | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Händelser som `wedding`, `hurricane` `car accident`,,, `solar eclipse``American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produkt  

Fysiska objekt av olika kategorier.  

Språk: 

* Offentlig för hands version:`English`

| Under typs namn | Beskrivning                                                                        | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | `Microsoft Surface laptop`Till exempel `sunglasses` `motorcycle`,,,, `bag``Xbox` | `2020-02-01`                           |
| Databehandling    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Kvalifikation  

En entitet som beskriver en funktion eller expertis.  

Språk: 

* Offentlig för hands version:`English`

| Under typs namn | Beskrivning                                                                 | Tillgängligt från och med modell version |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefonnummer

Telefonnummer (endast telefonnummer till USA). 

Språk:

* Offentlig för hands version:`English`

| Under typs namn | Beskrivning                                    | Tillgängligt från och med modell version |
|--------------|------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | AMERIKANSKt telefonnummer, till exempel`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>E-post

E-postadress. 

Språk:

* Offentlig för hands version:`English`

| Under typs namn | Beskrivning                                      | Tillgängligt från och med modell version |
|--------------|--------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | E-postadress, till exempel`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

Internet-URL: er.

Språk:

* Offentlig för hands version:`English`

| Under typs namn | Beskrivning                                          | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | URL: er till webbplatser, till exempel`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP-adress

Internet Protocol adress

Språk:

* Offentlig för hands version:`English`

| Under typs namn | Beskrivning                              | Tillgängligt från och med modell version |
|--------------|------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Nätverks adress till exempel`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Datum-och tidsentiteter. 

* Tillgängligt från och med modell version`2019-10-01`

Språk:

* Offentlig för hands `Chinese-Simplified`version `English`: `French`, `German` , och`Spanish`

| Under typs namn    | Exempel                     |
|-------------|------------------------------|
| Ej tillämpligt         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Tid     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Varaktighet | `2.5 minutes`, `one and a half hours`         |
| Ange | `every Saturday`         |

###  <a name="quantity"></a>Kvantitet

Siffror och numeriska kvantiteter. 

* Tillgängligt från och med modell version`2019-10-01`

Språk:

* Offentlig för hands `Chinese-Simplified`version `English`: `French`, `German` , och`Spanish`

| Under typs namn    | Exempel                     |
|-------------|------------------------------|
| Antal         | `6`, `six`                   |
| Procent  | `50%`, `fifty percent`       |
| Numret     | `2nd`, `second`              |
| Ålder         | `90 day old`, `30 years old` |
| Valuta    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Temperatur | `32 degrees`, `10°C`         |
