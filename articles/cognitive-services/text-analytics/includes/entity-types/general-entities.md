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
ms.openlocfilehash: e81a1de02c112abd5c52f0f83404a615d738e01c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508071"
---
## <a name="general-entity-types"></a>Allmänna enhets typer:

### <a name="person"></a>Person
Identifierade namn och andra personer i text.
Språk:
* Offentlig för hands version: `English`

| Under typs namn | Beskrivning             |
|--------------|-------------------------|
| Gäller inte          | Identifierade namn, till exempel `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Plats

Naturliga och mänskliga landmärkes-, struktur-och geografiska funktioner.

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| Gäller inte          | platser, till exempel `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty` |

### <a name="organization"></a>Organisation  

Erkända organisationer, organisationer, organ och andra grupper av personer. Exempel: företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer. Nationella objekt och religions ingår inte i den här entitetstypen. Språk: 

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| Gäller inte          | organisationer, till exempel `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Telefonnummer

Telefonnummer. 

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                  |
|----------|----------------------------------------------|
| Gäller inte         | Telefonnummer, till exempel `+1 123-123-123`. |

### <a name="url"></a>URL

Internet-URL: er.

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                           |
|----------|-------------------------------------------------------|
| Gäller inte         | URL: er till webbplatser, till exempel `https://www.bing.com`. |

###  <a name="number"></a>Tal

Siffror och numeriska kvantiteter. 

Språk:


* Offentlig för hands version: `English`

| Under typs namn    | Exempel                     |
|-------------|------------------------------|
| Gäller inte         | `6`, `six`                   |
| Procent  | `50%`, `fifty percent`       |
| Ordningstal     | `2nd`, `second`              |
| Valuta    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Temperatur | `32 degrees`, `10°C`         |
