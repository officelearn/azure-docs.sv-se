---
title: Allmänna namngivna entiteter
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: 693a81cfb15407541311d7ab053bb2ab6a267b29
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800167"
---
## <a name="general-entity-types"></a>Allmänna enhets typer:

### <a name="person"></a>Person
Identifierade namn och andra personer i text.
Språk:
* Offentlig för hands version: `English`

| Under typs namn | Beskrivning             |
|--------------|-------------------------|
| Saknas          | Identifierade namn, till exempel `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Plats

Naturliga och mänskliga landmärkes-, struktur-och geografiska funktioner.

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| Saknas          | platser, till exempel `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty` |

### <a name="organization"></a>Organisation  

Erkända organisationer, organisationer, organ och andra grupper av personer. Exempel: företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer. Nationella objekt och religions ingår inte i den här entitetstypen. Språk: 

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| Saknas          | organisationer, till exempel `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Telefonnummer

Telefonnummer. 

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                  |
|----------|----------------------------------------------|
| Saknas         | Telefonnummer, till exempel `+1 123-123-123`. |

### <a name="email"></a>E-post

E-postadress. 

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                  |
|----------|----------------------------------------------|
| Saknas         | E-postadress, till exempel `support@contoso.com` |

### <a name="url"></a>URL

Internet-URL: er.

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                           |
|----------|-------------------------------------------------------|
| Saknas         | URL: er till webbplatser, till exempel `https://www.bing.com`. |

###  <a name="number"></a>Tal

Siffror och numeriska kvantiteter. 

Språk:


* Offentlig för hands version: `English`

| Under typs namn    | Exempel                     |
|-------------|------------------------------|
| Saknas         | `6`, `six`                   |
| Procent  | `50%`, `fifty percent`       |
| Ordningstal     | `2nd`, `second`              |
| Valuta    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Temperatur | `32 degrees`, `10°C`         |
