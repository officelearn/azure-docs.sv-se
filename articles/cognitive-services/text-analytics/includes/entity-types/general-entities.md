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
ms.openlocfilehash: f95d7613926cf332a498cca84563dbc0ebcbbe9b
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086885"
---
## <a name="general-entity-types"></a>Allmänna enhets typer:

### <a name="person"></a>Person

Identifiera person namn i text.

Användning
* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                      | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------------------|---------------------------------------|
| SAKNAS          | Identifierade person namn, till exempel `Bill Gates``Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Jobb typ eller roll som innehas av en person.

Användning
* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                                | Tillgängligt från och med modell version |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| SAKNAS          | Jobb typer till exempel `civil engineer`, `salesperson`, `chef`, `librarian`, `nursing aide` | `2020-02-01`                           |

### <a name="location"></a>plats.

Naturliga och mänskliga landmärkeer, strukturer, geografiska egenskaper och geografiskt politiska enheter.

Användning

* Offentlig för hands version: `English`

| Under typs namn              | Beskrivning                                                                              | Tillgängligt från och med modell version |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| SAKNAS                       | platser, till exempel `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty`  | `2019-10-01`                           |
| GPE (politisk entitet) | Städer, länder, stater till exempel `Seattle`, `Pennsylvania`, `South Africa`, `Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organisation  

Erkända organisationer, organisationer, organ och andra grupper av personer. Exempel: företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer. Nationella objekt och religions ingår inte i den här entitetstypen. 

Användning 

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                                             | Tillgängligt från och med modell version |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| SAKNAS          | organisationer, till exempel `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration`,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Händelse  

Historiska, sociala och naturliga händelser.  

Användning 

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                            | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| SAKNAS          | Händelser som `wedding`, `hurricane`, `car accident`, `solar eclipse`, `American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produkt  

Fysiska objekt av olika kategorier.  

Användning 

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                        | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| SAKNAS          | Till exempel `Microsoft Surface laptop`, `sunglasses`, `motorcycle`, `bag`, `Xbox` | `2020-02-01`                           |
| Databehandling    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Kvalifikation  

En entitet som beskriver en funktion eller expertis.  

Användning 

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                 | Tillgängligt från och med modell version |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| SAKNAS          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Telefonnummer

Telefonnummer (endast telefonnummer till USA). 

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                    | Tillgängligt från och med modell version |
|--------------|------------------------------------------------|----------------------------------------|
| SAKNAS          | AMERIKANSKt telefonnummer, till exempel `(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>E-post

E-postadress. 

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                      | Tillgängligt från och med modell version |
|--------------|--------------------------------------------------|----------------------------------------|
| SAKNAS          | E-postadress, till exempel `support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

Internet-URL: er.

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                          | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------|----------------------------------------|
| SAKNAS          | URL: er till webbplatser, till exempel `https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP-adress

Internet Protocol adress

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                              | Tillgängligt från och med modell version |
|--------------|------------------------------------------|----------------------------------------|
| SAKNAS          | Nätverks adress till exempel `10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Datum-och tidsentiteter. 

* Tillgängligt från och med modell version `2019-10-01`

Användning

* Offentlig för hands version: `English`

| Under typs namn    | Exempel                     |
|-------------|------------------------------|
| SAKNAS         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Tid     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Varaktighet | `2.5 minutes`, `one and a half hours`         |
| Ange | `every Saturday`         |

###  <a name="quantity"></a>Kvantitet

Siffror och numeriska kvantiteter. 

* Tillgängligt från och med modell version `2019-10-01`

Användning

* Offentlig för hands version: `English`

| Under typs namn    | Exempel                     |
|-------------|------------------------------|
| Tal         | `6`, `six`                   |
| Procent  | `50%`, `fifty percent`       |
| Numret     | `2nd`, `second`              |
| Ålder         | `90 day old`, `30 years old` |
| Valuta    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Temperatur | `32 degrees`, `10°C`         |
