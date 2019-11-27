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
ms.openlocfilehash: a4c911fa077c2ec332974fb2f0c5abcdef21307c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284168"
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

Telefonnummer (endast telefonnummer till USA). 

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                  |
|----------|----------------------------------------------|
| Gäller inte         | AMERIKANSKt telefonnummer, till exempel `(312) 555-0176`. |

### <a name="email"></a>E-post

E-postadress. 

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                  |
|----------|----------------------------------------------|
| Gäller inte         | E-postadress, till exempel `support@contoso.com` |

### <a name="url"></a>URL

Internet-URL: er.

Språk:


* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                           |
|----------|-------------------------------------------------------|
| Gäller inte         | URL: er till webbplatser, till exempel `https://www.bing.com`. |

###  <a name="number"></a>Antal

Siffror och numeriska kvantiteter. 

Språk:


* Offentlig för hands version: `English`

| Under typs namn    | Exempel                     |
|-------------|------------------------------|
| Gäller inte         | `6`, `six`                   |
| Procent  | `50%`, `fifty percent`       |
| Ordningstal     | `2nd`, `second`              |
| Currency    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Temperatur | `32 degrees`, `10°C`         |
