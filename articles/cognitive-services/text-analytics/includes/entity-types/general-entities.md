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
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779869"
---
Följande enhets kategorier returneras när begär Anden skickas till `/entities/recognition/general` slut punkten.

| Kategori   | Underkategori | Beskrivning                          | Startar modell version                                                    | Kommentarer |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Person     | Ej tillämpligt         | Namn på personer.  | `2019-10-01`  | Returneras även av NER v 2.1 |
| PersonType | Ej tillämpligt         | Jobb typer eller roller som innehas av en person. | `2020-02-01` | |
|Location    | Ej tillämpligt         | Naturliga och mänskliga landmärkeer, strukturer, geografiska egenskaper och geografiskt politiska enheter     |  `2019-10-01` | Returneras även av NER v 2.1 |
|Location     | GPE (politisk entitet)        | Städer, länder/regioner, stater.      | `2020-02-01` | |
|Location     | Rörande                       | Konstgjorda-strukturer. | `2020-04-01` | |
|Location     | Skyddade       | Geografiska och naturliga funktioner som floder, hav och Deserts. |  `2020-04-01` | |
|Organisation  | Ej tillämpligt | Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer.  | `2019-10-01` | Nationella objekt och religions ingår inte i den här entitetstypen. Returneras även av NER v 2.1 |
|Organisation | Sjukdom | Medicinska företag och grupper. | `2020-04-01` |  |
|Organisation | Börs kurs | Fond börs grupper. | `2020-04-01` | |
| Organisation | Sport | Idrotts relaterade organisationer. | `2020-04-01` |  |
| Händelse  | Ej tillämpligt | Historiska, sociala och naturligt förekommande händelser. | `2020-02-01` |  |
| Händelse  | Kulturella | Kulturella händelser och helgdagar. | `2020-04-01` | |
| Händelse  | Fysiska | Naturligt förekommande händelser. | `2020-04-01` |  |
| Händelse  | Sport | Idrotts evenemang.  | `2020-04-01` | |
| Produkt | Ej tillämpligt | Fysiska objekt av olika kategorier. | `2020-02-01` | |
| Produkt | Dator produkter | Dator produkter. |  `2020-02-01 ` | |
| Kvalifikation | Ej tillämpligt | En funktion, kunskap eller expertis. | `2020-02-01` |  |
| Adress | Ej tillämpligt | Fullständiga post adresser.  | `2020-04-01` |  |
| PhoneNumber | Ej tillämpligt | Telefonnummer (endast USA och EU-telefonnummer). | `2019-10-01` | Returneras även av NER v 2.1 |
| E-post | Ej tillämpligt | E-postadresser. | `2019-10-01` | Returneras även av NER v 2.1 |
| URL | Ej tillämpligt | URL: er till webbplatser. | `2019-10-01` | Returneras även av NER v 2.1  |
| IP-adress | Ej tillämpligt | Nätverks-IP-adresser. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | Ej tillämpligt | Datum och tidpunkter på dagen. | `2019-10-01` | Returneras även av NER v 2.1 | 
| DateTime | Datum | Kalender datum. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | Tid | Tider på dagen | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | DateRange | Datum intervall. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | TimeRange | Tidsintervall. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | Varaktighet | Varaktigheter. | `2019-10-01` | Returneras även av NER v 2.1 |
| DateTime | Ange | Ange, upprepade gånger. |  `2019-10-01` | Returneras även av NER v 2.1 |
| Kvantitet | Ej tillämpligt | Siffror och numeriska kvantiteter. | `2019-10-01` | Returneras även av NER v 2.1  |
| Quantity | Tal | Nummer. | `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Procentandel | Procenttal.| `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Ordningstal | Ordnings tal. | `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Ålder | Personer. | `2019-10-01` |  Returneras även av NER v 2.1 |
| Quantity | Valuta | Valutor. | `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Dimension | Dimensioner och mått. | `2019-10-01` | Returneras även av NER v 2.1 |
| Quantity | Temperatur | Temperaturer. | `2019-10-01` | Returneras även av NER v 2.1 |
