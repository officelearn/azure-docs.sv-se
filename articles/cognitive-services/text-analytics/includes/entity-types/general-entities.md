---
title: Allmänna namngivna entiteter
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 630f04bf2cc9e7de6331f9d25754a20fe2327d76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779869"
---
Följande enhets kategorier returneras när begär Anden skickas till `/entities/recognition/general` slut punkten.

| Kategori   | Underkategori | Beskrivning                          | Startar modell version                                                    | Obs! |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Person     | E.t.         | Namn på personer.  | `2019-10-01`  | Returneras även av NER v 2.1 |
| PersonType | E.t.         | Jobb typer eller roller som innehas av en person. | `2020-02-01` | |
|Plats    | E.t.         | Naturliga och mänskliga landmärkeer, strukturer, geografiska egenskaper och geografiskt politiska enheter     |  `2019-10-01` | Returneras även av NER v 2.1 |
|Plats     | GPE (politisk entitet)        | Städer, länder/regioner, stater.      | `2020-02-01` | |
|Plats     | Rörande                       | Konstgjorda-strukturer. | `2020-04-01` | |
|Plats     | Skyddade       | Geografiska och naturliga funktioner som floder, hav och Deserts. |  `2020-04-01` | |
|Organisation  | E.t. | Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer.  | `2019-10-01` | Nationella objekt och religions ingår inte i den här entitetstypen. Returneras även av NER v 2.1 |
|Organisation | Sjukdom | Medicinska företag och grupper. | `2020-04-01` |  |
|Organisation | Börs kurs | Fond börs grupper. | `2020-04-01` | |
| Organisation | Sport | Idrotts relaterade organisationer. | `2020-04-01` |  |
| Händelse  | E.t. | Historiska, sociala och naturligt förekommande händelser. | `2020-02-01` |  |
| Händelse  | Kulturella | Kulturella händelser och helgdagar. | `2020-04-01` | |
| Händelse  | Fysiska | Naturligt förekommande händelser. | `2020-04-01` |  |
| Händelse  | Sport | Idrotts evenemang.  | `2020-04-01` | |
| Produkt | E.t. | Fysiska objekt av olika kategorier. | `2020-02-01` | |
| Produkt | Dator produkter | Dator produkter. |  `2020-02-01 ` | |
| Kvalifikation | E.t. | En funktion, kunskap eller expertis. | `2020-02-01` |  |
| Adress | E.t. | Fullständiga post adresser.  | `2020-04-01` |  |
| PhoneNumber | E.t. | Telefonnummer (endast USA och EU-telefonnummer). | `2019-10-01` | Returneras även av NER v 2.1 |
| E-post | E.t. | E-postadresser. | `2019-10-01` | Returneras även av NER v 2.1 |
| URL | E.t. | URL: er till webbplatser. | `2019-10-01` | Returneras även av NER v 2.1  |
| IP-adress | E.t. | Nätverks-IP-adresser. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | E.t. | Datum och tidpunkter på dagen. | `2019-10-01` | Returneras även av NER v 2.1 | 
| DateTime | Datum | Kalender datum. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | Tid | Tider på dagen | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | DateRange | Datum intervall. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | TimeRange | Tidsintervall. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | Varaktighet | Varaktigheter. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | Ange | Ange, upprepade gånger. |  `2019-10-01` | Returneras även av NER v 2.1 |
| Kvantitet | E.t. | Siffror och numeriska kvantiteter. | `2019-10-01` | Returneras även av NER v 2.1  |
| Quantity | Tal | Nummer. | `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Procentandel | Procenttal.| `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Ordningstal | Ordnings tal. | `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Ålder | Personer. | `2019-10-01` |  Returneras även av NER v 2.1 |
| Quantity | Valuta | Valutor. | `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Dimension | Dimensioner och mått. | `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Temperatur | Temperaturer. | `2019-10-01` | Returneras även av NER v 2.1 |
